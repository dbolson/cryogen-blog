{:title "State Machine Transactions"
:layout :post
:tags ["rails" "ruby"]}

We have a model that has a [state machine](https://github.com/omghax/acts_as_state_machine) on it
to activate, close, and cancel after meeting certain conditions. When closing this model, we want
to manipulate an associated model as well, and we want these in a [transaction](http://api.rubyonrails.org/classes/ActiveRecord/Transactions/ClassMethods.html)
to preserve data integrity. Unfortunately, there is logic in the associated model that doesn't
like to have the original model in the new state before saving.

One solution is to set up the following callbacks:

```ruby
state :closed, :enter => :enter_close, :after => :after_close
```

```ruby
def enter_closed
  # do stuff to model
end

def after_close
  # do stuff to associated model
end
```

### The Problem

It turns out that the callbacks are not wrapped in a transaction and that `#after_close` is called
after the model saves, leaving the associated model in danger of getting in a back state or
failing validations.

### The Solution

Using [this post](http://rhnh.net/2010/07/05/acts-as-state-machine-is-not-concurrent) as a guide,
we ended up with this code:

```ruby
class ActiveRecord::Base
  def self.transaction_around_transitions
    event_table.keys.each do |t|
      define_method("#{t}_with_lock!") do
        transaction do
          send("#{t}_without_lock!")
        end
      end
      alias_method_chain "#{t}!", :lock
    end
  end
end
```

And call that method after setting up the transitions:

```ruby
event :close do
  transitions :from => :foo, :to => :bar
end
```

### Other Transitions...

Now there is a transaction around the entire state change and its callbacks, and we'll be able to
sleep tonight knowing that all the saving is safe and sound, all wrapped up in a nice, warm
transaction.
