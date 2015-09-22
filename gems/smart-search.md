# Smart Search - learn by users behavior

Searchkick gem is a gem that learns what your users are searching, as more people search, it will get smarter and result will be more accurate. However it only works well with `elasticsearch`

Searchkick handles the following:
- Steamming - `tomatoes` matches `tomato`
- Special Characters - `jalapeno` matches `jalapeño`
- extra whitespace - `dishwasher` matches `dish washer`
- misspellings - `zuchini` matches `zucchini`
- custom synonyms - `qtip` matches `cotton swab`

With addition to this gem, you can also use `searchjoy` gem to track queries and conversion.

I believe this gem will be able to bring the search function to the next level.

For more information, visit the [Searchkick](https://github.com/ankane/searchkick) and [SearchJoy](https://github.com/ankane/searchjoy)
