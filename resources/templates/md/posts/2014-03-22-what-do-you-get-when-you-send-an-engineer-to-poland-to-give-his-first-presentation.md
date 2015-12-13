{:title "What Do You Get When You Send an Engineer to Poland to Give His First Presentation"
:layout :post
:tags ["conferences" "ruby" "speaking"]}

A version of this post can be found [here][1].

There are two types of people: producers and consumers. Producers make things that consumers use.
We as software developers are both – we build software using software. We also create and consume
information: books, [blog posts][2], and conference talks, to name a few.

I recently gave my first conference talk at [wroc_love.rb][3] in Wrocław, Poland. We've had good
success with refactoring a complicated form to a form object, and we wanted to share this
knowledge. There are [many][4] [blog][5] posts that mention form objects, but we found that they
only gave an overview of what they are and didn't go into as much detail as we wanted.

Some of the best talks for me go into detail on a specific topic with lots of examples. Attila
Domokos' "[Simple and Elegant Rails Code with Functional Style][6]" is one example, and I wanted
to emulate that style of deep diving.

## Preparation

Two great resources were [speaking.io][7] for how to give the talk and [reveal.js][8] for the
presentation. Reveal.js was super simple to set up, and it let me use Markdown for the content.
These constrained me to keep the slides basic and not get caught up with all the fancy features
of typical presentation software.

My goals were to draw the audience into why one would want to use a form object and to be
(somewhat) entertaining. I framed the talk around a scenario of building a form that needs to
persist to multiple database tables, and then adding a feature to it to show how quickly things
get overly complex with the Rails default ways of making multiple model-backed forms. I'd then
show the refactored version that used a form object and how much simpler the same new feature was
to implement.

Of course, I had to include [some silliness][9] as well. Because internet.

I was nervous about the talk. I thought I would talk too fast, that I would forget everything,
that nobody would pay attention and have any questions, that I would be a failure. There were a
lot of psychological barriers, but I figured I should just keep going – preparing the code, the
slides, the content. My coworkers were a big help, giving me support, feedback, and
accountability. After all, I couldn't back out when everyone I work with would know about it.

## The conference

[Wrocław][10] was fun. It's very pretty, has a lot of history (the [dwarfs][11] were a nice
touch), and the food was good and plentiful. The conference took place at the [university][12] and
was run very well.

There were about 100 people at the conference, mostly local developers, but I heard a few American
accents. I spoke on the second day so there was time to incorporate some of the information from
the initial talks into mine. We had plenty of coffee and slow wifi, just like every other
conference I've attended.

**Highlight**: Talking to Michael Feathers about [pierogi][13]

**Lowlight**: Jet lag. Boo.

## The talk

Well, it was finally time. I plugged in my computer, set up the microphone, got introduced, and
proceeded to rip through my slides at lightening speed. Nerves and tunnel vision do quite a number
on ones perception of time, and as Mike Tyson says, "Everybody has a plan until they get punched
in the face." Good thing I had my notes as I could read from those. It was also incredibly
important that I went through the talk numerous times, adding notes each time and getting more
comfortable with the material. The difference between my first run-through and the last was night
and day.

## It's over already?

And then I was done. There were a few questions, some definitely because I spoke too fast. After
some applause, a dozen audience members came up to the podium to ask more specific questions. We
ended up digging through the code of the [sample application][14]. Everyone was very nice and we
had some good discussions. It felt good to know that the content resonated enough to talk to me.

## Next steps

I can now say that I'm a conference presenter. It feels good, but there's a lot of room to
improve. To that end, I've submitted the talk to more upcoming conferences, so hopefully you'll be
able to catch the presentation.

The questions provided a lot of good feedback for weaknesses in the content, so I know that the
next time will be a much stronger presentation. Plus I will be less nervous (but still nervous)
and can present the material more slowly and effectively.

It was a great experience, that I recommend to everyone. We've all have hard-fought experiences
that we can teach others. I've done a conference talk and so can you.

## Lessons learned

* It's going to happen so just keep moving forward.
* A support network is invaluable.
* Talk even slower.
* Practice, practice, practice.
* People are nice so don't be afraid of them.

## Postscript

Here are the [slides][15] and here is the [video][16].

[1]: http://engineering.sharethrough.com/blog/2014/03/21/what-do-you-get-when-you-send-an-engineer-to-poland-to-give-his-first-presentation/
[2]: http://engineering.sharethrough.com/
[3]: http://wrocloverb.com/
[4]: http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/
[5]: http://robots.thoughtbot.com/activemodel-form-objects
[6]: http://confreaks.com/videos/2431-railsconf2013-simple-and-elegant-rails-code-with-functional-style
[7]: http://speaking.io/
[8]: http://lab.hakim.se/reveal-js/#/
[9]: http://upload.wikimedia.org/wikipedia/en/5/5f/Original_Doge_meme.jpg
[10]: http://en.wikipedia.org/wiki/Wroc%C5%82aw
[11]: http://en.wikipedia.org/wiki/Wroc%C5%82aw%27s_dwarfs
[12]: http://en.wikipedia.org/wiki/Wroc%C5%82aw_University_of_Technology
[13]: http://en.wikipedia.org/wiki/Pierogi
[14]: https://github.com/dbolson/form-object-presentation
[15]: http://www.slideshare.net/dannyolson315/objectify-your-forms
[16]: https://www.youtube.com/watch?v=NENkGg-wzx8
