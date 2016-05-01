{:title "FactoryGirl fun with associations with associations"
:layout :post
:tags ["rspec" "ruby" "testing"]}

Ah, [factories][1]. Such a powerful feature that's so easy to abuse and make our tests slow and
unhappy. But they're just so convenient, if we can only manage to keep them doing exactly what we
want and not create extra database records.

The main problem comes from associations because each one will insert a new record into the
database unless it's specified at creation. Using [composition][2] helps, but there is still another
problem: if we have an association that itself has an association, the original association still
saves a new record in the database for its association. An example will help.

```ruby
# models/widget.rb
class Widget < ActiveRecord::Base
  belongs_to :account
  belongs_to :doodad
end

# models/doodad.rb
class Doodad < ActiveRecord::Base
  belongs_to :account
end

# spec/factories/widgets.rb
FactoryGirl.define do
  factory :widget do
  end

  trait :with_doodad do
    association :doodad
  end

  trait :with_account do
    association :account
  end
end

# spec/factories/doodads.rb
FactoryGirl.define do
  factory :doodad do
  end

  trait :with_account do
    association :account
  end
end

widget = FactoryGirl.create(:widget, :with_doodad)
widget.account == widget.doodad.account # => false
```

The instance of `Doodad` has an account that's created when it's created, so `widget`'s account and `doodad`'s account will be different accounts. This can be a problem if it matters that both belong to the same account.

### Yo dawg, I heard you like associations...

We can use [lazy attributes][3] to set the `doodad`'s account to be the same as the `Widget`'s.

```ruby
FactoryGirl.define do
  factory :widget do
    account
    doodad { |w| w.association(:doodad, account: w.account) }
  end
end
```

We now have one account that's associated with both the `widget` and the `doodad`.

This is also nice for a model for a join table used for `has_many :through` associations.

```ruby
# models/assignment.rb
class Assignment < ActiveRecord::Base
  belongs_to :account
  belongs_to :programmer
  belongs_to :project
end

# models/programmer.rb
class Programmer < ActiveRecord::Base
  belongs_to :account
  has_many :assignments
  has_many :projects, through: :assignments
end

# models/project.rb
class Project < ActiveRecord::Base
  belongs_to :account
  has_many :assignments
  has_many :programmers, through: :assignments
end

# spec/factories/assignments.rb
FactoryGirl.define do
  factory :assignment do
    association :account
    programmer { |a| a.association(:programmer, account: a.account) }
    project { |a| a.association(:project, account: a.account) }
  end
end
```

[1]: https://github.com/thoughtbot/factory_girl/blob/master/GETTING_STARTED.md
[2]: https://blog.pivotal.io/pivotal-labs/labs/how-to-use-factorygirl-effectively
[3]: https://github.com/thoughtbot/factory_girl/blob/master/GETTING_STARTED.md#lazy-attributes
