{:title "My name is Danny...and I make mistakes"
:layout :post
:tags ["learning"]}

Yes, it's true. Here's what I did, and here's the reaction.

At work a few weeks ago, I was going through some callback code that sets some meta data on a
model's associations. I noticed that a related flag wasn't getting set as well, that it was only
set in one other specific instance with the meta data. Hmm…let's fix that, shall we? Flag added,
moving on.

Star wipe to this week.

We found a bug when displaying historical data, and I quickly realized that the display was wrong
because it was skipping over objects it shouldn't, objects that were flagged when they shouldn't
be. Cue pants pooping.

There was a fix, and I would just need to run a script that would update the flag for all the
associated models affected after the callback happens and ignore the other ones because those were
the ones that explicitly have the flag set at the other, correct, time. But should I tell anyone
or do I just run the script and say that I fixed the display bug? Well, WWJD (what would Jack
Nicholson do)? He'd tell everyone, damn it, because he's like that. Keeping it real. Not like
Chuck Norris.

Anyway, I sent out an email admitting what happened, and I included a high-level explanation of
what happened along with a technical one. I explained that there is a fix and we won't lose any
data, and that it would fix the current display bug but that we need to do some tests to make
sure it didn't affect any other parts of the application.

My manager's response? "Hey, man, shit happens. Glad you fixed it." That's why I like working
here.
