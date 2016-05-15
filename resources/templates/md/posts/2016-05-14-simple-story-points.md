{:title "Simple Story Points"
:layout :post
:tags ["productivity" "teams" "work"]}

We use [story points][storypoints] in agile practices to predict the future. They measure effort and not hours, although in my experience they tend to be strongly correlated. Their supposed value comes for determining when software will be delivered and provide actual business value. What company wouldn't want that?

Of course, [estimation is hard][estimation], especially for something we've never done before. Which is much of the software we write. It takes months of data to build up a model of any accuracy, and there are many confounding factors that can prevent stability, and therefore, predictability. Bugs, changing requirements, fixing technical debt, and unknown unknowns are unexpected and affect the team's velocity.

### Another Way

Once a team has been creating story points for a few months and understands how to break down stories, we can attempt a simpler method. This is inspired, if not pilfered, from [NoEstimates][noestimates] (you can probably guess what that implies) and [kanban][kanban]. The idea is that the higher the complexity of a story, the less accurate the estimate, so let's reduce the complexity for each story. We need to change our concept of a deliverable unit of business value, as those are highly variable: a story may involve only a single line change in a CSS file or may involve setting up a new database, data model, business logic, and user interface. If "business value" means "user facing change" then the latter example is one story and needs to be estimated as such. But if we break it down into much smaller units of deliverable work, each of which is more easily understood, we have a better chance of being more accurate in our estimates.

What does this look like?

Say we have a single story to add a new field to an existing form, ensure the input is valid, show an error message if it's not, and send a success email if it is. There are a few tasks:

* Add a database migration
* Add data model validation
* Add new HTML markup
* Add logic to handle failure
* Send an email on success

After the engineers get together for planning poker, they decide it's worth two story points. An engineer picks up the story and finds out that the validation code is more complex than previously thought and will need to spend some time to refactor the code. After deploying to a staging server, the acceptance test results in some changes to the styling of the form and a bug in the validation logic. The engineer makes these changes, merges, deploys, and the story is eventually accepted.

What if we had split up the deliverables to be a each task instead? In this case, we could deliver the new HTML first and hide it behind a feature flag. We get the feedback about the styling and can make the changes quickly and get that story accepted. We then move on to the rest of the tasks, delivering each in term. When we discover the need to refactor, we already know we've delivered the rest of the tasks and that, even if this story goes long, we've accurately estimated, and delivered, the other ones. This is reflected in four tasks delivered as estimated instead of a two point story that should probably have been at least a three pointer.

### How to Estimate

Since everything is a one point story, we can calculate the velocity by simply adding up all delivered stories. And since all stories are broken down into one pointers, we can estimate how much we can deliver in the future by the average of how many stories are delivered each week, just as we were doing before. If we continually deliver 4 - 6 stories an iteration, we'll probably continue doing so.

### How to Accept Everything

How do we accept a story that has no UI and is only a database change? If the product owner is technical enough, they can still accept these types of stories. If not, another engineer can accept these and wait for the product owner to accept the user facing stories downstream. We just need to shift our definition of business value to ensure we can create units of work that are small enough.

### Benefits

* Faster, more consistent delivery cadence which will push us to use more [continuous delivery](http://martinfowler.com/bliki/ContinuousDelivery.html).
* Faster feedback loop so stories don't languish while going back and forth between development and QA.
* It is easier to reason about smaller stories, and we can build up context on the completion of each piece.
* We can potentially work on more stories in parallel which lets each engineer touch more parts of the entire codebase.

### Drawbacks

* The team needs to understand how to break down stories and what a one pointer looks like.
* Some stories will be blocked on others so we need to work on some in serial, not parallel.
* This flow requires more infrastructure to prevent blockages at various stages of delivery.
* We need to get acceptance feedback quickly so we don't end up with a huge list of stories that require acceptance.

Give this a try if you find your team unhappy with the current way of estimating.

[storypoints]: https://agilefaq.wordpress.com/2007/11/13/what-is-a-story-point/
[estimation]: https://blog.8thlight.com/uncle-bob/2012/04/20/Why-Is-Estimating-So-Hard.html
[noestimates]: http://softwaredevelopmenttoday.com/noestimates/
[kanban]: https://en.wikipedia.org/wiki/Kanban
