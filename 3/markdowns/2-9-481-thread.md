
# Post \#58339607 [Link](https://stackoverflow.com/questions/58339607/)

## Why does Rails fails to boot with "Expected to find a manifest file in `app/assets/config/manifest.js` (Sprockets::Railtie::ManifestNeededError)"?

**Vote**: 76 (82/702) **Views**: 28595 (237/702) 

**Internal ID** \#2-9-481

Created at 2019-10-11 10:45:31

Tags: `ruby-on-rails` `ruby-on-rails-5`

----------

#### Metadata:

Area: `Back-end`

Language: `ruby` (full parsed tag list: `ruby`)

----------

**Notepad**


----------

After `bundle update` my Rails app fails to boot with:

```
Expected to find a manifest file in `app/assets/config/manifest.js` (Sprockets::Railtie::ManifestNeededError)
```



----------
        
## Answer \#0

**Accepted** Vote: 115

Created at 2019-10-14 04:30:57

------------


### What's happening?


Looks like you've upgraded sprockets. The later version(s) of sprockets require what's called a `manifest.js` file. You don't have one. You need to create one, and add in a few "directives".

### Why do I need to do this?


In the old version of sprockets, big assumptions were made about  what assets to bundle/concatenate** - this is what sprockets does btw.
"Sprockets, please"
- `abc`- `xyz`- `admin.js`

# Easy Steps To Solve the Problem:



1. Create the manifest.js file $ mkdir -p app/assets/config 
$ touch app/assets/config/manifest.js
 (not the root rails directory)
2. Then copy and paste the following into the manifest.js file you just created: //= link_tree ../images
//= link_directory ../javascripts .js
//= link_directory ../stylesheets .css



### What are "Directives"?


Those commenty things `//=` are called "directives".
If you haven't done so, pls review the [sprockets](https://github.com/rails/sprockets) documentation and save yourself some headaches. Small example below:
Let's translate the `//= link_directory ../javascripts .js` directive:
"grab every `js` file in the javascripts directory, concatenate them, and keep them as SEPARATE javascript files i.e. no bundling." If you want bundling, use a different directive.

### Set up your layouts template


You should also have a `javascript_include_tag`, which is typically placed in your `application.html.erb` file. If you have other files js files that are separately bundled, don't forget to add them to `application.html.erb` e.g.:
```
<%= javascript_include_tag "application", "addOtherFiles", "here", "etc", "data-turbo-track": "reload", defer: true %>
```


1. If you have a precompile array in your app/config/environments/production.rb folder (see below for an example) then perhaps you should move them over to your manifest.js if they are not already accessed above. config.assets.precompile = ["admin.js", "admin.css"]


Presumably you will want your `admin.js` javascript file separate from your `application.js` file. No problem, just tell sprockets to keep them separate:
```
//= link_tree ../images
//= link_directory ../javascripts .js
//= link_directory ../stylesheets .css
//= link "admin.js"
```


1. Lastly, if you are using webpacker, you might want to decide what you want handled by the asset pipeline and what you want handled by webpacker. i.e. remove the link_directory to the javascripts file according to your own particular use cases.


[Reference: read here for further details re: manifest.js. file](https://github.com/rails/sprockets/blob/master/UPGRADING.md#manifestjs)
[Source: Thanks to Richard Schneeman's blog - see here for more information..](https://www.schneems.com/2017/11/22/self-hosted-config-introducing-the-sprockets-manifestjs/)

---



### Footnotes and Primers


- - 
 if things are confusing / not clear:  How can I fix if you keep mum? everyone benefits by these improvements.


------------
    
    
## Answer \#1

 Vote: 28

Created at 2019-10-11 10:45:31

------------

A new major version of sprockets was recently released which is not compatible with the previous version.

Either perform the [steps needed to upgrade](https://github.com/rails/sprockets/blob/master/UPGRADING.md#guide-to-upgrading-from-sprockets-3x-to-4x) or pin to version 3.x in Gemfile

```
gem 'sprockets', '~>3.0'
```



------------
    
    
## Answer \#2

 Vote: 11

Created at 2020-02-28 02:37:30

------------

Based on the [answer here](https://github.com/rails/sprockets-rails/issues/444#issuecomment-548526846) you may be able to solve this with:

```
mkdir -p app/assets/config && echo '{}' > app/assets/config/manifest.js
```


And if you need more details, [the answer in this thread](https://stackoverflow.com/a/58339608/1201134) helpfully points to the [Guide to upgrading from Sprockets 3.x to 4.x](https://github.com/rails/sprockets/blob/master/UPGRADING.md#guide-to-upgrading-from-sprockets-3x-to-4x)


------------
    
    
## Answer \#3

 Vote: 0

Created at 2019-11-27 01:56:05

------------

As suggested by link [http://www.redmine.org/boards/2/topics/58169](http://www.redmine.org/boards/2/topics/58169), it is a known issue. See #32223 and sprockets 4.0.0 breaks Redmine 3.4.11 with Ruby <2.5.0.

I just reproduced this issue with redmine 3.4.4, but found everything is ok with Redmine 3.4.12.

wget [http://www.redmine.org/releases/redmine-3.4.12.tar.gz](http://www.redmine.org/releases/redmine-3.4.12.tar.gz)


------------
    
    