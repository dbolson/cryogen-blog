{:title "MySQL RSpec won't clear the database between test runs"
:layout :post
:tags ["ruby" "testing"]}

A quick one that can save a major headache. It seems that RSpec won't clear the database of a
record that is defined outside of a let statement, a begin block, or an it block.

```ruby
describe '#some_method' do
  record = FactoryGirl.create(:my_record)

  it 'does something' do
    # ...
  end
end
```

In this case, record will not get cleared out between test runs. It's noticeable when checking
that a query only returns certain records because it could include an extra one that's unexpected.

The solution is to either declare the record in a let statement, a begin block, or an it block.

Headache gone.
