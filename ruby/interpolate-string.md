# Interpolate string, not concatenate

```ruby
# concatenate
puts “error description” + e.cause.to_s + “happened on: ” + Time.now.to_s"
```

When we concatenate a string,
- the code is longer to read
- we need to #to_s on the variable to avoid errors
- we type longer

```ruby
# interpolate
puts "error description #{e.cause} happened on: #{Time.now}”
```

By interpolating the string,
- the code is shorter and easier to read
- interpolate will automatically #to_s
- the code is safer to run
- we are happier
