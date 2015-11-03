# Extract methods to concerns easily

A very easy way to extracts methods away from controllers and models is to use concerns.

```ruby
module TopicTracker
  extend ActiveSupport::Concern
  
  def completed_topics_tracker
    session[:completed_topics] ||= []
  end
end

class OpinionsController < ApplicationController
  include TopicTracker

  def create
    # method in concerns is available now
    completed_topics_tracker << params.require(:topic_id)
  end
end
```
