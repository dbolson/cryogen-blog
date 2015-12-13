{:title "Testing controller JSON responses in Rspec"
:layout :post
:tags ["rails" "ruby" "testing"]}

I was recently rewriting some controller specs because they were way too heavy: all the models
were saved to the database and there was no mocking. While trying to test the JSON response of an
action, I got the following exception:

```ruby
ActiveSupport::JSON::Encoding::CircularReferenceError
```

Here is the relevant controller code:

```ruby
format.json do
  render :json => as_json(@questions)
end
```

Here is the test's mock:

```ruby
question = mock_model(Question)
```

A little cryptic, right? After a little digging, I changed the mock to this:

```ruby
question = mock_model(Question, :as_json => {'foo' => 'bar'})
```

Ah, there we go!

The spec ended up looking like this:

```ruby
context "as json" do
  it "lists the questions" do
    question = mock_model(Question, :as_json => {'foo' => 'bar'})
    Question.should_receive(:find_ordered_subjects).and_return([question])
    get :index, :format => 'json'
    response.body.should == "[{\"foo\":\"bar\"}]"
  end
end
```

In our `application_controller.rb`, an `as_json` method called `to_json`, which would call
`as_json` in the test, resulting in a circular reference. Oops.

Don't forget to stub `as_json`!
