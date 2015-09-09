# Date Output simple date formatter

By default the date is always shown as MM/DD/YYYY, almost every single time we will have to format it to DD/MM/YYYY.

By using `strftime_format "%d-%m-%y"` will be able to get the job done easily, but it always look slightly off as a whole due to its naming convention.

Date Output is a simple gem that allow us to format in a clear and clean manner, with minor customization.

## Most common date formats

```ruby
numbered_date_with_time(date) #=> 28/03/2013 14:42pm

numbered_date(date) #=> 28/03/2013

long_date_no_day(date) #=> 28th March 2013

short_date_no_day(date) #=> 28th Mar 13

long_date_no_day_with_time(date) #=> 28th March 2013 14:42pm

short_date_no_day_with_time(date) #=> 28th Mar 13 14:42pm
```

For more information, visit the [Github](https://github.com/craigsheen/date_output) 
