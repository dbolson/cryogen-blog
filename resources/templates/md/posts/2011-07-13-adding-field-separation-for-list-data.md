{:title "Adding Field Separation for List Data"
:layout :post
:tags ["metaldetectr" "modularity" "rails" "ruby"]}

[MetalDetectr](https://github.com/dbolson/metaldetectr) is effectively a list of data as specific
as a user wishes to see. It will show only a list of releases a user has in his [last.fm](http://www.last.fm/)
library to a list of everything on [metal-archives.com](http://www.metal-archives.com/). A big
concern is presenting it properly, and one method is to delineate releases by whatever sort method
a user wants to see. This can be by release date, by the band's name, by the release's name, or by
the release's format (eg, EP, full-length, DVD).

## The algorithm

* Start with a table row showing the earliest or most recent, depending on sort order, of the
selected sort column.
* Loop through the releases.
* If the current release's relevant field is greater/less than the preceding one, show another
table row with the current release's field value.
* Show the release.

For example, the default sort is by US release date starting at the earliest date (and the current
month so there's less noise). The list will display the current month and every album released
during that month. When a release is next month, it will show next month and then all releases
from that month. Continue on through the rest of the releases. If the user wants to see the list
in descending order, it will show the last month first and work its way to the most current month.

## The code

First find the first value and display it in a full column span table row:

```
# views/releases/index,html.haml
- comparison_value = @releases.first.chain_methods(Release::FIELDS_WITH_METHODS[Release.default_sort(params[:s])])
= separator_row(comparison_value)
```

These two lines use the following methods:

```ruby
# models/release.rb
# Sets the sort order to what's passed or us_date.
def self.default_sort(sort)
  sort || 'us_date'
end

# models/release.rb
# Takes an array of symbols and calls them on the release instance if it
# responds to them.
# Example: release.chain_methods([:us_date, :month]) => release.us_date.month
def chain_methods(methods)
  methods.inject(nil) do |memo, acc|
    target = memo ? memo : self
    target.respond_to?(acc) ? target.send(acc) : memo
  end
end

# helpers/releases_helper.rb
# Creates a row with a full colspan for the value.
def separator_row(value)
  value = Date::MONTHNAMES[value] if value.is_a?(Fixnum)
  content_tag(:tr, :class => cycle('even', 'odd')) do
    content_tag(:td, value, :class => 'separator_row', :colspan => 7)
  end
end
```

`FIELDS_WITH_METHODS` is a constant that contains a mapping of field names and methods to call on
them to display properly:

```ruby
# models/release.rb
FIELDS_WITH_METHODS = {
  'band' => [:band, :first, :downcase],
  'name' => [:name, :first, :downcase],
  'us_date' => [:us_date, :month],
  'euro_date' => [:euro_date, :month],
  'format' => [:format],
  nil => [:us_date, :month]
}
```

Then we loop through each release, updating the comparison value when we get to the next one:

```
# views/releases/index,html.haml
- @releases.each do |release|
  - current_value = release.chain_methods(Release::FIELDS_WITH_METHODS[Release.default_sort(params[:s])])
  - if Release.values_compared?(current_value, comparison_value, params[:d])
    - comparison_value = current_value
    = separator_row(comparison_value)
  - else
    - comparison_value = current_value
  = render release
```

Compare the two values based on the sort order:

```ruby
# models/release.rb
# Sets the comparison operator to be greater than if the direction is nil or ascending,
# or less than if the direction is descending.
def self.comparison_operator(direction)
  (direction.nil? || direction == 'asc') ? :> : :<
end

# models/release.rb
# True if both value and comparison exist and
# if the direction is ascending:
#   true if value > comparison, false otherwise
# if the direction is descending:
#   true if value < comparison, false otherwise
def self.values_compared?(value, comparison, direction)
  value &&
  comparison &&
  value.send(
    Release.comparison_operator(direction),
    comparison
  )
end
```

We tried to abstract the comparisons and what's displayed so we can add new fields and only need
to update the field-method mapping. There is always the possibility that a field is nil, since we
don't always get all the possible data for every release, so `chain_methods` will call all the
methods it can on a release instance until it finishes or returns nil. We could have chained a
bunch of `try`s together, but that didn't look right.

We also tried to get as much code out of the view as we could, and it can be improved, but it's
okay for now.
