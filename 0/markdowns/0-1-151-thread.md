
# Post \#69906920 [Link](https://stackoverflow.com/questions/69906920/)

## Css changes are not being applied in rails app

**Vote**: 0 (696/702) **Views**: 1179 (648/702) 

**Internal ID** \#0-1-151

Created at 2021-11-10 01:18:50

Tags: `css` `ruby-on-rails` `asset-pipeline` `assets`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css` `ruby`)

----------

**Notepad**


----------

I am working on rails app and now I am having trouble.
> Early when I use to change my frontend styling and apply changes in css then they quickly show up on reload in brower.
> But in present, don't know but something went wrong. Whenever I change css and save it then onload, things remain same.
After debugging, I found that app is picking styling files from precompiled assets pipeline. So for the solution, I have to recompile the assets then 2 new files are generated against wach file changed. And there will be 4 files in total against a single css file in which changes are made. So now I have to delete old ones and then have to restart the serve and refreshing the browser will show all the changing and that is weird as I have to do it all the time for every single change.
I can't figure as I am new to rails. Help....


----------
        
## Answer \#0

**Accepted** Vote: -1

Created at 2022-01-25 02:05:50

------------

So I have find out the reason and it was because I have run the command of assets precompile which I shouldn't have to run as rails was doing it already for me may be due to configuration of my project or may it is its default behavior.
> To solve this problem I have to delete my projects folder from my system and clone it again and things got normal again.
And now I never ever going to run assets precompile command again 


------------
    
    
## Answer \#1

 Vote: 4

Created at 2021-11-10 13:35:25

------------

Please clear the tmp cache first and then run the server
```
$ rake tmp:cache:clear && rails server
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-11-10 01:57:19

------------

If you are developing the app, you should delete `public/assets` directory. Then restart the app. Don't run `asssts:precompile` task. Your most recent changes of assets will be up to date without restart the app.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-05-30 09:10:48

------------

I had the same issue and found a solution : run  `rake assets:clobber`, it will remove the precompiled  css and js. Then run  `yarn build --watch` to build back the css and js file.
Dont run the `assets:build` as the same issue will revert.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-11-29 01:56:35

------------

This might actually be a browser issue, where the browser is caching the previous CSS and not reloading your new CSS changes.
Instead of reloading the page with CTRL+R, try reloading the page with CTRL+SHIFT+R
This will force a reload of the web page and clear the page cache, which forces all the assets to reload and fixed the problem for me.
No need to run css:clobber, assets:precompile, or anything like that! Just change the way you reload your browser :)


------------
    
    