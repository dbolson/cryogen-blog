{:title "Emailing with Delayed Job"
:layout :post
:tags ["rails"]}

We used [Delayed Job](https://github.com/tobi/delayed_job) to queue emails sent out to users, both
to offload that blocking process and for scheduling. It has worked well so far, but recently there
were some strange bugs popping up. Some emails were stuck in the queue, and the error message was
about bad YAML syntax.

Delayed Job serialized objects in its handler field, and, with some user input that's not encoded
properly, created incorrect YAML. For example, this could happen:

```yaml
id: 1
  foo: 'here is some 'text'
  bar: 'something else'
```

Notice the odd number of single quotes in `foo`? Yeah, that's bad. Since we already had that kind
of data saved, we needed another way to fix this.

Instead of having methods in the notifier.rb file like so:

```ruby
def forgot_password(user)
  ...
end
```

We did it like this:

```ruby
def forgot_password(user_id)
  user = User.find(user_id)
  ...
end
```

Delayed Job serialized just the user id and not the entire user object, so any potentially harmful
data wasn't saved. This was more expensive since the objects had to get instantiated again, but
sending out email wasn't expensive for our app, so this solution worked well.

If you ever get strange YAML syntax errors from delayed job, perhaps this method will work for
you.
