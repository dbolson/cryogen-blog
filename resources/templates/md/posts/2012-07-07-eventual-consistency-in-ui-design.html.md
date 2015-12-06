{:title "Eventual Consistency In UI Design"
:layout :post
:tags ["user interface"]}

A [recent article][1] from Los Techies resonated with something I also did recently at work, and
it's nice to see others come up with similar ideas independently. [Hopefully][2] that means
they're good ideas.

"Eventual consistency in interaction design" presents the idea of giving the user immediate
feedback for an action that is not immediate. It combines a synchronous call (a message to a user
after he interacts with a page) with an asynchronous one (processing the results of that action in
a background task). The benefits are obvious: the user gets immediate feedback that something
happened, and he knows that, while it may not have happened right away, he's not stuck in spinner
hell, waiting for some sort of response.

When a user does certain actions on our site, we create an activity to put it in a feed to either
display or to send it off to a third party. We create the activity immediately and put it in a job
queue to do whatever functionality it requires later. The job queue is fast, but it can take a
minute or so before it processes the new activity.

That's a minute the user shouldn't need to wait for.

So when we schedule the job, we also save the related data, the data we're sending off to a third
party site, so the user sees the results immediately. To him, his action happened in real time and
he never notices the difference.

One obvious question is what if the user tries to do something on our site with the data that is
supposedly linked to another site because he assumes they've been synchronized? Since everything
that goes to the other site is done asynchronously, his other actions just get added to the job
queue after the first action. If he goes to the other site, the job is generally fast enough that
it will have processed the original activity (again, since most jobs run in under a minute). If it
becomes a problem where users are getting to the other site too quickly, we'll be able to come up
with language more akin to "we're processing your request." But this current implementation
provides a nice solution for now.

Like the article mentions, "If you have users that have to wait to have the view model updated to
see their results, you have built the wrong user interface." In our case, the user does not have
to wait for an update, even if that update hasn't happened yet.

[1]: http://lostechies.com/jimmybogard/2012/06/26/eventual-consistency-cqrs-and-interaction-design/
[2]: http://i.mobavatar.com/what-s-on-your-mind/great-minds-think-alike.jpg
