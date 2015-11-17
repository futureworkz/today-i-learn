# Cache Control in Rails

Whenever we load a page in browser, it will stores a cached version of the page.

So if users press the back buttons, the browser will simply load the cache version of the page.

This can be an issues when there sensitive information involve.

We can simply set the header `Cache-Control` in our `application_controller.rb`

```ruby
#application_controller
  before_filter :set_as_private

  protected

  def set_as_private
    response.headers['Cache-Control'] = 'no-cache, no-store'
  end
```

In the case where you only need it in specific page, just use the `before_filter` in respective controller.
