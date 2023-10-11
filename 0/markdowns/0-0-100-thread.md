
# Post \#59021924 [Link](https://stackoverflow.com/questions/59021924/)

## Error: Failed to load parser 'babel-eslint' declared in '.eslintrc': Cannot find module 'babel-eslint' in create-react-app

**Vote**: 20 (261/702) **Views**: 33622 (213/702) 

**Internal ID** \#0-0-100

Created at 2019-11-24 20:14:02

Tags: `reactjs` `create-react-app` `eslint` `babel-eslint`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

Trying to install [eslint](https://www.npmjs.com/package/eslint) into [create-react-app](https://www.npmjs.com/package/create-react-app), but get next error when running linter:

![enter image description here]](https://i.stack.imgur.com/YhtHl.png)

Here is my `.eslintrc` config file:

```
{
  "extends": ["airbnb", "prettier", "prettier/react"],
  "plugins": ["prettier"],
  "parser": "babel-eslint"
}
```


If install `babel-eslint` manually it'll potentially produce another error based on package conflict between  project and `react-scripts` dependencies:
[](https://i.stack.imgur.com/shgSj.png)


----------
        
## Answer \#0

**Accepted** Vote: 16

Created at 2019-11-24 20:14:02

------------

To fix this issue just reuse `babel-eslint` dependency from `react-scripts`, that already installed. Update your config:

```
{
  "extends": ["airbnb", "prettier", "prettier/react"],
  "plugins": ["prettier"],
  "parser": "react-scripts/node_modules/babel-eslint"
}
```



------------
    
    
## Answer \#1

 Vote: 11

Created at 2022-02-28 19:29:25

------------

Did you install `@babel/eslint-parser` or `eslint-parser`?
In my case I had to use `@babel/eslint-parser` and `.eslintrc` looks like this:
```
"parser": "@babel/eslint-parser",
```



------------
    
    
## Answer \#2

 Vote: 5

Created at 2020-10-25 12:58:31

------------

In my case solution was just run `npm install eslint --save-dev` for update eslint version


------------
    
    
## Answer \#3

 Vote: 2

Created at 2021-04-09 10:57:04

------------

`yarn add eslint --save-dev` solved that issue for me!


------------
    
    
## Answer \#4

 Vote: 2

Created at 2021-08-28 12:29:24

------------

For me, it is because that dependency is really not installed... I just followed the GatsbyJS's official guide, and it is not installed (not sure why that guide is not complete).
So just: `yarn add -D babel-eslint`


------------
    
    
## Answer \#5

 Vote: 2

Created at 2021-12-13 09:50:34

------------

```
❯ yarn add -D babel-eslint 
yarn add v1.22.15
[1/4] Resolving packages...
warning babel-eslint@10.1.0: babel-eslint is now @babel/eslint-parser. This package will no longer receive updates.
```

`babel-eslint` seems deprecated and package is now provided as an ES module under `babel`, so remove `babel-eslint` and instead install `@babel/eslint-parser`
```
yarn remove babel-eslint
yarn add -D @babel/eslint-parser
```



------------
    
    
## Answer \#6

 Vote: 1

Created at 2021-08-05 16:25:09

------------

A little late here but thought I would share what got me going...
I completely dismissed the error output which tells me where the `.eslintrc` file (that is looking for said package) lives. As you can see... I had some random `.eslintrc` living outside of my project which was somehow getting picked up.
```
Failed to load parser 'babel-eslint' declared in '../.eslintrc': Cannot find module 'babel-eslint'
```


#### Solution:



I suspect that it has something to do with installing babel-eslint and eslint globally.


------------
    
    
## Answer \#7

 Vote: 0

Created at 2021-03-05 19:12:24

------------

Running eslint on your projects root folder `eslint .` will display the missing packages that you might need to install and that worked well for me.


------------
    
    
## Answer \#8

 Vote: 0

Created at 2022-03-28 07:41:33

------------

Just add `@babel/eslint-parser` in .eslintrc


------------
    
    
## Answer \#9

 Vote: 0

Created at 2022-05-02 06:27:44

------------

As for me i simply install this  npm install eslint@4.x babel-eslint@8 - g  and it works for me


------------
    
    
## Answer \#10

 Vote: 0

Created at 2022-08-01 18:34:54

------------

I have yet another "this is what worked for me" answer. Like others my issue is definitely related to the deprecation of `babel-eslint`. My specific issue was that `eslint-config-react-app` was marked as a direct dependency, which I hadn't upgraded as part of upgrading a major version of `react-scripts`, somehow this left me with a version of `eslint-config-react-app` that was expecting `babel-eslint`, but only `@babel/eslint-parser` installed. For a while I thought the solution was removing the old `eslint-config-react-app`, but several iterations later I found I needed the direct dependency.
```
$npm ls eslint-config-react-app
client@0.1.1 /srv/
├── eslint-config-react-app@6.0.0
└─┬ react-scripts@5.0.1
  └── eslint-config-react-app@7.0.1

$ npm i eslint-config-react-app

added 1 package, and audited 2909 packages in 6s

$ npm ls eslint-config-react-app
client@0.1.1 /srv/
├── eslint-config-react-app@7.0.1
└─┬ react-scripts@5.0.1
  └── eslint-config-react-app@7.0.1 deduped
```



------------
    
    
## Answer \#11

 Vote: 0

Created at 2022-09-18 14:52:32

------------

This works like Charm
> npm update eslint


------------
    
    