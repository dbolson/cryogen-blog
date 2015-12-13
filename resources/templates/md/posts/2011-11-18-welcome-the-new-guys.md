{:title "Welcome the New Guys"
:layout :post
:tags ["pair programming"]}

We recently hired a trio of new developers at work, and it's been such an enjoyable experience to
have them around. [Tim](http://www.tjames.com/), Clay, and [Yan](http://yanpritzker.com/) (who
starts soon) are all solid developers and great guys as well.

I've paired with Clay and Tim full-time, and I'm completely sold on the practice. The mind-share
we create through immediate feedback and conversation is invaluable. Management likes it because
it means the new hires are productive from the outset, as the training happens in real-time. We're
learning from each other in numerous ways. For example, the default macvim/git interaction doesn't
work, so I have had to use the command line for commit messages. Tim kindly informed me that I can
set my bash editor to default to macvim using:

```bash
EDITOR="macvim -f"
```

Now I can write my commit messages in macvim. The -f flag prevents forking when starting so git
will wait until the editor closes before proceeding. A nice thing to have.

Here's to the new guys!
