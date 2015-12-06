{:title "Namespacing lib files and RSpec"
:layout :post
:tags ["metaldetectr" "modularity" "rails" "testing"]}

I've been in an ongoing battle with RSpec to get it to properly load files in the `/lib` directory
of a rails app. There's a class `MetalArchivesFetcher` wrapped in a MetalDetectr module as a
namespace:

```ruby
module MetalDetectr
  class MetalArchivesFetcher
    ...
  end
end
```

The spec file starts like this:

```ruby
require 'spec_helper'
require 'metal_archives_fetcher'

describe MetalDetectr::MetalArchivesFetcher do
  ...
end
```

Without the require, I would receive the message

```ruby
load_missing_constant': Expected /Users/danny/code/metaldetectr/lib/metal_archives_fetcher.rb to define MetalArchivesFetcher (LoadError)
```

It felt a little off to need to require the file again because Rails already loads it in with

```ruby
# config/application.rb
config.autoload_paths += Dir["#{config.root}/lib/**/"]
```

I could put the require in `spec_helper.rb`, but it still felt strange.

I decided to remove the module namespace. That lets me remove the require line and all preceding
`MetalDetectr::` for every MetalArchivesFetcher call in the spec. Is this the right decision? It's
definitely [DRYer](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself), but I do create tighter
coupling. Jim Weirich's talk, ["The Building Blocks of Modularity"](http://mattgrande.com/2009/05/11/jim-weirich-on-connascence/)
(that I can't find online) does go over the trade-offs of writing code that is either more tightly
or loosely coupled, and my takeaway from that is, since this file is already coupled to the
application and models within it, why add an additional layer? It's more of a perceived loosening
while only adding a bit more complexity. And that's usually not a good thing.

Perhaps I'll add it back in later, but for now, [I'm not going to need it](https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it).
