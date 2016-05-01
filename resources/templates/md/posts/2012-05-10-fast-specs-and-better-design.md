{:title "Fast specs and better design"
:layout :post
:tags ["testing"]}

[Corey Haines][1] first brought testing parts of Rails applications without loading Rails to my
attention and excitement a few months ago, and I've been looking at how other developers do
similar techniques. Having recently finished [*Objects on Rails*][2] and working through the
[Destroy All Software][3] videos, I've been constantly looking at ways to make my Rails code
better and faster to test. I believe these are related and that there is a lot of value in working
to make the code easy to test and fast.

An example of some code I've written that doesn't rely on Rails is an [observer][4]. The observer
inherits from `ActiveRecord::Observer` and would normally need Rails loaded to exercise its full
functionality with the model it's observing. But if we try to keep Rails out and only load what we
need, we can drive the design of the observer.

Here we save an activity, which is used as a generic Facebook-style activity stream item, after
someone answers a question.

```ruby
class AnswerObserver < ActiveRecord::Observer
  def after_create(answer)
    Activity.question_answered(answer)
  end
end
```

Here's the entire spec file.

```ruby
require 'fast_spec/spec_helper'
stub_class 'ActiveRecord::Observer'
require 'app/models/observers/answer_observer'

class Activity; end

describe AnswerObserver do
  let(:observer) { AnswerObserver.new }

  describe "#after_create" do
    let(:answer) { stub }

    it "creates an activity" do
      Activity.should_receive(:question_answered).with(answer)
      observer.after_create(answer)
    end
  end
end
```

We do a few things here. The first is loading `fast_spec/spec_helper.rb` instead of the regular
`spec_helper.rb` that loads the Rails environment. The fast spec helper requires a few utility
files like `awesome_print` and `logger`, and a `module_stub` file that has a convenience method
stolen from Avdi Grimm:

```ruby
def stub_module(full_name)
  full_name.to_s.split(/::/).inject(Object) do |context, name|
    begin
      context.const_get(name)
    rescue NameError
      context.const_set(name, Module.new)
    end
  end
end
```

To quote Avdi:

> This method uses `#const_get` to attempt to reference the given module. If the module is
> defined, or if calling `#const_get` causes it to be auto-loaded, the method does nothing more. But
> if `#const_get` fails to turn up the module, it defines an anonymous empty module to act as a
> placeholder.

Looking back at the spec file, we see a similar call to `stub_class` which does the same thing for
classes. We also include the actual file of the code we're testing.

We create an empty `Activity` class, normally an `ActiveRecord` model, since we don't care what
it's doing, just that it receives messages we're going to send to it. That's all this test does,
really: we check that the Activity class gets a specific message passed to it with specific
parameters.

Testing this way drives the code to be [loosely coupled][5] with objects this class needs to
communicate with. We could have put all the activity action in the `after_create` callback and
we'd be able to see what's going on, but we'd need to mock and stub a lot of interactions, or we'd
have to go back to loading Rails, slowing our tests down. Plus we'd have a lot of code that has
[feature envy][6] and would be difficult to maintain.

We know the test works because changing the functionality of the callback will break the test, and doing BDD will ensure that we're writing failing tests before we add functionality that could be skipped by our mocks. Of course, this problem exists with slower, more integration-like tests, so it's not like running through the full stack is a silver bullet.

There is a point where there can be too much mocking and stubbing, and I'll get to that in another post. For now, being able to run an entire suite of tests in less than half a second helps keep me in [flow][7] to write better software.

[1]: http://confreaks.com/videos/641-gogaruco2011-fast-rails-tests
[2]: http://devblog.avdi.org/2011/11/15/early-access-beta-of-objects-on-rails-now-available-2/
[3]: https://www.destroyallsoftware.com/screencasts
[4]: https://github.com/rails/rails-observers
[5]: http://en.wikipedia.org/wiki/Coupling_(computer_science)
[6]: http://c2.com/cgi/wiki?FeatureEnvySmell
[7]: http://en.wikipedia.org/wiki/Flow_(psychology)
