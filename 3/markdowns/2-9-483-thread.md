
# Post \#56242664 [Link](https://stackoverflow.com/questions/56242664/)

## How to require custom JS files in Rails 6

**Vote**: 27 (213/702) **Views**: 32710 (219/702) 

**Internal ID** \#2-9-483

Created at 2019-05-21 16:26:34

Tags: `ruby-on-rails` `ruby-on-rails-6`

----------

#### Metadata:

Area: `Back-end`

Language: `ruby` (full parsed tag list: `ruby`)

----------

**Notepad**


----------

I'm currently trying Rails 6.0.0.rc1 which seems to have moved the default `javascript` folder from `app/assets/javascript` to `app/javascript`. The `application.js` file is now located in `app/javascript/packs`. Now, I want to add a couple of js files, but for some reason they don't get imported and I can't find any documentation on how this can be done in Rails 6. I tried a couple of things:


1. Create a new folder custom_js under app/javascript/packs, putting all my js files there and then add a require "custom_js" to application.js.
2. Copy all my js files under app/javascript/channels (which should be included by default, since application.js has require("channels")).
3. Adding require_tree . to application.js, which was the previous approach.



How can I load my own js files inside a Rails 6 application?


----------
        
## Answer \#0

**Accepted** Vote: 65

Created at 2019-06-10 10:06:57

------------

Get better-organized code and avoid multiple javascript_pack_tags in your application.html.erb file with this approach:


1. Add your custom example.js javascript file to app/javascript/packs.
2. Add require("packs/example") to your application.js file.




---





It isn't necessary to include the ".js" extension inside of the require.


------------
    
    
## Answer \#1

 Vote: 23

Created at 2019-05-21 21:06:17

------------

You need to do the following steps to add custom javascript file to rails 6 (webpacker)
.Create your custom file named `custom.js` in `app/javascript/packs` directory.
For testing purpose, write any `console.log` in it.
```
// app/javascript/packs/custom.js

console.log("custom js file loaded")
```

. Go to your `application.html.erb` and add the following line at the end of your `<head></head>`
```
<%= javascript_pack_tag 'custom', 'data-turbolinks-track': 'reload' %>
```

. Now execute `rake assets:precompile`
This will pack your javascript code (including our custom file we just added)
Now reload your page and you should see the message
```
custom js file loaded
```

In your browser console.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-09-10 04:41:34

------------

My custom js has functions which will be called by embedded javascript of serveral html pages. Following snippet works in Rails6, compiled by webpacker:

1. put custom js file in folder app/javascript/packs e.g. app/javascript/packs/my_functions.js


```
say_hello = function(a_text){ 
    console.log("HELLO "+ a_text);  
}
```



1. add javascript_pack_tag in html file, e.g. index.html.erb .


```
<%= javascript_pack_tag 'my_functions' %>
<!-- html here -->

<script>
$(document).ready(function(){
    say_hello('ABer')
});
</script>
```


Note : This line is inside html body, not in head
```
<script src="/packs/js/my_functions-1db66368ebbd2fe31abd.js"></script>
```




------------
    
    