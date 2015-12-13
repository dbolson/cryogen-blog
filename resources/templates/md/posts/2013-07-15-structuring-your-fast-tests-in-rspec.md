{:title "Structuring your fast tests in RSpec"
:layout :post
:tags ["ruby" "testing"]}

Not to be too hyperbolic, but `spec_fast` directory *will* increase the quality of all our lives
in addition to making our code better. You see, we don't have to load Rails for all our test
files. In fact, it's a dependency that we should try to stay away from as much as possible.

```
/spec_fast
  /contexts
  /decorators
  /models
  /lib
  /services
```

Sure, if we need to touch the database or we're testing the integration of multiple classes that
access the framework, we need it. But we don't need it for so many other things. What other things
you ask? How about most other things.

For example, we use [Draper][1] to decorate our objects with presentational data. Do we need Rails
to test these? Nope. You can see it in action below:

```ruby
# spec_fast/decorators/user_decorator.rb
require 'draper'

require_relative '../fast_spec_helper'
require_relative '../../app/decorators/user_decorator'

describe UserDecorator do
  let(:decorator) { UserDecorator }

  let(:user) { stub(:user, my_method: 'my method') }

  describe 'delegation' do
    it 'delegates all methods' do
      expect(decorator.decorate(user).my_method).to eq('my method')
    end
  end

  describe '#special_url' do
    it 'sets the URL to the special URL' do
      expect(decorator.decorate(user).special_url).to eq('you/are/special')
    end
  end
end

# app/decorators/user_decorator.rbo
class UserDecorator < Draper::Decorator
  delegate_all

  def special_url
    'you/are/special'
  end
end
```

You'll notice a few things:

* The `/spec_fast` directory is set up just like the `spec` directory where the folder structure
  mimics the structure in the application.
* The `fast_spec_helper` contains everything that all the fast tests need.
* The `/support` directory functions just like the `spec/support` directory.
* We use `require_relative` for each file we need to load, and we need to load all the files
  manually in the tests.
* The secret sauce is that there is no `require 'spec_helper'` in any of the tests, so there is no
  Rails! [Freedom][2]!
* Feel free to add to this directory as often as possible. In fact, you should probably default
  your tests here. Why? It reduces coupling with the framework and (should) lead to better, ie,
  [SOLID][3]) classes.

[1]: https://github.com/drapergem/draper
[2]: http://gal.darkervision.com/wp-content/uploads/2008/11/calvin-naked-resize.jpg
[3]: http://en.wikipedia.org/wiki/SOLID_(object-oriented_design
