{:title "Refactoring to a Query Object"
:layout :post
:tags ["refactoring" "ruby"]}

We noticed that the approval badges for a manager were off. They were calculating how many *total*
records there were instead of records for the users managed by the current manager.

```ruby
# view partial
= link_to managers_approvals_path do
  #icon
  = display_badge(Approval.where(status: 'requested').count)
```

We were also doing too much logic in the view, so we needed to extract this. We want to minimize
what we put in `ActiveRecord` models so they only have to worry about persistence, and the query
needs two objects to figure out the right data, so we put this code in a [query object][1].

```ruby
# app/queries/requested_approvals.rb
class RequestedApprovals
  def self.requested_for_manager(user)
    new.requested_for_manager(user)
  end

  def initialize(relation=Approval)
    @relation = relation
  end

  def requested_for_manager(user)
    relation
      .where('user_id IN (?)', user.managed_user_ids)
      .requested
      .by_name
  end

  private

  attr_accessor :relation
end
```

We can further refactor the `#where` method to a scope in `approval.rb` since there's a bit of
feature envy:

```ruby
# app/models/approval.rb
scope :for_users, -> (user_ids) { where('user_id IN (?)', user_ids) }
```

So now we have:

```ruby
# app/queries/requested_approvals.rb
def requested_for_manager(user)
  relation
    .for_users(user.managed_user_ids)
    .requested
    .by_name
end
```

There are a few things going on here:

* `requested_for_manager` returns an `ActiveRecord::Relation` so we can treat it as a normal query
  (eg., chain methods on it).
* We use a class method so we don't need to use `RequestedApprovals.new.requested_for_manager(user)`.
  The class method delegates to the instance method, and we keep the instance method public so we
  can test the code more easily with a stubbed dependency (if we want to go that route).
* The `attr_accessor` is private so we don't expose it but can still access the instance variable
  without using `@relation`. This keeps the code a little cleaner because we're not littering
  instance variables all over the place.

Now we can do this:

```ruby
# app/controllers/managers/approvals_controller.rb
@approvals = RequestedApprovals.requested_for_manager(current_user)

# view partial
= display_badge(RequestedApprovals.requested_for_manager(current_user).count)
```

### Testing

We tested this through the controller as an integration test. This was slow, but it ensured the
proper behavior while we refactored.

```ruby
spec/controllers/managers/approvals_controller_spec.rb
describe Managers::ApprovalsController do
  let(:user) { create(:manager) }

  describe '#index' do
    let(:managed) { create(:user, manager: user) }

    let!(:requested_approval) { create(:approval, user: managed, status: 'requested') }
    let!(:requested_other_user_approval) { create(:approval, :with_user, status: 'requested') }
    let!(:requested_owned_by_manager_approval) { create(:approval, user: user, status: 'requested') }
    let!(:another_approved) { create(:approval, user: user, status: 'approved by finance') }

    before do
      sign_in user
    end

    it 'is successful' do
      get :index
      expect(response).to be_success
    end

    it 'shows the approvals for those managed by the user' do
      get :index
      results = assigns(:approvals)
      expect(results).to eq([requested_owned_by_manager_approval, requested_approval])
    end
  end
end
```

Now that we've refactored our code, we can write tests for the query object and throw out our
integration tests for faster, more easily understood unit tests.

```ruby
# spec/queries/requested_approvals_spec.rb
describe RequestedApprovals do
  let(:subject) { RequestedApprovals }

  describe '.requested_for_manager' do
    let!(:requested_approval) { create(:approval, user: managed, status: 'requested') }
    let!(:requested_other_user_approval) { create(:approval, :with_user, status: 'requested') }
    let!(:requested_owned_by_manager_approval) { create(:approval, user: user, status: 'requested') }
    let!(:another_approved) { create(:approval, user: user, status: 'approved by finance') }

    it 'finds approvals for the users the user manages' do
      results = subject.requested_for_manager(user)
      expect(results).to eq([requested_owned_by_manager, requested])
    end
  end
end
```

We kept the integration tests for the query object since it's conceptually tied to the database.
We could stub all the chained methods on the relation, but that's fragile and hard to test.

We changed the controller tests to make the query object a mock since that object was tested
somewhere else and we knew it worked.

```ruby
# spec/controllers/managers/approvals_controller_spec.rb
describe Managers::ApprovalsController do
  let(:user) { create(:manager) }
  let(:approval) { double(:approval) }

  describe '#index' do
    before do
      sign_in user
      allow(RequestedApprovals).to receive(:requested_for_manager).with(user)
    end

    it 'is successful' do
      get :index
      expect(response).to be_success
    end

    it 'shows the approvals for those managed by the user' do
      expect(RequestedApprovals).to receive(:requested_for_manager).with(user).and_return([approval])
      get :index
      expect(assigns(:approvals)).to eq([approval])
    end
  end
end
```

[1]: http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/
