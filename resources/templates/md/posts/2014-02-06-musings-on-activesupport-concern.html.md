{:title "Musings on ActiveSupport::Concern"
:layout :post
:tags ["rails" "refactoring"]}

I saw a `UserConcern` class for dealing with accepting terms and conditions show up in our code
base the other day. Concerns are a [contentious][1] [topic][2], so there isn't necessarily a wrong
answer, but I do side with Corey on this one.

The `Concern` mixes in additional functionality that's available at all times. It doesn't really
remove the functionality so much as move it around, and now it's in less obvious place. The object
still contains all the same methods, but we don't have a single place for these methods, so we
need to dig around more to find them.

An alternative approach would be to [decorate][3] the instance with the additional functionality
that's only needed in its current context. So we could do something like:

```ruby
class FooController
  before_action :enforce_tos

  def index
  end

  private

  def enforce_tos
    unless AcceptedTOSUser.new(current_user).accepted_tos?
      redirect_to :terms_path
    end
  end
end

class AcceptedTOSUser
  def initialize(user)
    @user = user
  end

  def accepted_tos?
    @user.accepted_terms_of_service?
  end

  def accept_tos!
    @user.update_attributes!(accepted_terms_of_service_at: Time.current)
  end
end
```

This makes sure the user has already accepted the terms and conditions before he can access the
`index` action. There is also additional functionality to do the TOS accept that would occur
somewhere else. This keeps all the TOS accepting functionality in the same class (nice and DRY)
and doesn't pollute the `User` space.

[1]: http://blog.coreyhaines.com/2012/12/why-i-dont-use-activesupportconcern.html
[2]: http://37signals.com/svn/posts/3372-put-chubby-models-on-a-diet-with-concerns
[3]: http://robots.thoughtbot.com/evaluating-alternative-decorator-implementations-in
