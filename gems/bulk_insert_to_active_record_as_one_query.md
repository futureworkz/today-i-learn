# Bulk Insert to database with just one Query

By chances, I stood upon this gem call `BulkInsert`.

As ActiveRecord doesn't cater to multiple inserting of query into one query.

This gem consolidate all the query into one single query and insert to the database.

Use-case for this, I can think of dbseed and import function where there a need to insert multiple query for the same model.

More information is available at [Github](https://github.com/jamis/bulk_insert) on how to use it etc..
