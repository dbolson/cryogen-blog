{:title "RSpec message expectations with dates"
:layout :post
:tags ["rspec" "ruby" "testing"]}

I've got a class that has a collaborator class. The collaborator takes a start date and an end
date as arguments. I want to test that the original object instantiates the collaborator with the
right dates.

## A little tricky

The tests fail if I were to assert that the collaborator should receive dates as its arguments.

```ruby
# the test
a_collaborator.should_receive(:new)
  .with('an argument',
      Time.zone.parse('2000-1-11 00:00:00'),
      Time.zone.parse('2000-1-11 23:59:59'))
  .and_return(a_collaborator_instance)

# the error
Stub :a_collaborator received :new with unexpected arguments
 expected: ("an argument", Tue, 11 Jan 2000 00:00:00 UTC +00:00, Tue, 11 Jan 2000 23:59:59 UTC +00:00)
      got: ("an argument", Tue, 11 Jan 2000 00:00:00 UTC +00:00, Tue, 11 Jan 2000 23:59:59 UTC +00:00)
```

Time comparison in Ruby is a little tricky. So what do we do?

## An unexpected expectation syntax

It [turns out][1] that RSpec has another expectation syntax that will let us do some manipulation
of the date arguments. Check this out:

```ruby
a_collaborator.should_receive(:new) { |an_argument, start_date, end_date|
  expect(an_argument).to eq('an argument')
  expect(start_date.to_i).to eq(Time.zone.parse('2000-1-11 00:00:00').to_i)
  expect(end_date.to_i).to eq(Time.zone.parse('2000-1-11 23:59:59').to_i)
}.and_return(a_collaborator_instance)
```

We're able to get the seconds since the Epoch version of the dates to get an exact number to
compare to each other.

The seconds matter, so it's good to know we can test them easily.

[1]: https://www.relishapp.com/rspec/rspec-mocks/docs/message-expectations
