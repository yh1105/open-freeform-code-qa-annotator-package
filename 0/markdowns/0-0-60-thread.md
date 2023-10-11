
# Post \#57459917 [Link](https://stackoverflow.com/questions/57459917/)

## How to fix this error : " Module not found :can't resolve popper.js "

**Vote**: 81 (77/702) **Views**: 124983 (70/702) 

**Internal ID** \#0-0-60

Created at 2019-08-12 10:58:09

Tags: `jquery` `reactjs` `react-bootstrap` `react-popper`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

After import the Bootstrap and Jquery this error is showning when compiling.

```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import 'bootstrap/dist/css/bootstrap.css';
import './index.css';

global.jQuery = require('jquery');
require('bootstrap');
```



----------
        
## Answer \#0

**Accepted** Vote: 214

Created at 2019-08-12 11:29:05

------------

Popper.js is a dependency of Bootstrap 4 which is used for showing popups. It is a peer dependency of bootstrap meaning it is something that bootstrap requires but doesn't include with itself when installed.
So to install popper.js run
```
npm install popper.js --save
```

It is setup as a peer dependency because some people just use the css of Bootstrap without the javascript.
jQuery and popper.js are the peer dependencies which needs to be installed separately. If you require the javascript of Bootstrap you need to install jQuery and popper.js alongside Bootstrap 4.

---


 requires "Popper.js ", not Popper.js. You should run this instead:
```
npm install @popperjs/core --save
```

(thanks @Kyouma for adding this in the comments)


------------
    
    
## Answer \#1

 Vote: 14

Created at 2022-01-10 13:39:07

------------

For  you are to
npm users:
`npm install @popperjs/core --save`
for yarn users:
`yarn add @popperjs/core`
then import bootstrap like so:
```
import 'bootstrap/dist/js/bootstrap.bundle';
```



------------
    
    
## Answer \#2

 Vote: 5

Created at 2020-04-22 17:30:40

------------

It's simple you can [Visit this](https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js), And save the file as popper.min.js

then import it.

```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import 'bootstrap/dist/css/bootstrap.css';
import './index.css';
import 'bootstrap/dist/js/popper.min.js';
global.jQuery = require('jquery');
require('bootstrap');
```



------------
    
    
## Answer \#3

 Vote: 5

Created at 2022-03-19 07:20:41

------------

I also ran into the same issue and was installing popper using the following command :-
```
npm install vue-popperjs --save
```

But, Then I realise I was using bootstrap 5, Which no longer supports this command.
So The command used with bootstrap 5 for "Popperjs Core",
```
npm install @popperjs/core --save
```



------------
    
    
## Answer \#4

 Vote: 4

Created at 2021-01-03 14:53:27

------------

Like tomi0505 have written, you need to import bootstrap and other things like this:
```
import 'jquery';
import '@popperjs/core'; // Edit here
import 'bootstrap/dist/js/bootstrap.bundle';
```

After that it will work fine, I hope.


------------
    
    
## Answer \#5

 Vote: 3

Created at 2021-05-10 15:29:46

------------

You can direct import like
```
import bootstrapBundle from './../path to node modules/../node_modules/bootstrap/dist/js/bootstrap.bundle.min.js';
```

no need to install popper


------------
    
    
## Answer \#6

 Vote: 2

Created at 2020-09-10 07:19:46

------------

Add the popper.js as dependency in your package.json file like this:
```
{
  "dependencies": {
    ...,
    "popper.js": "1.16.1",
    ...,
  }
}
```

and also add popper.js as required import before adding required bootstrap, as following:
```
require('popper.js');
require('bootstrap');
```



------------
    
    
## Answer \#7

 Vote: 2

Created at 2021-11-13 14:33:29

------------

 requires "Popper.js Core", You Can run this instead:
```
npm install @popperjs/core --save
```



------------
    
    
## Answer \#8

 Vote: 1

Created at 2020-12-19 15:00:48

------------

if you have installed:
`npm install jquery popper.js`

instead:
`global.jQuery = require('jquery');`
`require('bootstrap');`

add:

`import $ from 'jquery';`
`import Popper from 'popper.js';`
`import 'bootstrap/dist/js/bootstrap.bundle.min';`

[look this](https://officialrajdeepsingh.medium.com/how-to-use-bootstrap-in-react-js-adf50165c7a1)


------------
    
    