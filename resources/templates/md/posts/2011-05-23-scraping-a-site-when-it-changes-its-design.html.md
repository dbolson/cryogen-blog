{:title "Scraping a Site When It Changes Its Design"
:layout :post
:tags ["metaldetectr"]}

[MetalDetectr][1] has hit a snag. The site I'm scraping for all the release information,
[Metal Archives][2], recently changed their entire user interface, rendering the current
functionality of my screen scraping gem useless. What shall I do?

I started looking into their new UI, and they use [jQuery Datatables plugin][3] to display the
list of albums through ajax calls. A little Firebug and I can read the JSON that the plugin uses
to populate the tables.

Now that I can access all the data, I will just need to rewrite the metal-archives gem to grab
JSON for every paginated list of a result set, and I have all the information again! Check back
for updates to the application.

[1]: /posts/2011-05-14-introducing-metaldetectr.html.html
[2]: http://www.metal-archives.com/
[3]: http://www.datatables.net/
