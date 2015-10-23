# Shoulda-matcher 3.0.0 errors with fix

I'm pretty sure many of us meet with the following errors when using the latest Shoulda-matchers 3.0.0

```ruby
NoMethodError:
       undefined method `validate_presence_of' for #<RSpec::ExampleGroups ....`
```

It make it seem like the whole of `shoulda-matchers` is not working.

However that not the case, thoughtbots changed the way of how `Shoulda-matchers` loads

Before version 3.0.0, they will detect what test framework you are using, like `rspec-rails`, `minitest`, `test_unit` etc

After facing much bugs and issues, they decided to launch version 3.0.0 which now they will not detect which test framework you are using.

Instead they require the us to tell `shoulda-matchers` which test framework to load

Below is the following solution

```ruby
Shoulda::Matchers.configure do |config|
  config.integrate do |with|
    with.test_framework :rspec
    with.library :rails
  end
end
```

We will now need to declare which framework to use and which library to load.

More information on which framework or library it supports is available at [Thoughtbot](https://robots.thoughtbot.com/shoulda-matchers-3-0) or [Documents](http://matchers.shoulda.io/docs/v3.0.0/)
