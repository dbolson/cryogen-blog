{:title "Creating a presenter in Rails"
:layout :post
:tags ["rails" "ruby"]}

There are a [lot][1] of [presenter][2] [pattern][3] [implementations][4] out there, but I wanted
to roll my own because I only needed one class and it's fun.

It needs to have `content_tag()` and named routes available, so it looks like the following:

```ruby
class FooPresenter
  include Rails.application.routes.url_helpers

  def initialize(context)
    @context = context
  end

  def bar(arg=nil)
    if arg
      context.content_tag(:a, 'hey there', href: foo_path)
    else
      'Nothing to see here'
    end
  end

  private

  attr_accessor :context
end
```

Then, in the controller:

```ruby
@foo = FooPresenter.new(view_context)
```

And in the view:

```ruby
@foo.bar('yup')
```

Which creates a link to `foo_path`.

### What's going on?

We need to include `url_helpers()` to get the named routes to access `foo_path()`.

We also need to instantiate the presenter with the [`view_context`][5] from the controller, which
is

> An instance of a view class. The default view class is `ActionView::Base`
> The view class must have the following methods: `View.new[lookup_context, assigns, controller]`

This gives us access to helper methods like `content_tag()`.

### Isn't this still overkill?

Many have [discussed][6] this before, so I'll just reiterate that OOP is nicer than procedural
code, so there.

[1]: https://github.com/drapergem/draper
[2]: https://github.com/ihoka/viewtastic
[3]: https://github.com/jamesgolick/active_presenter
[4]: https://github.com/apotonick/cells
[5]: http://apidock.com/rails/AbstractController/Rendering/view_context
[6]: http://blog.steveklabnik.com/posts/2011-09-09-better-ruby-presenters
