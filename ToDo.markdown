# Todo

## Basics
* Add peastash gem
* Configure it
```ruby
config.peastash.enabled = true
config.peastash.source = 'peastash-talk'
config.peastash.output = Peastash::Outputs::IO.new("#{Rails.root}/log/logstash_#{Rails.env}.log")
```
* Done!

## The ELK part
* Download & unzip Logstash
* run Logstash: ``bin/logstash -f peastash-talk.conf``
* start kibana: ``bin/logstash-web`` and visit http://localhost:9292

## Now let's have some more fun!
* Add a tag to the events to find them back later:
```ruby
# app/controllers/application_controller.rb
before_action :log_additional_parameters

def log_additional_parameters
  Peastash.with_instance.store[:meetup] = 'paris.rb'
end
```
* Add the author to Blog creations:
```ruby
# config/initializers/peastash.rb
Peastash.with_instance.watch('blog.created') do |name, start, finish, id, payload, store|
  store[:author] = payload[:author]
end

# app/models/blog.rb
after_create :log_author

def log_author
  ActiveSupport::Notifications.instrument('blog.created', author: author)
end
```
