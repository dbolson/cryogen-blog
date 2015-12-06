{:title "Rails controller tests without Rails"
:layout :post
:tags ["rails" "ruby" "testing"]}

In our continued journey to faster tests, we've tried doing controller tests without loading Rails
and using only what's minimally required to fully test the functionality. The idea is that not
only will the tests be faster, but they will drive the controller's design to be [thinner][1].

How did it work out? First the background.

We implemented a controller that received an [OAuth][2] callback from another site to allow their
users to directly login to Crowdcast. The required code was minimal, so we tried to test minimally
as well.

Since we're not loading Rails, we need to manually load some required libraries for the
controller. We need enough to load the controller and its dependencies (in this case, a URL
generator class and the controller itself) successfully.

```ruby
require 'uri'
require 'active_support'
require 'fast_spec/spec_helper'
require 'lib/crowdcast/url_generator'
require 'app/controllers/oauth_controller'
```

We also need to stub out calls to models, both `ActiveRecord` and plain old Ruby objects the
controller uses. Here is where we realize how many of these we need and use the pain of stubbing
yet another class to drive the design to remove that dependency.

```ruby
stub_class 'UserSession'
stub_active_record_model 'Site'
stub_active_record_model 'SalesforceConnection'
```

Some are necessary, such as those for [Authlogic][3] and the relevant AR classes we're calling,
but we were able to move calls to others out of the controller to make the tests easier to write
and the code easier to read and maintain. All the usual good stuff we get from TDD and OOP.

Here's an example:

```ruby
describe OauthController do
  let(:controller) { OauthController.new }

  describe "#callback" do
    let(:user_attributes)
    let(:user) { stub("user", :single_access_token => "foo") }
    let(:site) { stub("site") }
    let(:redirect_to_url) { "https://foo.example.com:8080?foo=bar&baz=quux" }
    let(:state) { CGI.escape({:url => redirect_to_url, :subdomain => "foo" }.to_json) }

  before do
    controller.stub(:params)
    controller.stub(:current_user) { user }
    controller.stub(:session)
    Crowdcast::Config.stub(:salesforce)

    Salesforce::UserResolver.stub(:user_attributes_by_auth_code).with({ :id => "bar" }, "foo").and_return(user_attributes)
    SalesforceConnection.stub(:connect).and_return(user)
    Site.stub(:find_by_subdomain).with("foo").and_return(site)
  end

  it "creates a SalesforceConnection connection" do
    SalesforceConnection.should_receive(:connect).once.with(user, user_attributes)
    controller.callback
  end

  it "redirects to the return_to path with user's single access token" do
    controller.should_receive(:redirect_to).with(redirect_to_url + "&token=foo")
    controller.callback
  end
end
```

You can see that we need a fair amount of initial setup to get both the regular methods all
controllers access and the specific ones we want to test or stub in the specific tests. It's still
feels reasonable considering that we lose the Rails loading overhead and tests are incredibly
fast.

Here is the controller:

```ruby
def callback
  if params[:code]
    cc_user = connect_user_to_salesforce
    redirect_to Crowdcast::UrlGenerator.url(:url => return_to_url_from_callback, :params => { :token => cc_user.single_access_token })
  else
    flash[:error] = params[:error_description] if params[:error_description]
    redirect_to return_to_url_from_callback
  end
end

private

def connect_user_to_salesforce
  SalesforceConnection.connect(existing_or_autoregistered_user)
end

def existing_or_autoregistered_user
  current_user || Salesforce::UserAutoRegistrar.register(current_site)
end

def return_to_url_from_callback
  state_from_callback["url"]
end

def state_from_callback
  JSON.parse(CGI.unescape(params[:state]))
end
```

And now a problem.

We want to add some more Rails controller goodness in case there are exceptions (they're always
where you [least expect them][4]). Check this out.

```ruby
rescue_from Salesforce::UserResolver::UnsupportedAccountTypeError, :with => :account_error
rescue_from Salesforce::TokenResolver::AuthCodeExpiredError, :with => :expired_auth_code

def account_error
  render :status => :precondition_failed, :action => "account_error", :layout => "not_found"
end
```

Now we need to figure out how to get `rescue_from` or have more stubbing on the controller class.
Before, when our controller was very lightweight, we could deal with the minimal amount of manual
dependencies to get the speed increases. But at this point we decided to convert the controller to
our "slow specs" folder, ie, our regular spec folder, since the pain of managing the external
dependencies reached a threshold we weren't willing to cross.

How did we decide this was the time? It wasn't anything specific but the overall feel of the code
getting too complicated and stub-happy; we weren't getting pain from bad design but from using a
framework.

Conclusions

Testing without Rails is still new, and we're still learning what works and what doesn't. Should
we automatically test functionality that's coupled to the framework within the framework? I still
say no, that we can get out of the framework if we use only a minimal subset that we can maintain
manually. We decided to return to loading Rails when that subset was no longer minimal. That
situation did not come up for some time, and it isn't a foregone conclusion that it always will.
It's a developer's decision on the tradeoffs. Plus it was a great learning experience.

[1]: http://weblog.jamisbuck.org/2006/10/18/skinny-controller-fat-model
[2]: http://oauth.net/
[3]: https://github.com/binarylogic/authlogic
[4]: http://www.youtube.com/watch?v=vt0Y39eMvpI&t=45s
