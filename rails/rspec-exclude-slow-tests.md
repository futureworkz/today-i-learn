# RSpec Exclude Slow Tests

Capybara tests are slow especially if your app uses a lot of CSS and JS.

To exclude capybara tests that running with Javascript, you can simply use

```
$ rspec -t ~js
```

You can also add it to your `rails_helper.rb` so that it will not run `js: true` or `slow: true` tests by default.

```
# rails_helper.rb
config.filter_run_excluding(:slow, :js) unless ENV['ALL']
```

When you run `rspec` now, it will not run the test cases where `js: true` or `slow: true`.
To run the full test, you can run `ALL=1 rspec`.
