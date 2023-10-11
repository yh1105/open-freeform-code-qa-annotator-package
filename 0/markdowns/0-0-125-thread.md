
# Post \#59540900 [Link](https://stackoverflow.com/questions/59540900/)

## How to upgrade Vue version

**Vote**: 12 (356/702) **Views**: 44847 (180/702) 

**Internal ID** \#0-0-125

Created at 2019-12-31 07:21:14

Tags: `javascript` `php` `laravel` `vue.js` `webpack`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript` `php`)

----------

**Notepad**


----------

I was using the Vue version (2.5), I ran that command `npm install -g @vue/cli` after that I check my vue version by using the command `vue --version`and it shows me `@vue/cli 4.1.2`. but in my package.json file the version did not upgrade, it still showing me the older version i.e 2.5.7. 
[](https://i.stack.imgur.com/cKW6d.png) 

Any help would be highly appreciable


----------
        
## Answer \#0

**Accepted** Vote: -13

Created at 2020-01-01 13:52:20

------------

```
npm install -g @vue/cli --save
```



## --save will automatically update version in your package.json file.




------------
    
    
## Answer \#1

 Vote: 25

Created at 2019-12-31 07:35:35

------------

You can upgrade it through vue cli.

```
npm install -g @vue/cli
```


OR

```
yarn global add @vue/cli
```


then

```
vue upgrade
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-10-09 11:12:23

------------

Both provided answers assume you have a project and you're updating it.
If you want to update the global installation, as per the documentation in the Vue website at [https://cli.vuejs.org/guide/installation.html](https://cli.vuejs.org/guide/installation.html)
you need to do the following:
```
npm update -g @vue/cli
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-09-28 14:18:00

------------

You can use this guide to upgrade to Vue 2.7:
[Vue 2.7 Migration Guide](https://blog.vuejs.org/posts/vue-2-7-naruto.html)


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-07-19 21:11:33

------------

You can use npx to avoid installing the @vue/cli package globally.
`npx --legacy-peer-deps @vue/cli upgrade`


------------
    
    
## Answer \#5

 Vote: -1

Created at 2020-11-27 16:48:58

------------

You can upgrade it through vue cli.
`yarn global add @vue/cli@3.0`
OR
`npm i -g @vue/cli@3.0`
then
`vue upgrade`


------------
    
    