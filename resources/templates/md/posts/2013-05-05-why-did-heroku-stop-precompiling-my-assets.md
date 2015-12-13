{:title "Why did Heroku stop precompiling my assets?"
:layout :post
:tags ["bugs" "rails" "tools"]}

I come into work today to this error:

```ruby
ActionView::Template::Error ('twitter/bootstrap/bootstrap.less' wasn't found.
```

Odd...It works in development. What could have happened?

## The search

I ran the application locally with the development environment and got the same error. Great, at
least it's reproducible. Let's hit the Googles...

Hmm, some mentions in [twitter-bootstrap-rails][1] and some scattered [StackOverflow][2]
questions, but nothing that works. Then I noticed Heroku was no longer precompiling the assets on
deploy.

## The discovery

The [Heroku docs][3] have this glorious quote:

> The most common cause of failures in assets:precompile is an app that relies on having its
> environment present to boot. Your app's config vars are not present in the environment during
> slug compilation, so you should take steps to handle the nil case for config vars (and add-on
> resources) in your initializers.

A recent change added some configuration variables in `application.rb` that referenced some
environment variables.

## The fix

Based on some more [documentation][4] and [community help][5], I moved the configuration into an
initializer that is loaded after the slug compilation when it can access the environment
variables.

Deploy. Working. #winning.

[1]: https://github.com/seyhunak/twitter-bootstrap-rails
[2]: http://stackoverflow.com/questions/9047478/rails-app-with-twitter-bootstrap-works-in-development-but-not-in-production/9117069#9117069
[3]: https://devcenter.heroku.com/articles/rails-asset-pipeline#troubleshooting
[4]: https://devcenter.heroku.com/articles/smtp
[5]: http://stackoverflow.com/questions/7855870/gmail-on-heroku-with-rails-3
