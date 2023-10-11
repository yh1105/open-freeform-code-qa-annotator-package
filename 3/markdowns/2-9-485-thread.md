
# Post \#65834575 [Link](https://stackoverflow.com/questions/65834575/)

## How to enable TLS for Redis 6 on Sidekiq?

**Vote**: 25 (228/702) **Views**: 7486 (474/702) 

**Internal ID** \#2-9-485

Created at 2021-01-21 19:36:13

Tags: `ruby-on-rails` `ruby` `ssl` `heroku` `redis`

----------

#### Metadata:

Area: `Back-end`

Language: `ruby` (full parsed tag list: `ruby`)

----------

**Notepad**


----------


## Problem


On my Ruby on Rails app, I keep getting the error below for the [Heroku Redis](https://elements.heroku.com/addons/heroku-redis) Premium 0 add-on:
> OpenSSL::SSL::SSLError: SSL_connect returned=1 errno=0 state=error: certificate verify failed (self signed certificate in certificate chain)
Heroku Redis [documentation](https://devcenter.heroku.com/articles/heroku-redis#create-a-new-instance) mentions that I need to enable TLS in my Redis client's configuration in order to connect to a Redis 6 database. To achive this, I have read SSL/TLS Support documentation on [redis-rb](https://github.com/redis/redis-rb#ssltls-support). My understanding from it is; I need to assign `ca_file`, `cert` and `key` for `Redis.new#ssl_params`.  how to set these for Redis or through Sidekiq on Heroku?

## Updates


Update 3: Heroku support provided an answer which solved the problem.
Update 2: Created Heroku support ticket and waiting response.
Update 1: [Asked](https://github.com/mperham/sidekiq/issues/4786) on Sidekiq's Github issues and was adviced go write Heroku support. Will update this question, when I do get an answer.

---



### Related Info


I have verified the app does work when the add-on is either one of the below:
- - 
Versions:
- - - - - - 
Some links that helped me to narrow down the issue:
- [https://bibwild.wordpress.com/2020/11/24/are-you-talking-to-heroku-redis-in-cleartext-or-ssl/](https://bibwild.wordpress.com/2020/11/24/are-you-talking-to-heroku-redis-in-cleartext-or-ssl/)- [https://mislav.net/2013/07/ruby-openssl/](https://mislav.net/2013/07/ruby-openssl/)


----------
        
## Answer \#0

**Accepted** Vote: 30

Created at 2021-01-22 13:08:02

------------


# Solution


Use `OpenSSL::SSL::VERIFY_NONE` for your Redis client.

### Sidekiq


```
# config/initializers/sidekiq.rb
Sidekiq.configure_server do |config|
  config.redis = { ssl_params: { verify_mode: OpenSSL::SSL::VERIFY_NONE } }
end

Sidekiq.configure_client do |config|
  config.redis = { ssl_params: { verify_mode: OpenSSL::SSL::VERIFY_NONE } }
end
```


### Redis


```
Redis.new(url: 'url', driver: :ruby, ssl_params: { verify_mode: OpenSSL::SSL::VERIFY_NONE })
```


# Reason


Redis 6 requires TLS to connect. However, Heroku support explained that they manage requests from the router level to the application level involving Self Signed Certs. Turns out, Heroku terminates SSL at the router level and requests are forwarded from there to the application via HTTP while everything is behind Heroku's Firewall and security measures.

---



- [https://ogirginc.github.io/en/heroku-redis-ssl-error](https://ogirginc.github.io/en/heroku-redis-ssl-error)- [https://devcenter.heroku.com/articles/securing-heroku-redis#connecting-directly-to-stunnel](https://devcenter.heroku.com/articles/securing-heroku-redis#connecting-directly-to-stunnel)


------------
    
    
## Answer \#1

 Vote: 8

Created at 2021-07-05 11:58:45

------------

If you use ActionCable, you may also need to add `verify_mode` to the `cable.yml config:
```
production:
  adapter: redis
  url: <%= ENV.fetch("REDIS_URL") { "redis://localhost:6379/1" } %>
  channel_prefix: my_app_production
  ssl_params:
    verify_mode: <%= OpenSSL::SSL::VERIFY_NONE %>
```

Source: [https://github.com/chatwoot/chatwoot/issues/2420](https://github.com/chatwoot/chatwoot/issues/2420)


------------
    
    
## Answer \#2

 Vote: 6

Created at 2021-07-30 16:45:58

------------

If you are using Rails 5 you [won't be able to configure](https://github.com/rails/rails/issues/42036#issuecomment-885905472) Redis's `ssl_params` for ActionCable via the `cable.yml` file. Instead you can manually set the `redis_connector` attribute in an initializer, like this:
```
# frozen_string_literal: true

require "action_cable/subscription_adapter/redis"

ActionCable::SubscriptionAdapter::Redis.redis_connector = ->(_config) do
  Redis.new(...your options here...)
end
```

And for more context on the implications of using `OpenSSL::SSL::VERIFY_NONE`  and why it's probably OK if you're on Heroku:
Using `OpenSSL::SSL::VERIFY_NONE` tells the client it's OK to work with a self-signed certificate, no attempt will be made to verify that the cert was signed by a known Certificate Authority.
The risk there is the possibility of a man-in-the-middle attack. If the client attempting to talk to Heroku Redis is not verifying that the SSL certificates it encounters are known to belong to Heroku (AKA, that those certificates are signed by a certificate authority that has verified that the entity that requested the cert is in fact Heroku), then an attacker who sits betweeen your client and Heroku Redis could create their own self-signed SSL certificate and pretend to be Heroku. This means they could potentially intercept any traffic you attempt to send to Heroku Redis.
In practice that is probably not a realistic scenario for a Heroku dyno talking to Heroku Redis.
Here's a quote from Heroku support:
> MITM attacks are impractical on managed hosting providers. It would be
difficult for a bad actor to get between a dyno and a Redis instance
hosted on AWS. This is because EC2 instances that are in the same AZ
should not route outside of the AWS infrastructure at any point. This
being the case, a MITM attack could only be performed by a bad actor
within the managed service provider's facility itself since the
network traffic never leaves said facility.
And here are some snippets from the AWS docs that seem to corroborate this:
[https://aws.amazon.com/vpc/faqs/](https://aws.amazon.com/vpc/faqs/)
> Q. Does traffic go over the internet when two instances communicate
using public IP addresses, or when instances communicate with a public
AWS service endpoint?No. When using public address space, all communication between
instances and services hosted in AWS use AWS's private network.
Packets that originate from the AWS network with a destination on the
AWS network stay on the AWS global network, except traffic to or from
AWS China Regions.In addition, all data flowing across the AWS global network that
interconnects our data centers and Regions is automatically encrypted
at the physical layer before it leaves our secured facilities.
Additional encryption layers exist as well; for example, all VPC
cross-region peering traffic, and customer or service-to-service
Transport Layer Security (TLS) connections.
And [https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-vpc.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-vpc.html)
> Amazon Virtual Private Cloud (Amazon VPC) enables you to define a
virtual network in your own logically isolated area within the AWS
cloud, known as a virtual private cloud (VPC).When you create your AWS account, we create a default VPC for you in
each Region


------------
    
    