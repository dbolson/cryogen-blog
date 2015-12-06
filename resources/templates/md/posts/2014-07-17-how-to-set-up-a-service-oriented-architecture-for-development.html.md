{:title "How to Set up a Service Oriented Architecture for Development"
:layout :post
:tags ["soa" "tools"]}

Service oriented architectures can have many benefits, but there are trade-offs as well. One
immediate annoyance is setting up multiple applications to do local development. The current
workflow looks like this:

**Side note**: Things have been quiet around here as I've just completed my first month at
[Funding Circle][1] and haven't made the time to blog. Now that I'm settling in, let's get this
blog going again.

```
$ cd app1
$ RACK_ENV=development rackup -p 3002

# open a new tab
$ cd app2
$ RACK_ENV=development rackup -p 4000

# open a new tab
$ cd app3
$ RACK_ENV=development rackup -p 5000

# etc.
```

It's also convenient to label the tabs to know where to look for an application's output, so we
need to name each tab as well. This is boring and error prone as it's very easy to forget to start
an application that could prevent the entire system from working as expected.

## Consular to the Rescue!

We just recently found out about [Consular][2] to automate terminal tasks and turn the above
headache into a one command solution.

Setup is simple:

```
$ gem install consular
$ gem install consular-iterm
$ rbenv rehash # if appropriate
$ consular init
```

This generates `~/.consularc` for customization, and `~/.config/consular` for projects. If you're
using [iTerm][3], you'll need to require the necessary core.

```
# ~/.consularc
require 'consular/iterm'

# the rest of the file
```

Now generate a project:

```
$ consular edit soa-setup
```

Here is an example project that replaces the current workflow:

```
# ~/.config/consular/soa-setup.term
tab 'app1' do
  run 'cd ~/workspace/app1'
  run 'RACK_ENV=development rackup -p 3002'
end

tab 'app2' do
  run 'cd ~/workspace/app1'
  run 'RACK_ENV=development rackup -p 4000'
end

tab 'app3' do
  run 'cd ~/workspace/app3'
  run 'RACK_ENV=development rackup -p 5000'
end
```

Now all you need to do is run one command, `consular start soa-setup`.

## Some tweaks

It's nice to not have to do this (minimal) song and dance for each development machine, so we
packaged this up into some scripts to make the process easier.

```
# bin/setup
echo "Installing Consular..."

bundle install
rbenv rehash
consular init

# prepend require to file
echo "require 'consular/iterm'\n" > tmpfile
cat ~/.consularc >> tmpfile
cp tmpfile ~/.consularc
rm tmpfile

# copy project(s) to home directory to use anywhere
cp lib/default.term ~/.config/consular/

echo "\nRun bin/run to start all applications."
```

```
# bin/run
consular start default
```

```
# lib/default.term
tab 'app1' do
  run 'cd ~/workspace/app1'
  run 'RACK_ENV=development rackup -p 3002'
end

tab 'app2' do
  run 'cd ~/workspace/app2'
  run 'RACK_ENV=development rackup -p 4000'
end

tab 'app3' do
  run 'cd ~/workspace/app3'
  run 'RACK_ENV=development rackup -p 5000'
end
```

```
# Gemfile
source 'https://rubygems.org'

gem 'consular'
gem 'consular-iterm'
```

Finally, you can create a symlink to the `run` command:

```
ln -s ~/path/to/bin/run ~/launch-apps
```

Go forth and be lazy, I mean, [virtuous][4]!

[1]: https://www.fundingcircle.com/us/
[2]: https://github.com/achiu/consular
[3]: http://www.iterm2.com/
[4]: http://c2.com/cgi/wiki?LazinessImpatienceHubris
