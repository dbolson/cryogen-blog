{:title "Do Not Do This: Stub System Under Test"
:layout :post
:tags ["testing"]}

In OOP it is a good thing when we're able to use one method in a class to create another. Unfortunately there can be a tendency to stub out the one method when writing tests for another in the system under test (SUT). "We've already tested that one, so it's redundant to test it again" you might say.

This reasoning can lead to two affects on our codebase. The first, as detailed [here][sut], is that the test setup tells us a lot about our code. If the setup is difficult it can be a sign that we should move parts of our code around to split up our different functionality.

Another risk I'd like to discuss is how this stubbing causes brittleness in our tests and can mask bugs.

Say we are building a very basic calculator. We start with one method that adds two numbers.

We write a test first.

```ruby
require "minitest/autorun"

class TestCalculator < Minitest::Test
  def test_add
    c = Calculator.new

    assert_equal 3, c.add(1, 2)
  end
end
```

We then write the code to make it pass.

```ruby
class Calculator
  def add(x, y)
    x + y
  end
end
```

We next want to write an increment method, reusing our `#add` method. Since we've already tested `#add` we can just stub the response from that.

```ruby
class TestCalculator < Minitest::Test
  def test_add
    c = Calculator.new

    assert_equal 3, c.add(1, 2)
  end

  def test_inc
    c = Calculator.new

    c.stub(:add, 3) do
      assert_equal 3, c.inc(2)
    end
  end
end

class Calculator
  def add(x, y)
    x + y
  end

  def inc(x)
    add(1, x)
  end
end
```

Now -- to be web scale -- we want to change our `#add` method to subtract one from the sum (remember: web scale!). We update the method's test and code.

```ruby
class Calculator
  def add(x, y)
    x + y - 1
  end

  def inc(x)
    add(1, x)
  end
end

class TestCalculator < Minitest::Test
  def test_add
    c = Calculator.new

    assert_equal 2, c.add(1, 2)
  end

  def test_inc
    c = Calculator.new

    c.stub(:add, 3) do
      assert_equal 3, c.inc(2)
    end
  end
end
```

Our tests stay passing and we're all good, right? Well our `#inc` method is still passing, but it is clearly the wrong answer when we run the code manually.

```
puts Calculator.new.inc(2) # => 2
```

By stubbing the call to `#add` in the `#inc` method, we've hidden a bug in our `#inc` method. If there were no stub we would have noticed the test failure and been able to fix it before our customers noticed.

"But it's a pain to fix the `#inc` test when we were only changing the `#add` code." That's true, and our tests are doing us a favor by showing us this coupling. Maybe this is bad and it doesn't make sense for the `#inc` method to be coupled to the `#add` method, or maybe it's an acceptable trade off to ensure the two methods are internally consistent.

The triviality of this example makes clear the risks of stubbing the SUT. This risk scales to other objects such as `ActiveRecord` classes, especially validations and callbacks, and other languages.

Please don't stub the SUT. External dependencies are fine to stub, and sometimes our tests are telling us when the current SUT does too much and should be broken apart.

[sut]: https://robots.thoughtbot.com/don-t-stub-the-system-under-test
