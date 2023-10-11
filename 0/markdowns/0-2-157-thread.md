
# Post \#63986278 [Link](https://stackoverflow.com/questions/63986278/)

## Vue 3 ::v-deep usage as a combinator has been deprecated. Use ::v-deep(<inner-selector>) instead

**Vote**: 35 (176/702) **Views**: 43434 (182/702) 

**Internal ID** \#0-2-157

Created at 2020-09-21 04:40:07

Tags: `css` `vue.js` `vuejs3`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

I started getting the following warning in Vue 3 with the `::v-deep` usage.
```
::v-deep usage as a combinator has been deprecated. Use ::v-deep(<inner-selector>) instead
```

The CSS looks like as follows:
```
.parent ::v-deep .child {
   ...
}
```

What's the correct way to use suggested option `::v-deep(<inner-selector>)`?


----------
        
## Answer \#0

**Accepted** Vote: 40

Created at 2020-09-21 04:53:46

------------

The relevant RFC is here:
[https://github.com/vuejs/rfcs/blob/master/active-rfcs/0023-scoped-styles-changes.md](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0023-scoped-styles-changes.md)
I believe you need to change it to:
```
.parent ::v-deep(.child) {
```


The warning message mentioned in the question has been changed in later versions of Vue 3 to recommend using `:deep()` instead. This is an alias for `::v-deep()` and it has been added to the documentation here:
[https://v3.vuejs.org/api/sfc-style.html#deep-selectors](https://v3.vuejs.org/api/sfc-style.html#deep-selectors)


------------
    
    
## Answer \#1

 Vote: 47

Created at 2021-06-10 15:11:33

------------

Little update: now, you need to change it to:
```
.parent :deep(.child) {
    (CSS rules)
}
```



------------
    
    
## Answer \#2

 Vote: 11

Created at 2022-08-05 09:32:35

------------

This issue also occurs in Vue 2.7
```
[@vue/compiler-sfc] ::v-deep usage as a combinator has been deprecated. Use :deep(<inner-selector>) instead.
```

I I think neither of the solutions above solve that warning.
Edited working using:
```
:deep() {
  .class {}
}
```

But it finds error in npm dependencies as well


------------
    
    