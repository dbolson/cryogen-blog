{:title "My Tools, My Choice"
:layout :post
:tags ["pair programming" "tools" "vim"]}

There are some technology choices a team should make as a whole such as programming language(s), deployment strategies, and hosting. One choice that should be up to the individual is text editor.

### Editor Wars

I write software in [vim][vim]. I write emails and notes and, yes, blog posts in vim. I could go on and on about why I use vim for everything, but let's just say I like it. A lot. I like it enough that it would be a deal-breaker if I could not use it at work. This isn't to seem precious and it doesn't mean that other editors [are crap][crap], it's just that I made a decision to use vim, I reevaluate that decision when I see another editor, and I've invested the time and muscle memory to learn vim. The tool I use for Every. Single. Keystroke. (it's vim so there are a lot!) is kind of a big deal.

### Fungible Assets

Engineers are not interchangeable. The practice of pair programming taken to the extreme (pun somewhat intended) -- where all pairing stations are identical -- can lead a team to believe that there can only be *the one* editor on each station. The argument goes, "this ensures anybody is able to pair with anybody else and not need a custom setup for the special snowflake." This also means that, unless there is a major coincidence, not everyone will be able to use their favorite editor. And finally, since these, ahem, "consistent" practices come from [a place][pivotal] that uses [RubyMine][rubymine], everyone uses RubyMine (with custom key bindings to boot).

### Can't We All Just Get Along?

Here's a simple way to fix this.

"Anybody use vim?" Okay, come up with a company `.vimrc` and put it on every machine.

"Anybody use emacs?" Okay, come up with a company `.emacs` and put it on every machine.

"Anybody use TextMate? Hello, is this thing on?"

Editors are able to hot-reload a file if it changes, so if I make a change in one editor you're able to see the results in another editor. And since pairing tends to involve one person typing at a time, it's simple enough to switch back and forth depending on who's typing.

On the one hand, it's not optimal because editor updates need to propagate to each machine, but that's simple enough to automate. On the other hand, the productivity and happiness gains for developers who prefer another editor are huge.

[vim]: /tags-output/vim.html
[crap]: https://www.youtube.com/watch?v=seN7AKSwMFU
[pivotal]: http://pivotal.io/labs
[rubymine]: https://www.jetbrains.com/ruby/
