{:title "Rails 2.3.5 Bug With accepts_nested_attributes_for"
:layout :post
:tags ["bugs" "rails" "ruby"]}

At work, our application has a series of questions it asks users, and an administrator can set a
target of what he thinks (or wants) the answer to be.

```ruby
class Question < ActiveRecord::Base
  has_many :targets
  accepts_nested_attributes_for :targets, :allow_destroy => true
end

class Target < ActiveRecord::Base
  belongs_to :question
end
```

The administrator fills the form out, choosing a target or not, and Rails will save, update, or
destroy it as normal. Well, almost. [Apparently](https://github.com/rails/rails/commit/146a7505680cbb646c0b9d55dca7cc8494b36e47)
there is a bug in Rails 2.3.5 that will save the child model twice, and we're experiencing that
now.

## What I want to do

Upgrade to Rails 3.1, get all the new hotness and bug fixes, and not worry about this problem.

## What I'm going to do

Since we don't have the resources to upgrade Rails (yet…), we need a workaround. Here's what we
came up with.

```ruby
after_save :ensure_one_target_while_in_draft

def ensure_one_target_while_in_draft
  if draft? && targets.count > 1
    targets[1..-1].each { |t| t.destroy }
  end
end
```

This problem only comes up when a question has not been activated and is still in draft mode. Once
it's activated, the administrator cannot change the target but only add additional ones. So while
in that state, and if there are multiple targets, destroy all but the first. This needs to be in
an `after_save` callback because the double-save does not happen until after the question is saved.
It's not elegant, but it works. I don't want to try to patch Rails and remember to deal with that
later when we finally do upgrade, and the situation is isolated enough that there isn't a
performance hit and it's easy to remove once it's no longer necessary. Elegant? No. But it's a
bug fix for the framework, it's well documented in the code, and it works.
