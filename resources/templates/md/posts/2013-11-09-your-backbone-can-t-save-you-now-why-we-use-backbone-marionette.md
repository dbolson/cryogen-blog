{:title "Your Backbone Can't Save You Now - Why We Use Backbone Marionette"
:layout :post
:tags ["backbone" "javascript" "marionette"]}
---

The [big ball of mud][1] is a well understood software anti-pattern, and we've all experienced it at some point in our careers (sometimes on the receiving end but all too often as the creator). There is never a specific event where we can look back and say, "this is what caused the problems we're experiencing right now." It's cumulative, subtle, and it takes time to manifest.

But how does this happen? We're [agile][2]. We pair program for instant code reviews. We follow the [YAGNI][3] principle and only write the functionality we need. And yet, we can often end up with code that is difficult to understand, change, and test. New features take much longer to add because we don't understand the code we're working on and any change we make breaks another part of the code base.

### The situation

We have an input form to let a user create a new ad creative. It's sufficiently complicated because a creative has a lot of moving parts, some required, some optional, and the user needs to be able to edit any of those parts. There is the content of the creative, information about the targeting, and the budget information. We also want to show the user what the ad unit will look like as the user enters information. It ain't trivial.

We use [backbone.js][4] to organize our JavaScript. It gives us powerful ways to organize our code, but it doesn't provide everything we need in a framework. True, it's easy to shoot yourself in the foot with any code, but Backbone's lack of opinion makes it easier. The documentation says that it "is an attempt to discover the minimal set of data-structuring (models and collections) and user interface (views and URLs) primitives that are generally useful when building web applications with JavaScript." Developers have the freedom to use Backbone as they wish, but it's not a fully-featured framework.

It turns out that we have a bit too much freedom and now we have some major concerns in our code.

### Structure

Our back end code is written in Ruby on Rails. Rails has a specific file structure based on its role (eg, controllers, models, or views) and uses "magic" to autoload the files. This design decision makes sense for Rails, but it doesn't make sense in our situation. Take a look:

```
backbone/
  helpers/
  routers/
  views/
```

These folders don't give us a lot of information about the domain of what we're trying to model. It does tell us what types of files we have, but this is not very helpful.

### DOM spaghetti

Backbone views are the heart of an application. The documentation states, "The general idea is to organize your interface into logical views, backed by models, each of which can be updated independently when the model changes, without having to redraw the page." There is some structure, but it's easy to make views junk drawers that are organized haphazardly. A critique of jQuery is that it's easy to create a lot of selectors that are difficult to organize, and Backbone.js views can easily consist of this mess instead of breaking it up into organized units.

### Testing is hard

We test our JavaScript with [Jasmine][5], and the setup required for some of our tests is intimidating. There are tests that recreate a large part of the DOM in fixtures, use a lot of selectors to grab various bits, and then do logic on multiple pieces at once.

This coupling makes changing anything very difficult since the change can affect so many different parts. Difficult tests are no fun for anyone.

### Won't someone *do* something!

With all this, we were still putting out new features, although they were small and easily understood. We recently planned work for a much larger feature that would make our creatives much more powerful. After evaluating what this feature requires, we realized that it would be very difficult to add to the code base in its existing form. We decided we needed to give our code more structure.

Backbone Marionette: Our New Puppet Master

[Derick Bailey explains his reasoning for using Marionette][6], and it's compelling. Here are his reasons and what we think of them:

#### Composite application architecture

We get much more value out of our code when we [prefer composition over inheritance][7].

#### Enterprise messaging pattern influence

The [pub/sub][8] pattern gives us loose coupling, letting us create multiple smaller components instead of one large, monolithic application.

#### Modularization options

We don't use any existing [module][9] [libraries][10] so Marionette's module system works well for us.

#### Incremental use (no all-or-nothing requirement)

Since we've got an existing application, it makes more sense to add or change functionality in pieces instead of all at once.

#### No server lock-in

Our backend is Rails without any plans to change it, so this isn't too relevant except that it allows us to use our preferred back-end technology.

#### Easily change the defaults

All applications seem to need more than what a framework provides by default, so making this easy makes developers happy.

#### Code as configuration

This goes with the previous reason. The frameworks author's conventions probably overlap a lot with ours, but it won't be 100%.

We'll next discuss specific benefits we get from using Marionette.js on top of Backbone.js. Stay tuned!

[1]: http://en.wikipedia.org/wiki/Big_ball_of_mud
[2]: http://en.wikipedia.org/wiki/Agile_software_development
[3]: http://en.wikipedia.org/wiki/You_aren't_gonna_need_it
[4]: http://backbonejs.org/
[5]: http://jasmine.github.io/
[6]: http://lostechies.com/derickbailey/2012/06/13/why-should-i-use-backbone-marionette-instead-of-%E2%80%A6/
[7]: http://en.wikipedia.org/wiki/Composition_over_inheritance
[8]: http://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern
[9]: http://requirejs.org/docs/whyamd.html
[10]: http://wiki.commonjs.org/wiki/CommonJS
