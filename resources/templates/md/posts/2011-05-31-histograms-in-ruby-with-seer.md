{:title "Histograms in Ruby With Seer"
:layout :post
:tags ["open source" "ruby"]}

Google has a robust set of charts through their [visualization API](https://developers.google.com/chart/)
that they use for Analytics, so when I needed a solution to graph a histogram, I turned to a Ruby
implementation of Google Charts.

There are [other options](https://www.ruby-toolbox.com/categories/graphing.html),
but most have little activity. The [Seer](https://github.com/CoralineAda/seer) gem seems to
provide enough power to get the data I need, providing a decent set of configuration options and a
lot of simplicity.

To get started, add this bit of JavaScript to the head of the page:

```javascript
Seer::init_visualization
```

In the view (preferably a view helper), add this:

```ruby
begin
  Seer::visualize(
    question.data_points,
    :as => :column_chart,
    :in_element => 'histogram',
    :series => {
      :series_label => 'label',
      :data_method => 'percentage'
    },
    :chart_options => {
      :max => 100,
      :min => 0,
      :height => 355,
      :width => 488,
      :is_3_d => false,
      :legend => 'none',
      :colors => "[{color:'#990000', darker:'#660000'}]",
      :title => 'Best Estimates',
      :title_x => 'Numbers',
      :title_y => 'Percent'
    })
rescue ArgumentError => e
  show_no_data_message_for_histogram
end
```

Here, we provide a `Struct` (`question.data_points`, which I'll get to later) to the column chart
(as per Google's API) to render in a div with a histogram id. The `series_label` and `data_method`
names are important, as they are the two methods the `Struct` responds to and provide the x- and
y-axis data respectively. There are a fair amount of options to play with. Notice that visualize
will raise an exception if there is no data instead of just rendering a blank chart, so we need to
catch that and display something else. I put a message to tell the user as much:

```ruby
def show_no_data_message_for_histogram
  content_tag(:div),
    content_tag(:h1, 'There is not enough data to display the Crowd Belief chart'),
    :id => 'empty_histogram_text'
  )
end
```

The interesting part is packaging up the data into the `Struct`. Bear in mind that this code is not
totally clean and refactored, but the test coverage will make it a lot easier to do so later.

```ruby
class DataPointsContainer
  class DataPoint < Struct.new(:label, :percentage); end
end
```

That's all we need to get the Seer visualization working, so now we can provide `DataPoint` with
some data points. As background, a `Question` has a numerical answer, and we want to get a set of
data points where each point is a range of equal size containing the answers. We will show up to
11 points, depending on the size of the range (not the amount of data) so the graph doesn't look
too bare or too cluttered. One last fun wrinkle is dealing with outlier data. We decided we don't
want to show those data points as separate pieces of data but absorb them into the first and last
points of the chart. To calculate the range without the outliers, we first calculate the mean and
the standard deviation, and we return two lists: one with the outliers (those above or below 1.96
standard deviations from the mean or ~5% and 95%).

```ruby
def outliers_and_kept_answers(all_answers)
  mean = mean(all_answers)
  stddev = stddev(all_answers, mean)

  all_answers.partition do |i|
    i > high_threshold(all_answers, mean, stddev) ||
    i < low_threshold(all_answers, mean, stddev)
  end
end

def low_threshold(numbers, mean, stddev)
  mean - (stddev * STDDEV_FACTOR).round
end

def high_threshold(numbers, mean, stddev)
  mean + (stddev * STDDEV_FACTOR).round
end
```

Now we can find the smallest and largest points of the new range of data without worrying about
the outliers messing up everything. We start with the first data point as the lowest in the range
and add the width of each range to get the highest point:

```ruby
low_point = answers.minimum
high_point = low_point + range_width(answers.minimum, answers.maximum, amount_of_points)

def range_width(min, max, points_size)
  width = ((max - min).to_f / points_size.to_f)
  width = 1.0 if width < 1.0 && @question.precision.to_i == 0
  width
end
```

For each data point (up to 11), we create a `DataPoint`, set the low to the current high, and find
the new high by adding the width range to the current low (which is the old high). If we're on the
last data point, use the last number in the range instead. When creating the `DataPoint`, we
calculate the percentage by doing a SQL count of all answers within the range and grouping by the
value:

```ruby
answers.count(
  :conditions => 'value >= #{low} AND value < #{high}",
  :order => 'value ASC',
  :group => 'value'
)
```

Finally, we add the additional outliers to either the first or last `DataPoint`, if the outliers
exist:

```ruby
if index == 0
  additional = outliers.count { |x| x < low }
end

if index == total_points_count - 1
  additional = outliers.count { |x| x > high }
end

amount += additional
```


The only thing left to do is to give the Question model access to the data points in its class:

```ruby
def data_points
  DataPointsContainer.new(self).data_points
end
```

A downside to this gem is that there is [not](https://www.ruby-forum.com/topic/996227) a
[lot](https://groups.google.com/forum/?fromgroups#!topic/rubyonrails-talk/5ULCgyWU7Xk) of
[discussion](http://stackoverflow.com/questions/4624601/seer-gem-usage-showing-errors) about it,
but I found that is also true for other solutions. Google does provide more options than the gem
currently offers, but I haven't had a need for these yet, and I can fork and add the functionality
later if needed. The only frustration so far is on Google's part, because there are some options
(removing the pop up bubbles or editing their contents) that aren't available.
