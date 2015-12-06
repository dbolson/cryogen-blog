{:title "Using ActiveSupport::TimeWithZone without Rails"
:layout :post
:tags ["rails" "testing"]}

My fast spec test suite does not load Rails (otherwise it's not so fast, right?), but I want to
use the niceness of `ActiveSupport::TimeWithZone` to create a `DateRange` class like so:

```ruby
class DateRange
  class DateRangeError < StandardError; end

  def initialize(range)
    @range = range
    raise_if_not_utc
  end

  def start_date
    range.first.beginning_of_day
  end

  def end_date
    range.last.end_of_day
  end

  private

  attr_reader :range

  def raise_if_not_utc
    if start_date.zone != 'UTC' || end_date.zone != 'UTC'
      raise DateRangeError.new('The date ranges must be in UTC.')
    end
  end
end
```

The corresponding tests didn't like `Time.zone` – they thought it was nil. Which it is.

## The fix

The tests need the proper `ActiveSupport` module loaded, and the time zone needs to be set.

```ruby
require 'active_support/core_ext'
Time.zone = 'UTC'
```

Now the time zone is set and the tests will work as expected.

```ruby
let(:range) { daterange.new(10.days.ago..time.zone.now) }

describe '#start_date' do
  it 'gets the beginning of the day for the earliest date in the range' do
    expect(range.start_date.to_i).to eq(Time.zone.parse('1999-12-31 00:00:00').to_i)
  end

  it 'is in UTC' do
    expect(range.start_date.zone).to eq('UTC')
  end
end
```
