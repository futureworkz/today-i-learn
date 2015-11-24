# Errors In Test Cases Creates Floating Test Data

If an exception is thrown when running your test cases, the test data created will NOT be rolled back.  
This creates "floating" test data in your test database will may impact other test cases.

The most common solution is to use the gem database_cleaner which basically clears out the entire test database
whenever you start the test cases.  
However, this makes your test cases run slower!  
Furthermore, database_cleaner gem also don't address any test data leakage in your test cases.

The easiest solution is to run `rake db:test:prepare` in your terminal.  
This resets the entire test database so that there are no more floating test data in your test database.

Every time you have a floating test data issue, you should run `rake db:test:prepare` 
instead of using a slow solution like database_cleaner.
