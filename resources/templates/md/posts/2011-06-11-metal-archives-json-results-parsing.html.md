{:title "Metal Archives' JSON Results Parsing"
:layout :post
:tags ["metaldetectr" "rails" "ruby"]}

Some further explanation of how to get Metal Archives' JSON data from a [recent post](/blog/2011/05/23/scraping-a-site-when-it-changes-its-design)
is necessary. Through reading the markup and trial and error, I found the URL to receive the data
I needed.

```
http://www.metal-archives.com/search/ajax-advanced/searching/albums \
/?&releaseYearFrom=2011&releaseMonthFrom=1&releaseYearTo=2011 \
&releaseMonthTo=12&_=1&sEcho=0&iColumns=4&sColumns=&iDisplayStart=1& \
iDisplayLength=100&sNames=%2C%2C%2C
```

This returns a result set that looks like this:

```
{
 "error": "",
 "iTotalRecords": 3637,
 "iTotalDisplayRecords": 3637,
 "sEcho": 0,
 "aaData": [
   [
    "<a href=\"http://www.metal-archives.com/bands/037/3540277845\" title=\"037 (ES)\">037</a>",
    "<a href=\"http://www.metal-archives.com/albums/037/Los_Fuertes_Sobreviven/307703\">Los Fuertes Sobreviven</a>",
    "Full-length",
    "May 24th, 2011 <!-- 2011-05-24 -->"
  ],
  [
    "<a href=\"http://www.metal-archives.com/bands/037/3540277845\" title=\"037 (ES)\">037</a>",
    "<a href=\"http://www.metal-archives.com/albums/037/Tantas_Vidas/306172\">Tantas Vidas</a>",
    "Single",
    "May 6th, 2011 <!-- 2011-05-06 -->"
  ]
}
```

You'll notice the `iTotalRecords` field which conveniently provides the total amount to releases
available. You'll also notice the the `iDisplayStart` parameter in the URL that lets us step
through the results 100 at a time. By looping through `(iTotalRecords / 100 + 1)` times,
incrementing `iDispalyStart` by `i * 100`, we can get a result set for all the records very
quickly.

Now that we have the results, we just need a little regular expression magic to pull out all the
information.

```ruby
BAND_NAME_AND_COUNTRY_REGEXP = /(.+)\s{1}\(([a-zA-Z]{2})\)/
ALBUM_URL_AND_NAME_REGEXP = /"(.+)">(.+)<\/a>/
RELEASE_DATE_REGEXP = /<!--\s(.{10})\s-->/
```

There was a strange situation where an album didn't have a band page but displayed a message that
the band didn't exist, so there is one last regular expression used to guard against a slightly
alternative format for the data:

```ruby
NO_BAND_REGEXP = /span.+<\/span/
```

The data are much easier to gather and never time out now, so I was able to get rid of all the
intermediate saving steps such as after gathering the paginated links and saving the last release
searched when the site times out. I'll probably have to add it back in to get the record label of
the release since you'll notice it's absent in the JSON but it is available on the release's page.

The code to save the albums now looks like this:

```ruby
agent = ::MetalArchives::Agent.new
agent.paginated_albums.each_with_index do |album_page, index|
  album_page.each do |album|
    if album[0].match(::MetalArchives::Agent::NO_BAND_REGEXP).nil?
      Release.create(
        :name => agent.album_name(album),
        :band => agent.band_name(album),
        :format => agent.release_type(album),
        :url => agent.album_url(album),
        :country => agent.country(album),
        :us_date => agent.release_date(album)
      )
    end
    CompletedStep.find_or_create_by_step(CompletedStep::ReleasesCollected)
  end
end
```

Quick and simple.
