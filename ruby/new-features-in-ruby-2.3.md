# New features in ruby 2.3

This update brings several new additions to core classes in ruby as well as some new syntax. Here are a few of the new additions coming in ruby 2.3:

### 1. Extract values with Array#dig and Hash#dig

The new #dig instance methods provide concise syntax for accessing deeply nested data. For example:

```ruby
user = {
  user: {
    address: {
      street1: '123 Main street'
    }
  }
}

user.dig(:user, :address, :street1) # => '123 Main street'

results = [[[1, 2, 3]]]

results.dig(0, 0, 0) # => 1
```

Both of these methods will return nil if any access attempt in the deeply nested structure returns nil:
```ruby
user.dig(:user, :adddresss, :street1) # => nil
user.dig(:user, :address, :street2) # => nil
```

### 2. Grep out the inverse of a pattern with Enumerable#grep_v

This method is the inverse of the Enumerable#grep method. The grep method and its inverse provide several powerful ways to filter enumerables:
Filtering by regular expression

```ruby
friends = %w[John Alain Jim Delmer]

j_friends = friends.grep(/^J/)   # => ["John", "Jim"]
others    = friends.grep_v(/^J/) # => ["Alain", "Delmer"]
```

Filtering by types
```ruby
items = [1, 1.0, '1', nil]

nums   = items.grep(Numeric)   # => [1, 1.0]
others = items.grep_v(Numeric) # => ['1', nil]
```

### 3. Fetching multiple values with Hash#fetch_values

Sometimes Hash#fetch is a better choice than Hash#[] when you want to write more strict code. You can also access multiple values from a hash using Hash#values_at, but there wasn’t a strict equivalent to values_at until ruby 2.3:

```ruby
values = {
  foo: 1,
  bar: 2,
  baz: 3,
  qux: 4
}

values.values_at(:foo, :bar)    # => [1, 2]
values.fetch_values(:foo, :bar) # => [1, 2]

values.values_at(:foo, :bar, :invalid)    # => [1, 2, nil]
values.fetch_values(:foo, :bar, :invalid) # => KeyError: key not found: :invalid
```

### 4. Positive and negative predicates for Numeric#positive? and Numeric#negative?
Numeric values now have predicate methods that check if the subject is positive or negative. This can be useful if you want to filter an enumerable:
```ruby
numbers = (-5..5)

numbers.select(&:positive?) # => [1, 2, 3, 4, 5]
numbers.select(&:negative?) # => [-5, -4, -3, -2, -1]
```

### 5. Hash superset and subset operators Hash#<=, Hash#<, Hash#>=, and Hash#>
These methods lets you compare hashes to see if they are subsets or proper subsets of each other. For example:
```ruby
small     = { a: 1                }
medium    = { a: 1, b: 2          }
large     = { a: 1, b: 2, c: 3    }
different = { totally: :different }

{ a: 1, b: 2 } > { a: 1 }             # => true
{ a: 1 } > { a: 1 }                   # => false
{ b: 1 } > { a: 1 }                   # => false
{ a: 1, b: 2 } < { a: 1, b: 2, c: 3 } # => true
```

### 6. Convert a hash to a proc with Hash#to_proc
Now you can use a hash to iterate over an enumerable object:
```ruby
hash = { a: 1, b: 2, c: 3 }
keys = %i[a c d]

keys.map(&hash) # => [1, 3, nil]
```

Avoid nil related errors with the safe navigation operator

### 7. New syntax for accessing deeply nested objects safely without accidentally triggering a dreaded NoMethodError on nil. 
The syntax looks like this:
```ruby
require 'ostruct'
user&.address&.street&.first_line
```

where each instance of &. is similar to ActiveSupport’s Object#try method. Basically, if a nil value is encountered, then each method call will not be attempted and instead the nil value will be returned immediately.
Experimental frozen string pragma

You’ve probably heard that strings will be frozen by default in ruby 3. Ruby 2.3 lets you specify a pragma which enables this by default:
```ruby
$ ruby -v
ruby 2.3.0preview1 (2015-11-11 trunk 52539) [x86_64-darwin14]
$ cat default.rb
# frozen_string_literal: false

puts "Hello world".reverse!
$ ruby default.rb
dlrow olleH
$ cat enabled.rb
# frozen_string_literal: true

puts "Hello world".reverse!
$ ruby enabled.rb
enabled.rb:3:in `reverse!': can't modify frozen String (RuntimeError)
  from enabled.rb:3:in '<main>'
```
Alternatively, you can also enable and disable this using the command line argument --enable=frozen-string-literal