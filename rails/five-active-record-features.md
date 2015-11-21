# Five Active Record Features You Should Be Using
### 1. Nested Queries
When doing database queries, the fewer the better. Since Active Record is responsible of crafting these queries, it is important to make sure it has all the help it needs. For simple queries this is rarely an issue, but more complex requirements can lead to sub-optimal results.

One day, Tim from sales comes rampaging through the office convinced that there must be a bug in the system. A recent sale for "booksandreviews.com" did not go well and he wants answers. Tim wants an analysis run. He wants all reviews published today, which are for books published in 2015.

Without too much thought, this approach seems reasonable:
```ruby
book_ids = Book.where(publish_year: '2015').map(&:id)
# => SELECT "books".* FROM "books" WHERE (publish_year = '2015')

reviews = Review.where(publish_date: '2015-11-15',
                       book_ids: book_ids).to_a
# => SELECT "reviews".* FROM "reviews" WHERE "reviews"."publish_date" = '2015-11-15' AND "reviews"."book_ids" IN (1, 2, 3)
```

This will load the desired books, extract their id's, and pass that result to the Review query. Not only does this generate two queries, it also wastes memory by creating an array of Book objects to map over and then another array of book_ids. With a large enough list of books, this could cause some serious problems.

Active Record's where method returns an instance of ActiveRecord::Relation. These relations can be passed to other methods to aid in query construction. With the same request, we can save the map and array creation:
```ruby
books = Book.where(publish_year: '2015')
# => ActiveRecord::Relation

reviews = Review.where(publish_date: '2015-11-15', book: books).to_a
# SELECT "reviews".* FROM "reviews" WHERE "reviews"."publish_date" = '2015-11-15' AND "reviews"."book_id" IN (SELECT "books"."id" FROM "books" WHERE "books"."publish_year" = '2015')
```
This still executes two SELECT statements but it nests them to let the database take care of memory allocation and optimization. The book_ids array is replaced with the books relation and is passed to the Review query.

Note: This can reduced to a single query with .joins, but for now we can assume that a nested query is desired.

### 2. DRY Scopes
Still fuming, Tim demands more information. Now he wants to know the list of all Books published in 2015 which have at least one approved Review. Since Reviews are subjective, they need to be approved in order to maintain the quality that "booksandreviews.com" is known for.

Luckily, a scope has been written on the Review class to accomplish this.
```ruby
class Review < ActiveRecord::Base
  belongs_to :book
  scope :approved, ->{ where(approved: true) }
end
```
However, it is Books that we need to return, not Reviews. Repeating the scope definition, a join query can be used for this analysis:
```ruby
books = Book.where(publish_year: '2015')
            .includes(:reviews)
            .references(:reviews)
            .where('reviews.approved = ?', true )
            .to_a
# => SELECT #long books and reviews column select# FROM "books" LEFT OUTER JOIN "reviews" ON "reviews"."book_id" = "books"."id" WHERE "books"."publish_year" = '2015' AND (reviews.approved = 't')
```
Books are returned at the cost of duplicating the approved scope. That means that the scope in Review changes, this code will not benefit from that change. The .includes and .references methods are used to ensure that we only return one Book (in the case of many Reviews belonging to the same Book).

The Don't Repeat Yourself (DRY) principle was created for this exact reason. When identical code is not shared and instead repeated, changes to one version can have dangerous consequences on the other.

The good news is that Active Record provides precisely the medicine for this ailment: .merge.

With .merge, an existing scope can be used in another Active Record query.
```ruby
books = Book.where(publish_year: '2015')
            .includes(:reviews)
            .references(:reviews)
            .merge(Review.approved)
            .to_a
# => SELECT #long books and reviews column select# FROM "books" LEFT OUTER JOIN "reviews" ON "reviews"."book_id" = "books"."id" WHERE "books"."publish_year" = '2015' AND (reviews.approved = 't')
```
Great! Now the results are the exact same and the code is DRY.

### 3. where.not
Typical insatiable Tim is back with yet another request to add to the brand new "totally not vanity metrics dashboard". Now, he wants to know all the books not published in 2012.

Without even asking why such a silly request is necessary, some more code can be cranked out:
```ruby
books = Book.where('publish_year != 2012').to_a
# => SELECT "books".* FROM "books" WHERE (publish_year != '2012')
```
Like before, this code works but could be better. There is some raw SQL in there that the next developer might not understand well enough to manipulate. Whatever the reason, it is best to rely on abstraction instead of explicit SQL.

To help solve this dilemma, the .not modifier has been introduced in Active Record 4.0.
```ruby
books = Book.where.not(publish_year: 2012).to_a
# => SELECT "books".* FROM "books" WHERE (publish_year != '2012')
```
The result is the same but look how much nicer that is. Not only is the raw SQL gone, the code is more positive too.

### 4. first and take
Since "booksandreviews.com" has been around since 2012, chances are it upgraded from Ruby on Rails 3.0 to 4.0. One notable change from Active Record 3 to 4 is the behaviour of .first.

In Ruby on Rails 4.0+, the .first method returns the first row after the table has been ordered by its id.
```ruby
Author.where(first_name: 'Bill').first
# => SELECT  "authors".* FROM "authors" WHERE "authors"."first_name" = "Bill" ORDER BY "authors"."id" ASC LIMIT 1
```
This will work fine for every table that has an id column. However, if a table does not need an id column, this method causes a problem.

Despite each Author having an id, complex joins might cause an issue with an implicit ORDER BY on queries.

To alleviate that problem, the take method can be used instead of first:
```ruby
Author.where(first_name: 'Bill').take
# => SELECT  "authors".* FROM "authors" WHERE "authors"."first_name" = "Bill" LIMIT 1
```
This behaves in a much more explicit way, returning the same information without a default ordering.

### 5. .unscoped
During the development life of "booksandreviews.com", countless modules have been built and gems included. Amidst this chaos, someone must have typed gem install hairball and horribly altered the Author class. This has led to the new guy Mike's complaint that: "Authors are missing data".

Mike knows that authors have a first_name but for some reason it is not being returned:
```ruby
authors = Author.where(last_name: 'Smith').take(5)
authors.map(&:first_name)
# => [nil, nil, nil, nil, nil]
```
What Mike does not know is that one of those hairball gems added a default scope to all Active Record objects that begin with the letter "A". However impossible this bug is, it exists and it is ruining Mike's day.

What Mike needs is the .unscoped method. This method removes all existing scopes on an Active Record relation.
```ruby
authors = Author.unscoped.where(last_name: 'Smith').take(5)
authors.map(&:first_name)
# => ['Frank', 'Frank', 'Jim', 'Frank', 'Frank']
```
(Is anyone else concerned that there are four Frank Smith authors?)

With the .unscoped method, all harmful default scopes are removed and the Franks are free.

Queries on Queries
With these five techniques (and probably a lot more), naiive Active Record queries can stay DRY and intuitive. The exhaustive list of what Active Record can provide can be found at Rails Guides.