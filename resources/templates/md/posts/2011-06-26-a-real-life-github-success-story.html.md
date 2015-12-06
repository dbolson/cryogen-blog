{:title "A Real Life GitHub Success Story"
:layout :post
:tags ["metaldetectr" "open source" "ruby" "testing"]}

[GitHub](https://github.com/) has been a real treasure for developers, and I've used it both at
work and for [personal projects](https://github.com/dbolson). Until now, I haven't used it to its
full effect, that is, contributing.

For [MetalDetectr](/blog/2011/05/introducing-metaldetectr), I wanted to allow a user to filter the
list to see releases from artists he had in his [last.fm](http://www.last.fm/) library. A quick
search led me to [this gem](https://github.com/youpy/ruby-lastfm), only it wasn't as
fully-featured as I needed.

So I forked it.

Github made this really easy to do. Soon I had the repository in my account, cloned it locally,
checked out a new branch, and I was working.

The code was clean and certainly made my life easier to get what I wanted. There was a
`/method_categories` folder that contained the methods to do API calls to get or create
information for artists, tracks, and users. I wanted to read in a user's library of artists, so I
simply modeled this after the other files.

```ruby
class Lastfm
  module MethodCategory
    class Library < Base
      regular_method :get_artists, [:user], [[:limit, nil], [:page, nil]] do |response|
        response.xml['artists']['artist']
      end
    end
  end
end
```

This created a get request call for a last.fm user, set an optional limit on the number of fetched
results, and set an optional page number to scan to. Along with the API key, these fields are
outlined in the [last.fm api docs](http://www.last.fm/api/show/library.getArtists). Testing worked
similarly. A spec file contained the other method tests, so adding the following, plus a fixture
of the xml response, was super easy.

```ruby
describe '#library' do
  it 'should return an instance of Lastfm::Library' do
    @lastfm.library.should be_an_instance_of(Lastfm::MethodCategory::Library)
  end

  describe '#get_artists' do
    it 'should get the artists\' info' do
      @lastfm.should_receive(:request).with('library.getArtists', {
        :user => 'test',
        :limit => nil,
        :page => nil
      }).and_return(make_response('library_get_artists'))
      artists = @lastfm.library.get_artists('test')
      artists[1]['name'].should eql('Dark Castle')
      artists.size.should == 2
    end
  end
end
```

After adding these methods, I pushed the branch to my github repository and sent a pull request to
the original repository. Again, github makes this trivially easy. Before it was accepted, I had
this line in the Metaldetectr Gemfile:

```ruby
gem 'lastfm', :git => 'git://github.com/dbolson/ruby-lastfm.git', :branch => 'library_get_artists'
```

With the pull request accepted and my code merged into the master branch, it looked like this:

```ruby
gem 'lastfm'
```

That's all it took to contribute to open source software.
