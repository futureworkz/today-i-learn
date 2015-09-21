# What is the different between Time.zone.now and DateTime.now

```ruby
# application.rb:
class Application < Rails::Application
  config.time_zone = 'Singapore'
end

Time.zone.now  # => Mon, 21 Sep 2015 10:11:22 SGT +08:00
DateTime.now   # => Mon, 21 Sep 2015 09:11:20 +0700 (Your localhost timezone)
```