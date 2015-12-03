# One liner To Get First and Last Elements In An Array

Here's cool way to get the first and last elements of an array _in one liner_ using splat:

```ruby
first, *, last =  [1, 2, 3, 4, 5]
first # => 1
last # => 5
```
