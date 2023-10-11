
# Post \#66246528 [Link](https://stackoverflow.com/questions/66246528/)

## Herkou Redis - certificate verify failed (self signed certificate in certificate chain)

**Vote**: 17 (290/702) **Views**: 5303 (514/702) 

**Internal ID** \#2-9-487

Created at 2021-02-17 16:57:35

Tags: `ruby-on-rails` `heroku` `redis` `heroku-redis`

----------

#### Metadata:

Area: `Back-end`

Language: `ruby` (full parsed tag list: `ruby`)

----------

**Notepad**


----------

I have been using heroku redis for a while now on one of my side projects. I currently use it for 3 things

1. It serves as a place for me to store firebase certificates
2. It is used for caching data on the site
3. It is used for rails sidekiq jobs


Recently, my heroku usage went up and I had to change it to use heroku redis premium plan. Ever since then I have been getting `error: SSL_connect returned=1 errno=0 state=error: certificate verify failed (self signed certificate in certificate chain)` somehow. Everything stayed the same yet the error started popping out of nowhere.
Does heroku-redis premium plan work fundamentally different than a basic heroku-redis plan?
I am using ruby on rails, deployed on heroku with heroku redis if that helps.


----------
        
## Answer \#0

**Accepted** Vote: 12

Created at 2021-02-19 22:54:55

------------

Actually, when you install the Heroku Redis on your heroku app, it will create for you 2 Config Vars : REDIS_TLS_URL and REDIS_URL.
The docs are actually incorrect, you have to set SSL to verify_none because TLS happens automatically.
From Heroku support:
> "Our data infrastructure uses self-signed certificates so certificates
can be cycled regularly... you need to set the verify_mode
configuration variable to OpenSSL::SSL::VERIFY_NONE"


------------
    
    
## Answer \#1

 Vote: 13

Created at 2021-08-19 21:23:48

------------

According to Heroku's [docs](https://devcenter.heroku.com/articles/heroku-redis#connecting-in-ruby)
You need to
> Create an initializer file named config/initializers/redis.rb
containing:
```
$redis = Redis.new(url: ENV["REDIS_URL"], ssl_params: { verify_mode: OpenSSL::SSL::VERIFY_NONE })
```

Also if you are having this issue while attempting to use sidekiq:
> Create an initializer file named config/initializers/sidekiq.rb containing:
```
Sidekiq.configure_server do |config|
  config.redis = { ssl_params: { verify_mode: OpenSSL::SSL::VERIFY_NONE } }
end

Sidekiq.configure_client do |config|
  config.redis = { ssl_params: { verify_mode: OpenSSL::SSL::VERIFY_NONE } }
end
```



------------
    
    
## Answer \#2

 Vote: 3

Created at 2022-08-25 22:46:54

------------

@ricks answer got me 90% of the way there but for anyone like me who is using Rails.cache with Heroku's Redis, you don't need that initializer, just this config line in production.rb:
```
config.cache_store = :redis_cache_store, { url: ENV['REDIS_URL'], ssl_params: { verify_mode: OpenSSL::SSL::VERIFY_NONE } }
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-12-16 05:11:24

------------

For some other users come to this question, there might be the same issue I experienced today. Error message is the same from Heroku, but it was related to Hotwire and `config/cable.yml` file. The error goes away when you add following to that file.
```
production:
  adapter: redis
  url: <%= ENV.fetch("REDIS_URL") { "redis://localhost:6379/1" } %>
  channel_prefix: name_production
  ssl_params:
    verify_mode: <%= OpenSSL::SSL::VERIFY_NONE %>
```



------------
    
    