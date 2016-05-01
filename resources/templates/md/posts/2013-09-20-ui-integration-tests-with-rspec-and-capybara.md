{:title "UI Integration Tests with RSpec and Capybara"
:layout :post
:tags ["rspec" "ruby" "testing"]}

Now that our form object is sufficiently complex, but mostly because sales found a bug, we decided
we need to have UI tests for the happy paths for new and edit actions.

We did not want to use Cucumber because we [didn't see its value compared to its overhead][1], so
we kept the tests to [RSpec features][2] and Capybara. Features let us test the UI as a user would
and Capybara loads a browser so we have JavaScript available.

Here's an example of signing in a user, navigating to the form, filling it out, and submitting it.
We want the form to successfully save and redirect to another page.

```ruby
# spec/features/content_form_spec.rb

require 'capybara/rails'
require 'spec_helper'

feature 'User creates content' do
  scenario 'with valid parameters', :integration, js: true do
    sign_in

    visit '/contents/new'
    fill_in 'URL', with: 'http://www.youtube.com/watch?v=SfammxKoUYs'
    fill_in 'Title', with: 'The Ultimate Dog Shaming : Cute Dog Maymo'
    fill_in 'Description', with: 'The Ultimate Dog Shaming : Cute Dog Maymo'
    page.execute_script("$('#action').val('video')") # needed until form can submit without Content#action field
    attach_file('Thumbnail', Rails.root + 'app/assets/images/logo.png')
    click_button 'Save & Submit'

    expect(page).to have_text('Content was successfully created.')
  end
end
```

```ruby
# spec/support/features/session_helpers.rb

module Features
  module SessionHelpers
    def create_user
      user = create(:user)
    end

    def sign_in_with(email, password)
      visit '/users/sign_in'
      fill_in 'Email', with: email
      fill_in 'Password', with: password
      click_button 'Sign In'
    end

    def sign_in
      sign_in_with(user.email, user.password)
    end
  end
end
```

```ruby
# spec/spec_helper.rb

config.use_transactional_fixtures = false

RSpec.configure do |config|
  config.include Features::SessionHelpers, type: :feature
end
```

```ruby
# spec/support/database_cleaner.rb

RSpec.configure do |config|
  config.before(:suite) do
    DatabaseCleaner.clean_with(:truncation)
  end

  config.before(:each) do
    DatabaseCleaner.strategy = :transaction
  end

  config.before(:each, js: true) do
    DatabaseCleaner.strategy = :truncation
  end

  config.before(:each) do
    DatabaseCleaner.start
  end

  config.after(:each) do
    DatabaseCleaner.clean
  end
end
```

### Notes

* We set `js: true` to load Capybara since we need to execute JavaScript in the test (more on that
  below). This was what loads the browser and slowed down tests, so don't use it unless you need
  it.
* We then used the helper method `sign_in` to set up the user and sign the user in. We had a few
  helpers that let us compose the setup we needed for each test, and we can build on them to make
  our tests more readable and maintainable.
* While filling out the form, we needed to execute JavaScript to fill in a hidden field.
* After submitting, we expected the page to redirect and flash a success message.
* We also needed to change RSpec to not use transactional fixtures by default. More about that
  [here][3].

### Results

We now know if the form breaks for a normal new or update action. We can and probably will add to
these tests and we discover bugs and add new features, but we now have the plumbing in place to do
so.

[1]: http://blog.8thlight.com/kevin-liddle/2013/09/18/a-case-against-cucumber.html
[2]: https://www.relishapp.com/rspec/rspec-rails/docs/feature-specs/feature-spec
[3]: http://devblog.avdi.org/2012/08/31/configuring-database_cleaner-with-rails-rspec-capybara-and-selenium/
