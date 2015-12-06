{:title "Don't be lazy with route resources and allow unused ones"
:layout :post
:tags ["bugs" "rails" "ruby" "testing"]}

Refactoring controllers to make them [skinny][1] is usually a multi-step affair. The common
process is to get current functionality tested and them refactor the business logic where it needs
to go, moving the tests along with them to their corresponding test files. As the code is moved
(to models or service objects, etc.), we can stub out those calls in the controller. Eventually,
hopefully, the controller actions will be only a few assignments and a render call. Since the
effects can reach across the entire Rails architecture (the M, V, and C, if you will), I find
[smoke testing][2] often a necessary step in the process.

Which brings me to two sources of frustration: the default Rails (2.0) routes and default restful
routes.

```ruby
map.connect ':controller/:action/:id.:format'
```

This is just ripe for annoyance. Having a list of all the routes set up and not being able to find
the one that I need? This guys fault. Rails has a lot of magic, but this has become a major pain
because of the implicit and non-obvious nature of allowing anything one wants to sit next to a
specified list. But then again, that specific list can have problems of its own too.

```ruby
map.resources :photos
```

This can be fantastic. One line gives us so much power and ease: 7 actions for the price of one.
What's the problem? Well, when I was recently working on some [technical debt][3], I thought I
found some unused actions. But how did I know for sure? I had to do a lot of [grepping][4] and
manual testing to make sure these really weren't used. It turned out that one I thought wasn't was
actually called in one specific case, so I had to back out my changes.

This would have been less painful if the code coverage was better, but that's sometimes the nature
of old code, isn't it? If there are methods in the controller that aren't used, it's one thing to
not have them declared, but what happens when there is code for an action that goes unused? I
decided to be explicit about which routes to use so I didn't have to deal with the same problems
later on (I can be my own worst enemy sometimes).

Let's lock down those resources:

```ruby
map.resources :foobars, :only => [:index, :show, :new, :create]
```

If we're not using `:edit`, `:update`, and `:destroy`, let's not clutter things up with
implications.

Maybe it's overkill, but tech debt can be painful to pay back, and I don't want to piss off my
future self if this were to happen again.

[1]: http://weblog.jamisbuck.org/2006/10/18/skinny-controller-fat-model
[2]: http://en.wikipedia.org/wiki/Smoke_testing#Software_development
[3]: http://en.wikipedia.org/wiki/Technical_debt
[4]: http://en.wikipedia.org/wiki/Grep
