
# Post \#71191685 [Link](https://stackoverflow.com/questions/71191685/)

## visit_Psych_Nodes_Alias: Unknown alias: default (Psych::BadAlias)

**Vote**: 66 (98/702) **Views**: 21709 (284/702) 

**Internal ID** \#2-9-488

Created at 2022-02-20 05:50:19

Tags: `ruby-on-rails` `ruby`

----------

#### Metadata:

Area: `Back-end`

Language: `ruby` (full parsed tag list: `ruby`)

----------

**Notepad**


----------

I updated from ruby 2.7.1 to 3.1.1, then removed Gemfile.lock and ran `bundle update` (it's on a dev branch, so I can throw it away if this is a bad idea, I just wanted to see if it would work).
`bundle update` succeeds, but when I start the server:
```
rails s
=> Booting Puma
=> Rails 7.0.2.2 application starting in development 
=> Run `bin/rails server --help` for more startup options
Exiting
/Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/to_ruby.rb:430:in `visit_Psych_Nodes_Alias': Unknown alias: default (Psych::BadAlias)
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/visitor.rb:30:in `visit'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/visitor.rb:6:in `accept'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/to_ruby.rb:35:in `accept'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/to_ruby.rb:345:in `block in revive_hash'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/to_ruby.rb:343:in `each'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/to_ruby.rb:343:in `each_slice'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/to_ruby.rb:343:in `revive_hash'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/to_ruby.rb:167:in `visit_Psych_Nodes_Mapping'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/visitor.rb:30:in `visit'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/visitor.rb:6:in `accept'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/to_ruby.rb:35:in `accept'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/to_ruby.rb:345:in `block in revive_hash'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/to_ruby.rb:343:in `each'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/to_ruby.rb:343:in `each_slice'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/to_ruby.rb:343:in `revive_hash'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/to_ruby.rb:167:in `visit_Psych_Nodes_Mapping'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/visitor.rb:30:in `visit'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/visitor.rb:6:in `accept'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/to_ruby.rb:35:in `accept'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/to_ruby.rb:318:in `visit_Psych_Nodes_Document'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/visitor.rb:30:in `visit'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/visitor.rb:6:in `accept'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych/visitors/to_ruby.rb:35:in `accept'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych.rb:335:in `safe_load'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/psych.rb:370:in `load'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/webpacker-4.3.0/lib/webpacker/env.rb:30:in `available_environments'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/webpacker-4.3.0/lib/webpacker/env.rb:21:in `current'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/webpacker-4.3.0/lib/webpacker/env.rb:15:in `inquire'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/webpacker-4.3.0/lib/webpacker/env.rb:7:in `inquire'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/webpacker-4.3.0/lib/webpacker/instance.rb:11:in `env'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/webpacker-4.3.0/lib/webpacker/instance.rb:18:in `config'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/webpacker-4.3.0/lib/webpacker.rb:34:in `config'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/webpacker-4.3.0/lib/webpacker/railtie.rb:32:in `block in <class:Engine>'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/railties-7.0.2.2/lib/rails/initializable.rb:32:in `instance_exec'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/railties-7.0.2.2/lib/rails/initializable.rb:32:in `run'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/railties-7.0.2.2/lib/rails/initializable.rb:61:in `block in run_initializers'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/tsort.rb:228:in `block in tsort_each'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/tsort.rb:350:in `block (2 levels) in each_strongly_connected_component'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/tsort.rb:431:in `each_strongly_connected_component_from'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/tsort.rb:349:in `block in each_strongly_connected_component'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/tsort.rb:347:in `each'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/tsort.rb:347:in `call'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/tsort.rb:347:in `each_strongly_connected_component'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/tsort.rb:226:in `tsort_each'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/3.1.0/tsort.rb:205:in `tsort_each'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/railties-7.0.2.2/lib/rails/initializable.rb:60:in `run_initializers'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/railties-7.0.2.2/lib/rails/application.rb:372:in `initialize!'
    from /Users/st/rails/hangswith/config/environment.rb:5:in `<main>'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/bootsnap-1.10.3/lib/bootsnap/load_path_cache/core_ext/kernel_require.rb:30:in `require'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/bootsnap-1.10.3/lib/bootsnap/load_path_cache/core_ext/kernel_require.rb:30:in `require'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/zeitwerk-2.5.4/lib/zeitwerk/kernel.rb:35:in `require'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/bootsnap-1.10.3/lib/bootsnap/load_path_cache/core_ext/kernel_require.rb:42:in `require_relative'
    from config.ru:3:in `block in <main>'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/rack-2.2.3/lib/rack/builder.rb:116:in `eval'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/rack-2.2.3/lib/rack/builder.rb:116:in `new_from_string'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/rack-2.2.3/lib/rack/builder.rb:105:in `load_file'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/rack-2.2.3/lib/rack/builder.rb:66:in `parse_file'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/rack-2.2.3/lib/rack/server.rb:349:in `build_app_and_options_from_config'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/rack-2.2.3/lib/rack/server.rb:249:in `app'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/rack-2.2.3/lib/rack/server.rb:422:in `wrapped_app'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/railties-7.0.2.2/lib/rails/commands/server/server_command.rb:76:in `log_to_stdout'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/railties-7.0.2.2/lib/rails/commands/server/server_command.rb:36:in `start'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/railties-7.0.2.2/lib/rails/commands/server/server_command.rb:143:in `block in perform'
    from <internal:kernel>:90:in `tap'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/railties-7.0.2.2/lib/rails/commands/server/server_command.rb:134:in `perform'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/thor-1.2.1/lib/thor/command.rb:27:in `run'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/thor-1.2.1/lib/thor/invocation.rb:127:in `invoke_command'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/thor-1.2.1/lib/thor.rb:392:in `dispatch'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/railties-7.0.2.2/lib/rails/command/base.rb:87:in `perform'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/railties-7.0.2.2/lib/rails/command.rb:48:in `invoke'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/railties-7.0.2.2/lib/rails/commands.rb:18:in `<main>'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/bootsnap-1.10.3/lib/bootsnap/load_path_cache/core_ext/kernel_require.rb:30:in `require'
    from /Users/st/.rbenv/versions/3.1.1/lib/ruby/gems/3.1.0/gems/bootsnap-1.10.3/lib/bootsnap/load_path_cache/core_ext/kernel_require.rb:30:in `require'
    from bin/rails:4:in `<main>'
```


### What I've tried


Googling the 'psych' error message reveals [this](https://stackoverflow.com/q/22664614/5783745) which may be related. But when I search the entire app for `YAML.safe_load` (or even just `safe_load`), there are 0 occurrences of it. (perhaps I should be searching actual gems in my app?).
It was a long shot but based on [this](https://github.com/ruby/psych/issues/533#issuecomment-1034067033) comment, I [ran gem rdoc --all](https://stackoverflow.com/a/3917878/5783745) to update all rdoc documentation. But that didn't help.


----------
        
## Answer \#0

**Accepted** Vote: 111

Created at 2022-02-20 09:21:24

------------

The problem is related to the Ruby 3.1 / Psych 4.x incompatibility described in this issue: [https://bugs.ruby-lang.org/issues/17866](https://bugs.ruby-lang.org/issues/17866)
Ruby 3.0 comes with Psych 3, while Ruby 3.1 comes with Psych 4, which has a major breaking change ([diff 3.3.2 → 4.0.0](https://my.diffend.io/gems/psych/3.3.2/4.0.0)).
- `aliases: true`- `aliases`
At this point, it seems like anyone, anywhere that wants to load YAML in the same way it worked prior to Ruby 3.1, need to do something like this:
```
begin
  YAML.load(source, aliases: true, **options)
rescue ArgumentError
  YAML.load(source, **options)
end
```

[as patched by the Rails team](https://github.com/rails/rails/commit/179d0a1f474ada02e0030ac3bd062fc653765dbe).
In your case, I suspect you will need to see which Rails version that matches your major version preference includes this patch before you can upgrade to Ruby 3.1.
Personally, wherever I have control of the code that loads YAML, I am adding an extension like this:
```
module YAML
  def self.properly_load_file(path)
    YAML.load_file path, aliases: true
  rescue ArgumentError
    YAML.load_file path
  end
end
```

or, if I want to completely revert this change done in Psych 4, I add this to my code:
```
module YAML
  class << self
    alias_method :load, :unsafe_load if YAML.respond_to? :unsafe_load
  end
end
```

This has the advantage of restoring `YAML::load`, and `YAML::load_file` (which uses it) to their original glory, and solves everything including libraries that you have no control of, in all Ruby versions.
Lastly, as I mentioned in the comments, in case you prefer a minimally invasive stopgap measure, you might be able to get away with pinning Psych to `< 4` in your Gemfile:
```
gem 'psych', '< 4'
```


### Note for Rails users (>= 7.0.3.1)


ActiveRecord 7.0.3.1 [changed an internal call](https://github.com/rails/rails/commit/611990f1a6c137c2d56b1ba06b27e5d2434dcd6a) and it now calls `safe_load` directly. If you see a Psych related error during rails tests, you might be affected by this change.
To resolve it, you can add this to a new or existing initializer:
```
# config/initializers/activerecord_yaml.rb
ActiveRecord.use_yaml_unsafe_load = true
```

You may also use `ActiveRecord.yaml_column_permitted_classes` to configure the allowed classes instead.
More info in [this post](https://discuss.rubyonrails.org/t/cve-2022-32224-possible-rce-escalation-bug-with-serialized-columns-in-active-record/81017).


------------
    
    
## Answer \#1

 Vote: 19

Created at 2022-03-16 19:34:27

------------

I ran into the same problem, using a very recent version of rails and ruby 3.1.1 and the suggestion that DannyB mentioned fixed my problem.
In my Gemfile, I put webpacker3 and specified psych < 4.  I don't know yet if this version of psych will break something down the road but at least webpacker installed:
gem 'webpacker', '~>3.0'
gem 'psych', '< 4'


------------
    
    
## Answer \#2

 Vote: 8

Created at 2022-04-26 22:59:12

------------

In my case I had `ruby version 3.0.3` on `gem 'rails', '~> 6.1.5'`
My default system version was locked to `psych (default: 3.3.2)` but somehow Gemfile.lock had stuff broken with:
```
psych (4.0.3)
  stringio

sdoc (2.3.1)
rdoc (>= 5.0, < 6.4.0)
```

The solution was to remove those, then lock correct version of Psych in Gemfile
```
gem 'psych', '< 4
```

Then `bundle install` which fixed the circular broken dependencies


------------
    
    
## Answer \#3

 Vote: 5

Created at 2022-03-19 01:40:32

------------


### A simple solution: downgrade to ruby 3.0.X


This is simple and easy, requires no code changes, and can be upgraded to ruby 3.1.x in the near future when everything's caught up and is compatible with Psych 4.
Note: ruby 3.0.3 is the latest ruby version which still uses Psych 3.x.x, so there are no problems and everything just works.

### Why this solution?


I prefer this option because [patching an app or its gems](https://stackoverflow.com/a/71192990/5783745) can get complicated and I prefer the simplest/safest solution.
See [here](https://stackoverflow.com/questions/71191685/visit-psych-nodes-alias-unknown-alias-default-psychbadalias#comment125844598_71192990) and [here](https://stackoverflow.com/questions/71191685/visit-psych-nodes-alias-unknown-alias-default-psychbadalias#comment125845005_71192990) for the advice that helped me make the decision:
> I think it's best to revert to ruby 3.0.3, so as to use the latest ruby version that doesn't use psych 4. Do you think that's a good idea?
> Absolutely yes in regards to reverting to Ruby < 3.1 ... it takes time until the gem developers community will update their gems

### Why not downgrade psych instead?


The only reason I didn't downgrade psych gem instead of ruby is because I'm not 100% sure that doing so won't cause problems elsewhere, potentially things that are hard to detect. Although it  be a completely viable solution (I'm just not sure that it is, hence I prefer to play it safe and downgrade ruby instead).


------------
    
    
## Answer \#4

 Vote: 0

Created at 2023-01-04 15:00:24

------------

in my case, after update to `ruby 3.1.2` updating to `rails 7.0.4` solved it.


------------
    
    