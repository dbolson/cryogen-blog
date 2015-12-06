{:title "Rails Security Refactor - Protect Those Attributes"
:layout :post
:tags ["rails" "ruby" "testing"]}

Rails in the enterprise is still a fairly new concept, but the same web development principles we
have also exist in this new realm. One no-brainer is security, and one no-brainer part of security
is protecting data from malicious user input.

The basic Rails ways to protect attributes on a model are `attr_protected` and `attr_accessible`.
If neither are set, it's easy to imagine a situation where a user updates his attributes and the
corresponding model has a boolean admin field on it. The user can trivially submit post data that
looks like this:

```ruby
params: { "user" => { :email => "foo@bar.com", :first_name => "Joe", :last_name => "Hacker", :admin => "true" }}
```

Oops! Now Mr. Hacker is Mr. Admin!

So how do we get from here to (more) secure? Throwing `attr_accessible` on the model to whitelist
is the safest, but it can cause a lot of unknown breakage if there aren't tests around the fields,
which there probably aren't because why would someone test the fields for accessibility if they
are automatically accessible? An interim step is to create a blacklist) using `attr_protected` to
only protect specified fields, get tests around these protected fields, and then upgrade to
`attr_accessible`.

For our user model, let's protect that admin field:

```ruby
Class User < ActiveRecord::Base
  attr_protected :admin
end
```

And the tests:

```ruby
describe User do
  describe "with protected fields" do
    context "including admin" do
      let(:user) { Factory.build(:user, :admin => false) }

      it "cannot mass-update" do
        user.update_attributes({ :admin => true })
        user.admin.should be_false
      end
    end
  end
end
```

We instantiate a user object with admin set to false, try to update that field, and ensure that it
did not get updated. If we use `user.update_attribute(:admin, true)`, the test would fail because
that skips all the ActiveRecord protection, so we use `user.update_attributes`. Doing this for all
the fields we want to protect will eventually get us to the point where we can swap out
`attr_protected` with the easier to deal with `attr_accessible`. Since we need to be explicit with
`attr_protected`, it can get to be difficult to maintain quickly since we need to remember to add
each new field we want to protect to the list and test it.

```ruby
Class User < ActiveRecord::Base
  # attr_protected :id, :admin, :awesomeness_rating, :money
  attr_accessible :email, :first_name, :last_name
end
```

That's better. Tests and iterative development to the rescue!
