{:title "Can We Have Too Many Tools?"
:layout :post
:tags ["vim"]}

[Vim](http://www.vim.org/) is a great editor. There are [so many plugins](https://github.com/carlhuda/janus)
that make it even better and increase my productivity. But can there be a saturation point where
it's not worth finding the next plugin to shave a keystroke off of a command? I've been trying to
find the sweet spot for the right amount of tools for the job.

## Bigger toolbox

Having [more developers](blog/2011/11/18/welcome-the-new-guys/) to talk to and work with, I have
been exposed to different methods of development. [Yan](http://yanpritzker.com/2011/12/16/learn-to-speak-vim-verbs-nouns-and-modifiers/)
in particular, has been a tremendous Vim resource. He's made it his focus to optimize working with
this editor as much as possible, and it's quite impressive. I've adopted some of his ideas, no
longer content to be "good enough" with Vim.

Janus has been a tremendous help. It exposed me to [command-T](http://www.vim.org/scripts/script.php?script_id=3025),
which is now my favorite thing ever. I no longer use a buffer explorer because it's easier to just
find it with command-T than search through the buffer list. I also rarely use [NERDtree](http://www.vim.org/scripts/script.php?script_id=1658)
now, since I was using that mostly as a convenience to open project files. I'll still use it for
looking in directories, but it's not open by default anymore.

From Yan, I've install [git-grep](https://github.com/tjennings/git-grep-vim) and mapped `K` to
search for the word under the cursor, and that's been such a pleasant, and faster experience, than
using vimgrep. Yan is also cleaning up a plugin for rspec integration that provides some nice
wins.

## Too big?

But how far do I take this path? I could continue to add plugins and map commonly used keystrokes
to further increase efficiency, but when do I start to get diminishing returns? For example, I
could map a letter to `:GitGrep` and save six keystrokes, but I haven't found the need to do that
yet. Usually when I'm searching for something, I'm thinking about what I want to search for while
typing the command, so I'm effectively multitasking and not wasting time with all that extra
typing. Sometimes slowing down can be a good thing to allow that planning. Plus each new mapping
or plugin is something new to learn, and it can occasionally become overwhelming with all the new
options.

## Just right, for now

I'll definitely continue to improve my Vim Fu, but I'm not in a hurry to continually add to my
repertoire until I've mastered what I currently have available. I've come this far on a basic
setup, and Vim has been around long enough that there have been many others who have gotten by
with less, so I'm not going to stress out about not optimizing every single key I type.
