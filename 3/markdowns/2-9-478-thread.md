
# Post \#69229112 [Link](https://stackoverflow.com/questions/69229112/)

## Error when trying to install devise in Ruby on Rails 7.0.0

**Vote**: 11 (370/702) **Views**: 2809 (578/702) 

**Internal ID** \#2-9-478

Created at 2021-09-17 20:23:06

Tags: `ruby-on-rails` `ruby` `devise` `rubygems`

----------

#### Metadata:

Area: `Back-end`

Language: `ruby` (full parsed tag list: `ruby`)

----------

**Notepad**


----------

I'm trying to install devise in the rails version I get the error of the latest version of devise
I have tried with other devise versions but it is update it to the latest,
this is the error:
```
/usr/local/bundle/gems/devise-4.8.0/lib/devise.rb:321:in `ref': undefined method `reference' for ActiveSupport::Dependencies:Module (NoMethodError)
web_1  |    from /usr/local/bundle/gems/devise-4.8.0/lib/devise.rb:340:in `mailer='
web_1  |    from /usr/local/bundle/gems/devise-4.8.0/lib/devise.rb:342:in `<module:Devise>'
web_1  |    from /usr/local/bundle/gems/devise-4.8.0/lib/devise.rb:11:in `<main>'
web_1  |    from /usr/local/bundle/gems/bootsnap-1.8.1/lib/bootsnap/load_path_cache/core_ext/kernel_require.rb:23:in `require'
web_1  |    from /usr/local/bundle/gems/bootsnap-1.8.1/lib/bootsnap/load_path_cache/core_ext/kernel_require.rb:23:in `block in require_with_bootsnap_lfi'
web_1  |    from /usr/local/bundle/gems/bootsnap-1.8.1/lib/bootsnap/load_path_cache/loaded_features_index.rb:92:in `register'
web_1  |    from /usr/local/bundle/gems/bootsnap-1.8.1/lib/bootsnap/load_path_cache/core_ext/kernel_require.rb:22:in `require_with_bootsnap_lfi'
web_1  |    from /usr/local/bundle/gems/bootsnap-1.8.1/lib/bootsnap/load_path_cache/core_ext/kernel_require.rb:31:in `require'
web_1  |    from /usr/local/bundle/gems/zeitwerk-2.5.0.beta3/lib/zeitwerk/kernel.rb:35:in `require'
web_1  |    from /usr/local/lib/ruby/3.0.0/bundler/runtime.rb:66:in `block (2 levels) in require'
web_1  |    from /usr/local/lib/ruby/3.0.0/bundler/runtime.rb:61:in `each'
web_1  |    from /usr/local/lib/ruby/3.0.0/bundler/runtime.rb:61:in `block in require'
web_1  |    from /usr/local/lib/ruby/3.0.0/bundler/runtime.rb:50:in `each'
web_1  |    from /usr/local/lib/ruby/3.0.0/bundler/runtime.rb:50:in `require'
web_1  |    from /usr/local/lib/ruby/3.0.0/bundler.rb:174:in `require'
```

if you could help me I'd be grateful


----------
        
## Answer \#0

**Accepted** Vote: 23

Created at 2021-09-17 20:59:18

------------


### Source of the Error


The version of Devise you're using calls a method named `reference`.
[https://github.com/heartcombo/devise/blob/c82e4cf47b02002b2fd7ca31d441cf1043fc634c/lib/devise.rb#L320-L323](https://github.com/heartcombo/devise/blob/c82e4cf47b02002b2fd7ca31d441cf1043fc634c/lib/devise.rb#L320-L323)
```
def self.ref(arg)
  ActiveSupport::Dependencies.reference(arg)
  Getter.new(arg)
end
```

The method existed in Rails 6:
[https://github.com/rails/rails/blob/6-0-stable/activesupport/lib/active_support/dependencies.rb#L651-L653](https://github.com/rails/rails/blob/6-0-stable/activesupport/lib/active_support/dependencies.rb#L651-L653)
It has been removed in Rails 7:
[https://github.com/rails/rails/blob/main/activesupport/lib/active_support/dependencies.rb](https://github.com/rails/rails/blob/main/activesupport/lib/active_support/dependencies.rb)
This line from Rails 7.0.0.alpha1 changelog explains.
> Private internal classes of ActiveSupport::Dependencies have been deleted, like ActiveSupport::Dependencies::Reference, ActiveSupport::Dependencies::Blamable, and others.
[https://github.com/rails/rails/blob/main/activesupport/CHANGELOG.md#rails-700alpha1-september-15-2021](https://github.com/rails/rails/blob/main/activesupport/CHANGELOG.md#rails-700alpha1-september-15-2021)

### Fix: Upgrade Devise


Devise has [released a 4.8.1](https://github.com/heartcombo/devise/blob/main/CHANGELOG.md#481---2021-12-16) version that's compatible with Rails 7. Run `bundle update devise` to upgrade.


------------
    
    
## Answer \#1

 Vote: 1

Created at 2021-09-17 21:09:24

------------

This fix fixed my bug:
> gem "devise", github: "strobilomyces/devise", branch: "patch-1"
[https://github.com/heartcombo/devise/pull/5397](https://github.com/heartcombo/devise/pull/5397)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-10-28 04:03:56

------------

Devise already merged the fix for this issue with rails 7 to their main branch.
Until a new version is released, you can point the devise gem to use the main branch from GitHub in your Gemfile:
```
gem 'devise', git: 'https://github.com/heartcombo/devise', branch: 'main'
```



------------
    
    