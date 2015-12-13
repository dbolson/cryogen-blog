{:title "Our Interview Process"
:layout :post
:tags ["interviewing" "pair programming"]}

At Crowdcast, we're currently hiring developers, and through much practice we have come up with an
interview process that maximizes our chances of finding a candidate who will be a good fit.

## Our Hiring Process

There are a few steps that aren't always in the same order, and we may even skip some depending on
the candidate.

## The phone screen

I'm not involved in this first step. The engineering manager will talk to a candidate initially to
find out if there are any immediate red flags and to clarify his experience. If nothing strange
happens (believe me, there have been strange happenings), we'll schedule the candidate to come in
or give him a preliminary coding test.

## The rails test

We send the client a small Rails coding project that should only take a few hours. We'd like him
to create a Rails application with some basic functionality and minimal markup and styling (design
is a bonus but not a requirement), put the project on his Github account, and send us a url to the
code. This is a [Fizzbuzz](http://blog.codinghorror.com/why-cant-programmers-program/)-style
question for the Rails framework, and it also implies that he has a Github account. You do
[have one](https://github.com/dbolson), right? If the candidate can already show us Rails code, we
may skip this step and just bring him in to the office.

## The onsite interview

We then bring the candidate in for a few interview rounds. I'll ask a candidate about previous
experience and what he enjoys working on to establish a little history and familiarity and to
decode what's written on his resume. I'll next go through a technical screening about Ruby, some
basic questions and some slightly-less basic questions, and I'll let the candidate explain as much
or little as he'd like. A good one will definitely have a lot of say for some of the answers, and
a short reply is usually an indication of minimal experience with the subject matter.

Next, we'll get into some Rails-specific questions, both methods and techniques. It's certainly
not necessary to know everything about the framework, especially given its development speed, but
there are some core ideas that get used often that he should be familiar with. If the candidate
can teach me something, that makes me very happy.

## The pair programming exercise part I

A new wrinkle we've introduced. Since I'll be [pair programming](https://en.wikipedia.org/wiki/Pair_programming)
with someone, I'd like to know how he thinks and how we'll work together. We'll sit at one
computer and, with the candidate driving, work through a small Ruby problem. We'll do
[TDD](https://en.wikipedia.org/wiki/Test-driven_development) (we use [Rspec](https://www.relishapp.com/rspec)
but that's not a requirement). The problem should take 20 – 40 minutes and leaves a lot of room to
delve into interesting design issues and refactorings. This is the fun part.

## The pair programming exercise part II

The last step is to invite the candidate back to pair program on real code for a few hours. We
need to make sure we're a fit for each other, and this gives him an opportunity to engage our code
base and be part of the team. This is not just for me but for everyone in the office, and of
course the candidate, to assess the interactions.

## Post mortem

Does any interview process find the perfect candidate? Not that I've discovered. We can't go as
in-depth as [Hashrocket](https://gist.github.com/obie/652092), but we can get partially there with
the multiple pairing exercises and full team interaction. It's so incredibly important to get
along with coworkers since we spend as much time at work as sleeping (actually, it's probably more
time at work), so just having the technical chops is not enough. We hope that our process gives us
the knowledge to make the right decisions for everyone involved.
