
# Post \#56723678 [Link](https://stackoverflow.com/questions/56723678/)

## Node --experimental-modules - Error: Cannot find module

**Vote**: 64 (104/702) **Views**: 63528 (135/702) 

**Internal ID** \#0-0-79

Created at 2019-06-23 12:04:21

Tags: `javascript` `node.js` `ecmascript-6` `google-chrome-devtools`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am getting an error when trying to import a local file, though no problem when using npm packages.


# server.js



```
import express from 'express'
import next from 'next'

import apis from './src/server/api'
```



# api.js



```
export default {
  ello: 'bye',
  jamie: 'hey'
}
```



# Starting app



```
node --experimental-modules --inspect server.js
```



# Error



```
For help, see: https://nodejs.org/en/docs/inspector
(node:20153) ExperimentalWarning: The ESM module loader is experimental.
internal/modules/esm/default_resolve.js:59
  let url = moduleWrapResolve(specifier, parentURL);
            ^

Error: Cannot find module '/var/www/goldendemon.hutber.com/src/server/api' imported from /var/www/goldendemon.hutber.com/server.js
    at Loader.resolve [as _resolve] (internal/modules/esm/default_resolve.js:59:13)
    at Loader.resolve (internal/modules/esm/loader.js:70:33)
    at Loader.getModuleJob (internal/modules/esm/loader.js:143:40)
    at ModuleWrap.<anonymous> (internal/modules/esm/module_job.js:43:40)
    at link (internal/modules/esm/module_job.js:42:36) {
  code: 'ERR_MODULE_NOT_FOUND'
}
```



----------
        
## Answer \#0

**Accepted** Vote: 112

Created at 2019-06-23 12:16:47

------------

I'm answering my own question if anybody else has this problem.
It turns out in experimental mode you need to define the full path with extension. So I am trying to import `index.js` thinking it will know.
To fix it:
```
import express from 'express'
import next from 'next'
import api from './src/server/api/index.js'
```



------------
    
    
## Answer \#1

 Vote: 52

Created at 2019-08-19 14:39:33

------------

on node 12 you have 2 options:


1. use type="module" on package.json, experimental modules and specify extensions with specidier-resolution like this: node --experimental-modules --es-module-specifier-resolution=node src/index
2. or not using specifier-resolution. Keep in mind you'll have to specify the extension of your files every where.




------------
    
    
## Answer \#2

 Vote: 3

Created at 2019-06-24 08:17:15

------------

It should also work if you name your module file with a `.mjs` extension. Also, other ways to enable ESM are mentioned [here](https://nodejs.org/api/esm.html#esm_enabling).
> Node.js will treat the following as ES modules when passed to node as the initial input, or when referenced by import statements within ES module code:- Files ending in .mjs.- Files ending in .js, or extensionless files, when the nearest parent package.json file contains a top-level field "type" with a value of "module".- Strings passed in as an argument to --eval or --print, or piped to node via STDIN, with the flag --input-type=module.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-07-13 21:45:53

------------

Or do like I did and just use transpilation the minute your source code deals with ES style module imports or some other non-standard JavaScript code, (E.g. TypeScript) on Node. For reference see this quick bash script I wrote, saved as `.script/run-it.sh` inside of my Node project:
```
#!/bin/bash

script=$1
out_path==/tmp/$script-out.js
npx esbuild --platform=node --bundle --outfile=$out_path $script
node $out_path
```

I added it as a run script in my `package.json`:
```
"scripts": {
    "test": "sst test",
    "start": "sst start",
    "build": "sst build",
    "deploy": "sst deploy",
    "remove": "sst remove",
    "run-it": "./.script/run-it.sh"
  },
```

And my target script (`import-test.js`), what I want to emit/transpile as JavaScript code:
```
import { default as myImport } from './lib/index.js'
console.log(myImport)
```

And now I run it:
```
$ npm run run-it ./import-test.js 

> my-nop@0.1.0 run-it /Users/jmquij0106/git/a-rebalancing-act
> ./.script/run-it.sh "./import-test.js"

[Function: main]
```

Bottomline is spare yourself the pain and just emit CommonJS compliant code whenever dealing with ES Modules on Node.js, see [this comment/issue](https://github.com/microsoft/TypeScript/issues/2719#issuecomment-111213184).


------------
    
    