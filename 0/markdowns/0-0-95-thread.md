
# Post \#66089016 [Link](https://stackoverflow.com/questions/66089016/)

## TypeError: Class extends value undefined is not a constructor or null while running next.js

**Vote**: 3 (575/702) **Views**: 14346 (366/702) 

**Internal ID** \#0-0-95

Created at 2021-02-07 14:26:52

Tags: `reactjs` `next.js`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

While running
```
npm run dev
```

I suddenly start getting error without pretty much any change to the code of the project:
```
TypeError: Class extends value undefined is not a constructor or null
    at Object.618 (/Users/EugeneBos/Websites/dotalaning/node_modules/next/dist/compiled/postcss-scss/scss-syntax.js:1:100)
    at __nccwpck_require__ (/Users/EugeneBos/Websites/dotalaning/node_modules/next/dist/compiled/postcss-scss/scss-syntax.js:1:11735)
    at Object.270 (/Users/EugeneBos/Websites/dotalaning/node_modules/next/dist/compiled/postcss-scss/scss-syntax.js:1:413)
    at __nccwpck_require__ (/Users/EugeneBos/Websites/dotalaning/node_modules/next/dist/compiled/postcss-scss/scss-syntax.js:1:11735)
    at Object.327 (/Users/EugeneBos/Websites/dotalaning/node_modules/next/dist/compiled/postcss-scss/scss-syntax.js:1:260)
    at __nccwpck_require__ (/Users/EugeneBos/Websites/dotalaning/node_modules/next/dist/compiled/postcss-scss/scss-syntax.js:1:11735)
    at Object.845 (/Users/EugeneBos/Websites/dotalaning/node_modules/next/dist/compiled/postcss-scss/scss-syntax.js:1:3733)
    at __nccwpck_require__ (/Users/EugeneBos/Websites/dotalaning/node_modules/next/dist/compiled/postcss-scss/scss-syntax.js:1:11735)
    at /Users/EugeneBos/Websites/dotalaning/node_modules/next/dist/compiled/postcss-scss/scss-syntax.js:1:11863
    at Object.<anonymous> (/Users/EugeneBos/Websites/dotalaning/node_modules/next/dist/compiled/postcss-scss/scss-syntax.js:1:11889)
    at Module._compile (node:internal/modules/cjs/loader:1108:14)
    at Object.Module._extensions..js (node:internal/modules/cjs/loader:1137:10)
    at Module.load (node:internal/modules/cjs/loader:973:32)
    at Function.Module._load (node:internal/modules/cjs/loader:813:14)
    at Module.require (node:internal/modules/cjs/loader:997:19)
    at require (node:internal/modules/cjs/helpers:92:18)
```

Modifying the code of the app so it won't include any files or load anything still produces the same error.


----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2021-02-07 14:30:02

------------

Ok, removing node_modules and installing everything again have helped.
```
rm -rf node_modules
npm install
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-01-26 23:11:39

------------

I know there's nothing wrong with the good old `rm -rf` but `npkill` is quite a nice tool:

1. npx npkill
2. Remove the appropriate node_modules folder
3. yarn or npm i




------------
    
    
## Answer \#2

 Vote: 1

Created at 2023-01-25 21:20:03

------------

Just update npm...
Sometimes it relates to the npm version, try to use the newest version of npm. It was happening to me on , once I updated npm to  the issue was resolved.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-04-06 06:30:56

------------

This might happen if you mix yarn with npm dependencies as well.
Try

1. rm-rf node_modules
2. npm install


This worked in my case


------------
    
    