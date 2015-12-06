{:title "The Right Time For Structure - Retrofitting Backbone.js to a jQuery Application"
:layout :post
:tags ["backbone" "javascript" "jquery"]}

It started innocently enough. We had a story to make a few UI changes to a small JavaScript app.
There would be no functionality changes, so it was going to be an easy morning. Maybe even an
early lunch? When we fired up Vim we saw that lunch was going to be a bit later than expected and
that we were having...[spaghetti][1].

Let's break it down, starting with the symptoms:

* 0 lines in the repository's README
* 1 JavaScript file
* 16 global functions
* 15 tests

This application was created using a "wouldn't it be cool if..." design process. Since this would
be a one-off application that would only get a little internal use, if any at all beyond the
initial excitement, it made sense to get something out, quick and dirty, and move on to more
valuable work. Given the wholly experimental nature (read: risky), this seemed reasonable.

It turns out that we created a popular tool and it wasn't going away anytime soon. And now we had
a dilemma: Do we make the quick code change and worry about [maintainability later][2], or do we
[clean up the code now][3], spending more time than anyone planned for this feature?

Would you keep reading this post if we wrote the feature, leaving a trail of shattered dreams
behind us?

We use [backbone.js][4] for most of our JavaScript applications, so it was the logical choice to
organize this application. It turned out that the tests were not very helpful since they were so
coupled to the implementation of the global functions, so out they went. One even questions the
utility of testing during raw experimentation (is testing code you're likely to shortly toss be
considered a form of [waste][5] in the Lean sense)?

We also decided not to practice TDD since we were essentially backfilling tests and it's [not
really TDD anymore][6].

## Let the Retrofitting Begin

1. Find the next function that's called
1. Move it to the appropriate backbone view or model without changing the internals of the
  function
1. Manually test the application in the browser
1. Clean up the code we just moved (extracting functions, moving DOM selectors, event handlers,
  and callbacks)
1. Manually test the application in the browser again
1. Repeat the above steps until there are no more global functions

We made slow and steady progress using the above process. If a change broke existing functionality, we'd revert the change and make another attempt. Using small iterations and only changing one thing at a time, we made progress and soon ended up with a much cleaner codebase.

Now our code is in a much better place for adding functionality. Before it would have been too difficult to add tests because they would be so brittle, but now our code is much easier to test since it's properly set up with appropriate models and views.

As a bonus, working through each function revealed a lot of dead code that we were able to get rid of. There are few things better than deleting code.

As evidence of our total victory, `application.js` is now this:

```javascript
$(function() {
  App.init();
});
```

## Take-aways

* **We must always be professionals**: Assume any code you're paid to write is going to get used.
  As a professional and a craftsman, we strive to create value with our work. We must use the best
  tools for the job and write software to the best of our abilities.
* **Speed at the cost of maintainability isn't true speed**: Working so quickly created a lot of
  technical debt that we eventually had to pay back. When we did, our initial development time
  slowed to a crawl. A one hour change turned into four days of work.
* **Write code for the benefit of the next person**: Our code should always be written so it's
  easy to change since we're always getting new feature requests. Someone will have to write these
  new features, and that someone needs to be able to understand the code quickly or spend valuable
  time getting up to speed. That person could easily be the same person who wrote the code (we've
  all experienced running git blame and seeing a familiar name listed). Remove code that's no
  longer used, document the code properly, and use proper naming.

[1]: http://dundundun.net/
[2]: http://en.wikipedia.org/wiki/Technical_debt
[3]: http://programmer.97things.oreilly.com/wiki/index.php/The_Boy_Scout_Rule
[4]: http://backbonejs.org/
[5]: https://en.wikipedia.org/wiki/Toyota_Production_System
[6]: http://www.amazon.com/dp/0131177052
