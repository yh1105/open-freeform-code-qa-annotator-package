
# Post \#65989040 [Link](https://stackoverflow.com/questions/65989040/)

## bundle exec jekyll serve: cannot load such file

**Vote**: 36 (170/702) **Views**: 13816 (374/702) 

**Internal ID** \#2-9-480

Created at 2021-02-01 08:02:09

Tags: `ruby` `rubygems` `jekyll` `bundler` `github-pages`

----------

#### Metadata:

Area: `Back-end`

Language: `ruby` (full parsed tag list: `ruby`)

----------

**Notepad**


----------

I am trying to contribute to a Github Page/Jekyll site and want to be able to visualise changes locally but when I run `bundle exec jekyll serve` but I get this output:
```
bundler: failed to load command: jekyll (/usr/local/lib/ruby/gems/3.0.0/bin/jekyll)
/usr/local/lib/ruby/gems/3.0.0/gems/kramdown-1.17.0/lib/kramdown/parser/html.rb:10:in `require': cannot load such file -- rexml/parsers/baseparser (LoadError)
        from /usr/local/lib/ruby/gems/3.0.0/gems/kramdown-1.17.0/lib/kramdown/parser/html.rb:10:in `<top (required)>'
        from /usr/local/lib/ruby/gems/3.0.0/gems/kramdown-1.17.0/lib/kramdown/parser/kramdown/html.rb:10:in `require'
        from /usr/local/lib/ruby/gems/3.0.0/gems/kramdown-1.17.0/lib/kramdown/parser/kramdown/html.rb:10:in `<top (required)>'
        from /usr/local/lib/ruby/gems/3.0.0/gems/kramdown-1.17.0/lib/kramdown/parser/kramdown/paragraph.rb:14:in `require'
        from /usr/local/lib/ruby/gems/3.0.0/gems/kramdown-1.17.0/lib/kramdown/parser/kramdown/paragraph.rb:14:in `<top (required)>'
        from /usr/local/lib/ruby/gems/3.0.0/gems/kramdown-1.17.0/lib/kramdown/parser/kramdown.rb:345:in `require'
        from /usr/local/lib/ruby/gems/3.0.0/gems/kramdown-1.17.0/lib/kramdown/parser/kramdown.rb:345:in `<class:Kramdown>'
        from /usr/local/lib/ruby/gems/3.0.0/gems/kramdown-1.17.0/lib/kramdown/parser/kramdown.rb:60:in `<module:Parser>'
        from /usr/local/lib/ruby/gems/3.0.0/gems/kramdown-1.17.0/lib/kramdown/parser/kramdown.rb:19:in `<module:Kramdown>'
        from /usr/local/lib/ruby/gems/3.0.0/gems/kramdown-1.17.0/lib/kramdown/parser/kramdown.rb:17:in `<top (required)>'
        from /usr/local/lib/ruby/gems/3.0.0/gems/jekyll-3.8.5/lib/jekyll/converters/smartypants.rb:3:in `require'
        from /usr/local/lib/ruby/gems/3.0.0/gems/jekyll-3.8.5/lib/jekyll/converters/smartypants.rb:3:in `<top (required)>'
        from /usr/local/lib/ruby/gems/3.0.0/gems/jekyll-3.8.5/lib/jekyll.rb:13:in `require'
        from /usr/local/lib/ruby/gems/3.0.0/gems/jekyll-3.8.5/lib/jekyll.rb:13:in `block in require_all'
        from /usr/local/lib/ruby/gems/3.0.0/gems/jekyll-3.8.5/lib/jekyll.rb:12:in `each'
        from /usr/local/lib/ruby/gems/3.0.0/gems/jekyll-3.8.5/lib/jekyll.rb:12:in `require_all'
        from /usr/local/lib/ruby/gems/3.0.0/gems/jekyll-3.8.5/lib/jekyll.rb:194:in `<top (required)>'
        from /usr/local/lib/ruby/gems/3.0.0/gems/jekyll-3.8.5/exe/jekyll:8:in `require'
        from /usr/local/lib/ruby/gems/3.0.0/gems/jekyll-3.8.5/exe/jekyll:8:in `<top (required)>'
        from /usr/local/lib/ruby/gems/3.0.0/bin/jekyll:23:in `load'
        from /usr/local/lib/ruby/gems/3.0.0/bin/jekyll:23:in `<top (required)>'
        from /usr/local/lib/ruby/gems/3.0.0/gems/bundler-2.2.5/lib/bundler/cli/exec.rb:63:in `load'
        from /usr/local/lib/ruby/gems/3.0.0/gems/bundler-2.2.5/lib/bundler/cli/exec.rb:63:in `kernel_load'
        from /usr/local/lib/ruby/gems/3.0.0/gems/bundler-2.2.5/lib/bundler/cli/exec.rb:28:in `run'
        from /usr/local/lib/ruby/gems/3.0.0/gems/bundler-2.2.5/lib/bundler/cli.rb:494:in `exec'
        from /usr/local/lib/ruby/gems/3.0.0/gems/bundler-2.2.5/lib/bundler/vendor/thor/lib/thor/command.rb:27:in `run'
        from /usr/local/lib/ruby/gems/3.0.0/gems/bundler-2.2.5/lib/bundler/vendor/thor/lib/thor/invocation.rb:127:in `invoke_command'
        from /usr/local/lib/ruby/gems/3.0.0/gems/bundler-2.2.5/lib/bundler/vendor/thor/lib/thor.rb:392:in `dispatch'
        from /usr/local/lib/ruby/gems/3.0.0/gems/bundler-2.2.5/lib/bundler/cli.rb:30:in `dispatch'
        from /usr/local/lib/ruby/gems/3.0.0/gems/bundler-2.2.5/lib/bundler/vendor/thor/lib/thor/base.rb:485:in `start'
        from /usr/local/lib/ruby/gems/3.0.0/gems/bundler-2.2.5/lib/bundler/cli.rb:24:in `start'
        from /usr/local/lib/ruby/gems/3.0.0/gems/bundler-2.2.5/exe/bundle:49:in `block in <top (required)>'
        from /usr/local/lib/ruby/gems/3.0.0/gems/bundler-2.2.5/lib/bundler/friendly_errors.rb:130:in `with_friendly_errors'
        from /usr/local/lib/ruby/gems/3.0.0/gems/bundler-2.2.5/exe/bundle:37:in `<top (required)>'
        from /usr/local/opt/ruby/bin/bundle:23:in `load'
        from /usr/local/opt/ruby/bin/bundle:23:in `<main>'
```

I did `bundle update` and this the result:
```
Using concurrent-ruby 1.1.8 (was 1.1.7)
Using multi_json 1.15.0
Using public_suffix 3.1.1
Using latex-decode 0.3.2
Using bundler 2.2.5
Using coffee-script-source 1.11.1
Using execjs 2.7.0
Using colorator 1.1.0
Using namae 1.0.2 (was 1.0.1)
Using unf_ext 0.0.7.7
Using http_parser.rb 0.6.0
Using faraday-net_http 1.0.1
Using multipart-post 2.1.1
Using ruby2_keywords 0.0.4
Using forwardable-extended 2.6.0
Using gemoji 3.0.1
Using rb-fsevent 0.10.4
Using kramdown 1.17.0
Using liquid 4.0.3
Using mercenary 0.3.6
Using rouge 3.13.0
Using safe_yaml 1.0.5
Using racc 1.5.2
Using jekyll-paginate 1.1.0
Using rubyzip 2.3.0
Using jekyll-swiss 1.0.0
Using unicode-display_width 1.7.0
Using i18n 0.9.5
Using coffee-script 2.4.1
Using citeproc 1.0.10
Using csl 1.5.2
Using unf 0.1.4
Using eventmachine 1.2.7
Using citeproc-ruby 1.1.13 (was 1.1.12)
Using activesupport 3.2.22.5
Using csl-styles 1.0.1.10
Using simpleidn 0.2.1 (was 0.1.1)
Using faraday 1.3.0 (was 1.0.1)
Using addressable 2.7.0
Using pathutil 0.16.2
Using jekyll-coffeescript 1.1.1
Using nokogiri 1.11.1 (x86_64-darwin) (was 1.10.10)
Using terminal-table 1.8.0
Using bibtex-ruby 4.4.7
Using em-websocket 0.5.2
Using dnsruby 1.61.5 (was 1.61.4)
Using html-pipeline 2.14.0
Using ffi 1.14.2 (was 1.13.1)
Using sawyer 0.8.2
Using ethon 0.12.0
Fetching ruby-enum 0.9.0 (was 0.8.0)
Using typhoeus 1.4.0
Using octokit 4.20.0 (was 4.18.0)
Using rb-inotify 0.10.1
Using github-pages-health-check 1.16.1
Using sass-listen 4.0.0
Using listen 3.4.1 (was 3.2.1)
Using jekyll-gist 1.5.0
Using sass 3.7.4
Using jekyll-watch 2.2.1
Using jekyll-sass-converter 1.5.2
Using jekyll 3.8.5
Using jekyll-avatar 0.7.0
Using jekyll-github-metadata 2.13.0
Using jekyll-mentions 1.5.1
Using jekyll-optional-front-matter 0.3.2
Using jekyll-readme-index 0.3.0
Using jekyll-redirect-from 0.15.0
Using jekyll-relative-links 0.6.1
Using jekyll-remote-theme 0.4.1
Using jekyll-seo-tag 2.6.1
Using jekyll-sitemap 1.4.0
Using jekyll-titles-from-headings 0.5.3
Using jemoji 0.11.1
Using jekyll-scholar 5.16.0
Using jekyll-feed 0.13.0
Using jekyll-theme-cayman 0.1.1
Using jekyll-theme-dinky 0.1.1
Using jekyll-theme-hacker 0.1.1
Using jekyll-theme-leap-day 0.1.1
Using jekyll-theme-merlot 0.1.1
Using jekyll-default-layout 0.1.4
Using jekyll-theme-minimal 0.1.1
Using jekyll-theme-midnight 0.1.1
Using jekyll-theme-primer 0.5.4
Using jekyll-theme-slate 0.1.1
Using jekyll-theme-tactile 0.1.1
Using jekyll-theme-modernist 0.1.1
Using jekyll-theme-time-machine 0.1.1
Using minima 2.5.1
Using jekyll-theme-architect 0.1.1
Installing ruby-enum 0.9.0 (was 0.8.0)
Using commonmarker 0.17.13
Using jekyll-commonmark 1.3.1
Using jekyll-commonmark-ghpages 0.1.6
Using github-pages 204
Bundle updated!
```

Also, this is what the gemfile looks like:
```
source "https://rubygems.org"
gem 'jekyll-scholar', group: :jekyll_plugins

# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
gem "github-pages", "~> 204", group: :jekyll_plugins

# If you have any plugins, put them here!
group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.6"
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem "tzinfo-data", platforms: [:mingw, :mswin, :x64_mingw, :jruby]

# Performance-booster for watching directories on Windows
gem "wdm", "~> 0.1.0" if Gem.win_platform?
```

Why doesn'it work?
I am fairly new to ruby and jekyll/github pages but I created another Github Page's website and this commands without a problem...


----------
        
## Answer \#0

**Accepted** Vote: 13

Created at 2021-03-07 20:31:19

------------

Here's how I solved it thanks to @choas' comment:

1. Updated github-pages, jekyll and jekyll-feed gems by running gem install github-pages, gem install jekyll and gem install jekyll-feed. I had to do this step as a simple bundle update wasn't installing the latest version.
2. Modify those gems in the Gemfile to the latest version.
3. Run bundle update
4. Finally run bundle exec jekyll serve


I am not sure that all those steps aren't redundant as I am not very familiar with Ruby/Github Pages/Jekyll but it did work.


------------
    
    
## Answer \#1

 Vote: 49

Created at 2021-02-02 16:29:41

------------

I had the same problem and I found a workaround here at [https://github.com/jekyll/jekyll/issues/8523](https://github.com/jekyll/jekyll/issues/8523)
Add `gem "webrick"` to the Gemfile in your website. Than run `bundle install`
At this point you can run `bundle exec jekyll serve`
For me it works!


------------
    
    
## Answer \#2

 Vote: 14

Created at 2021-07-23 01:17:30

------------

I also get this error. The solution is in the [documentation itself](https://jekyllrb.com/docs/).
> If you are using Ruby version , step 5 may (`bundle exec jekyll serve`). You may fix it by adding `webrick` to your dependencies: `bundle add webrick`
Or add `gem "webrick"` into the `Gemfile`, then `bundle install`.


------------
    
    
## Answer \#3

 Vote: 8

Created at 2021-02-28 13:45:06

------------

I see that you're using `github-pages:204` and `jekyll:3.8.5`.
I made my project with the same dependencies work with:

1. bundle update github-pages
2. bundle add webrick




------------
    
    
## Answer \#4

 Vote: 4

Created at 2021-06-15 08:31:23

------------


1. Install webrick and jekyll-mentions: open terminal and type gem install webrick then gem install jekyll-mentions.
2. Open your gem file and add gem "jekyll-mentions" and gem "webrick" in the pulgins:


```
group :jekyll_plugins do
  gem "jekyll-mentions", "1.6.0"
  gem "webrick"
end
```


1. Navigate back to terminal, and execute bundle install.


You are good to go, I think. This process worked for me!


------------
    
    
## Answer \#5

 Vote: 2

Created at 2022-06-12 04:48:34

------------

Simple remove the specific version of jekyll in gemfile, as that specificity causes dependency issues with the kramdown package.
```
gem "jekyll", "~> 3.2.1"
```

Remove the specific version of jekyll, like so:
```
gem "jekyll"
```

Add webrick gem to gemfile
```
gem "webrick"
```

Run bundler again.
```
bundler
```

Finally, serve
```
bundle exec jekyll serve --livereload
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-08-25 04:33:55

------------

Thank you yours most closely got mine working. I apparently also needed a bunch of other plugins so my working Gemfile jekyll_plugins look like so:
```
group :jekyll_plugins do
  gem "jekyll-feed"
  gem "jekyll-mentions"
  gem "webrick"
  gem "jekyll-redirect-from"
  gem "jekyll-sitemap"
  gem "jekyll-avatar"
  gem "jemoji"
end
```

then `bundle install; bundle exec jekyll serve`


------------
    
    