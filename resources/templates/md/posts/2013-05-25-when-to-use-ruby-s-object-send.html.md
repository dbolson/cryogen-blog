{:title "When to use Ruby's Object#send"
:layout :post
:tags ["ruby"]}

While refactoring a section of code to pull a few related methods into another class, that class
being a nested class of the original, I bumped into a small problem. Both the original and the
nested class needed access to some private methods. These methods were utility methods that summed
a field of the objects in both classes.

What should I do with these methods?

* I could have them in two spots, but that isn't DRY since they were operating on similar data
  structures so should only be declared once.
* I could move them to some sort of utility class or file, but that would be more difficult to
  find and could end up as a junk drawer of methods.
* I could make them public methods on the outer class and pass that class into the instance of the
  nested class, but I do not want to make those methods part of the public API of the outer class.
* Based on the above logic, I kept the methods private, sent an instance of the outer class into
  the instance of the inner class, and used `Object#send` to access the methods.

Here is what this looked like:

```ruby
class Outer
  def foo
    Inner.new(self).bar
  end

  def bar
    calculated_value = 'outer value'
    private_method(calculated_value)
  end

  class Inner
    def initialize(parent)
      @parent = parent
    end

    def bar
      calculated_value = 'inner value'
      parent.send(:private_method, calculated_value)
    end
  end

  private

  def private_method(value)
    puts "here's a value: #{value}"
  end
end
```

The inner class does not need its own `#private_method` but can access the method from the outer
class. Both classes keep a clean public API. Since the private methods are the most volatile, as
[Sandi Metz][1] says, "the writing test of private method means that you're writing a test that
tightly couples you to something that's unstable." The *simple* private method is tested through
the public API, so I feel confident that the code is tested properly.

Using `#send` is a [code smell][2], but this only "possibly indicates a deeper problem." Sensing
the smell allows us to be aware of our decision to reconsider the code, but it does not mean the
code needs to be changed.

[1]: http://rubyrogues.com/087-rr-book-clubpractical-object-oriented-design-in-ruby-with-sandi-metz/
[2]: http://en.wikipedia.org/wiki/Code_smell
