{:title "Setting default arguments in Ruby the right way"
:layout :post
:tags ["ruby"]}

For setting default arguments, my regular idiom has been:

```ruby
def initialize(options={})
  @param = options[:param] || false
end
```

Recently, Josh Susser [tweeted][1] that this is bad.

> Ruby API protip: don't use ||= to set defaults in an options hash. That blocks users being able
> to pass false as a value. #lolrails

Here's an example of what he's talking about:

```ruby
class Foo
  def initialize(options={})
    @param = options[:param] || true
  end

  def foo
    puts "param: #{@param.inspect}"
  end
end

Foo.new(param: true).foo  # => "param: true"
Foo.new(param: nil).foo   # => "param: true"
Foo.new(param: false).foo # => "param: true"
```

We can't set the parameter to be nil or false since it will evaluate the line logically to be
true.

The Fix

Let's rewrite the line in the initializer to use #fetch.

```ruby
class Foo
  def initialize(options={})
    @param = options.fetch(:param, true)
  end

  def foo
    puts "param: #{@param.inspect}"
  end
end

Foo.new(param: true).foo  # => "param: true"
Foo.new(param: nil).foo   # => "param: nil"
Foo.new(param: false).foo # => "param: false"
Foo.new.foo # => "param: nil"
```

Now we get the results we want for the parameter we pass in. But don't worry, we can still get the
default when we don't pass in a value:

```ruby
Foo.new.foo # => "param: true"
```

[1]: https://twitter.com/joshsusser/status/332302920752197633
