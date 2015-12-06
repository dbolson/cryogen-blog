{:title "A use for Ruby constants"
:layout :post
:tags ["ruby"]}

We know that [constants][1] [aren't][2], so I've always hesitated to use them when I could use a
constant method instead. This makes the value more difficult to change since I can't just reassign
the method's return value. It also makes it easier to test since I can override it explicitly with
a stub.

But now I have a use for a constant...

I have a default value in an `ActiveRecord` model that I set on a value when it's nil. I don't
keep this in the database since it's an arbitrary value that will probably change often, and it's
easier to change that in the application layer. I would normally have this value returned from a
method in the class, but I use it twice: once in a validation callback and once in a class method.
I would then need to define it twice:

```ruby
def self.foo_value
  'foo'
end

def foo_value
  self.class.foo_value
end
```

True, I can reuse the code and only declare the actual value once, but this isn't DRY since the
"logic" of the value is in two spots.

At least for now, I'm going to use a constant to set the value once and reference it at the class
level and the instance level.

[1]: http://www.liveandcode.com/2009/11/16/adventures-in-ruby-when-constants-arent/
[2]: http://stackoverflow.com/questions/1977780/what-does-ruby-constant-mean
