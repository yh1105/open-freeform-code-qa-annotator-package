
# Post \#53420082 [Link](https://stackoverflow.com/questions/53420082/)

## [Vue warn]: Avoid using non-primitive value as key, use string/number value instead

**Vote**: 41 (153/702) **Views**: 79648 (105/702) 

**Internal ID** \#0-0-62

Created at 2018-11-21 20:33:40

Tags: `javascript` `vue.js`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm building a clone of HackerNews and am getting the following error:

```
vue.esm.js?efeb:591 [Vue warn]: Avoid using non-primitive value as key, use string/number value instead.

found in

---> <Single> at src/components/Single.vue
       <App> at src/App.vue
         <Root>
```


The error seems to be coming from Single.vue but I can't work what it is? The template is as follows:

```
<template>
    <div class="container">
        <h2>{{ story.title }}</h2>
        <p>Score: {{ story.score }}</p>
        <p>{{ story.url }}</p>
        <div v-for="comment in comments" :key="comment">
          <div class="comment-wrap">
                <div class="comment-block">
                    <p class="comment-text">{{ comment.text }}</p>
                    <div class="bottom-comment">
                        <div class="comment-author">{{ comment.by }}</div>
                        <div class="comment-date">{{ comment.time }}</div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
```


If anyone out there can help, that would be great!?


----------
        
## Answer \#0

**Accepted** Vote: 22

Created at 2018-11-21 20:40:20

------------

You can simply avoid the use of `:key` in your `v-for`.
```
...
<div v-for="comment in comments">
...
```

As vuejs [documentation](https://v2.vuejs.org/v2/guide/list.html#key) said:
> It is recommended to provide a key with v-for whenever possible, unless the
iterated DOM content is simple, or you are intentionally relying on the
default behavior for performance gains.


------------
    
    
## Answer \#1

 Vote: 76

Created at 2019-04-10 20:19:34

------------

Other answers work, but the following is also worth a try:

```
<div v-for="(comment, idx) in comments" :key="idx">
  <!-- ... -->
</div>
```



------------
    
    
## Answer \#2

 Vote: 34

Created at 2018-11-21 20:37:48

------------

The warning sources from `<div v-for="comment in comments" :key="comment">`, where the object `comment` is used as a `key` for `v-for`. The meaning of the warning is quite literal, don't use Object as key.

Use unique primitive value as key, maybe something like `comment.id` or `${comment.time}${comment.by}`


------------
    
    
## Answer \#3

 Vote: 9

Created at 2019-09-06 04:53:38

------------

Below is an example of what works for me. Hope that helps

[](https://i.stack.imgur.com/qnV5o.png)


------------
    
    
## Answer \#4

 Vote: 8

Created at 2020-01-08 11:16:41

------------

Use unique primitive value as key,
It is recommended to provide a `key` attribute with `v-for` whenever possible.
> like `:key="design.uniqueId"`
```
<li v-for="design in designs" :key="design.id">
    <!-- code -->
</li>

<li v-for="loop in loops" :key="loop.id">
    <!-- code -->
</li>
```

Such as [Vue.js Documentation](https://v2.vuejs.org/v2/guide/list.html#Maintaining-State)


------------
    
    
## Answer \#5

 Vote: 2

Created at 2021-02-18 12:43:02

------------

as per vue js [Maintaining State](https://v2.vuejs.org/v2/guide/list.html#Maintaining-State) Doc it says your id is find multiple times
That means your code should look like this
`div v-for="comment in comments" :key="comment.id">`
insted of
`div v-for="comment in comments" :key="comment">`
To give Vue a hint so that it can track each node’s identity, and thus reuse and reorder existing elements, you need to provide a unique key attribute for each item


------------
    
    
## Answer \#6

 Vote: 0

Created at 2021-01-18 03:57:47

------------

Maybe you are using an Object type as the key, if it can be converted into a unique string, use the string instead of the instance by calling such as `toString`.
I had same issue when I used `mongoose.Types.ObjectId()` as the key, after I changed to `mongoose.Types.ObjectId().toHexString()` the warning disappear.


------------
    
    