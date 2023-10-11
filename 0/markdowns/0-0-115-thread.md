
# Post \#57070052 [Link](https://stackoverflow.com/questions/57070052/)

## create-react-app Typescript 3.5, Path Alias

**Vote**: 58 (113/702) **Views**: 49286 (165/702) 

**Internal ID** \#0-0-115

Created at 2019-07-17 06:49:53

Tags: `typescript` `create-react-app`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am trying to setup Path alias in my project by adding these values to tsconfig.json:

```
"compilerOptions": {
    "baseUrl": "src",
    "paths": {
      "@store/*": ["store/*"]
    },
```


And if I create an import, neither IntelliJ or VSCode bother me:

```
import { AppState } from '@store/index';
```


But when I compile the application I get this warning:

```
The following changes are being made to your tsconfig.json file:
  - compilerOptions.paths must not be set (aliased imports are not supported)
```


And it bombs saying it cannot find the reference:

```
TypeScript error in C:/xyz.tsx(2,26):
Cannot find module '/store'.  TS2307
```


Is there any workaround or it is not supported by `create-react-app --typescript`?


----------
        
## Answer \#0

**Accepted** Vote: 15

Created at 2020-03-09 09:56:39

------------

The alias solution for craco or rewired  is [react-app-alias](https://github.com/oklas/react-app-alias) for systems as: [craco](https://github.com/gsoft-inc/craco/blob/master/packages/craco), [react-app-rewired](https://github.com/timarney/react-app-rewired), [customize-cra](https://github.com/arackaf/customize-cra)
According docs of mentioned systems replace `react-scripts` in `package.json` and configure next:

## react-app-rewired


```
// config-overrides.js
const {aliasWebpack, aliasJest} = require('react-app-alias')

const options = {} // default is empty for most cases

module.exports = aliasWebpack(options)
module.exports.jest = aliasJest(options)
```


## craco


```
// craco.config.js
const {CracoAliasPlugin} = require('react-app-alias')

module.exports = {
  plugins: [
    {
      plugin: CracoAliasPlugin,
      options: {}
    }
  ]
}
```


## all


Configure aliases in json like this:
```
// tsconfig.paths.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "example/*": ["example/src/*"],
      "@library/*": ["library/src/*"]
    }
  }
}
```

And add this file in `extends` section of main typescript config file:
```
// tsconfig.json
{
  "extends": "./tsconfig.paths.json",
  // ...
}
```



------------
    
    
## Answer \#1

 Vote: 49

Created at 2020-08-12 21:46:52

------------

In my case ,i solved this issue using [craco](https://github.com/gsoft-inc/craco/blob/master/packages/craco) and [craco-alias](https://github.com/risenforces/craco-alias)

1. Install craco and craco-alias : npm install @craco/craco --save && npm i -D craco-alias
2. Create tsconfig.paths.json in root directory {
    "compilerOptions": {
        "baseUrl": "./src",
        "paths": {
           "@components/*" : ["./components/*"]
         }
    }
}
3. Extend tsconfig.paths.json in tsconfig.json {
    "extends": "./tsconfig.paths.json",
    //default configs...
}
4. Create craco.config.js in root direcory   const CracoAlias = require("craco-alias");

  module.exports = {
     plugins: [
       {
          plugin: CracoAlias,
          options: {
             source: "tsconfig",
             // baseUrl SHOULD be specified
             // plugin does not take it from tsconfig
             baseUrl: "./src",
             /* tsConfigPath should point to the file where "baseUrl" and "paths" 
             are specified*/
             tsConfigPath: "./tsconfig.paths.json"
          }
       }
    ]
  };
5. in package.json swap "start": "react-scripts start" with "start": "craco start"




------------
    
    
## Answer \#2

 Vote: 9

Created at 2019-10-25 11:53:52

------------

 

Had the same problem. At the time of posting it is currently not supported as is. 

This feature is still in development with the CRA team:
[https://github.com/facebook/create-react-app/pull/6116](https://github.com/facebook/create-react-app/pull/6116)

If you edit the tsconfig CRA removes changes: [https://github.com/facebook/create-react-app/issues/6269](https://github.com/facebook/create-react-app/issues/6269)

There are some hacks in this thread: [https://github.com/facebook/create-react-app/issues/5118](https://github.com/facebook/create-react-app/issues/5118) to get aliases working however require customization to the CRA setup which isn't suitable for my use case (using CRA as is). 


------------
    
    
## Answer \#3

 Vote: 9

Created at 2021-08-13 22:35:55

------------


# Path aliases no longer work in tsconfig.json


, to directly import files relative to the `src` directory
go to your `tsconfig.json` file add base URL to be `"."`
```
"compilerOptions": {
    "baseUrl":".",
    ...
```

then you can directly import stuff from the `src` directory
```
import Myfile from "src/myfile.js"
```



------------
    
    
## Answer \#4

 Vote: 2

Created at 2020-05-13 04:33:17

------------

@oklas. I've resolved using the following in config-overrides.js which is the same as above. But Thanks a lot for sharing this answer:
```
const { alias, configPaths } = require("react-app-rewire-alias");

module.exports = function override(config) {
  alias({
    ...configPaths("base-tsconfig.json"),
  })(config);
  return config;
};
```



------------
    
    
## Answer \#5

 Vote: 2

Created at 2021-10-07 09:37:51

------------

This is an old post, but i've found an extremly simple solution using only package.json features.
```
"dependencies": {
  "@yourAlias": "file:./path/to/your/folder"
}
```

It works both in node.js and webpack projects


------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-06-04 07:11:46

------------

In 2022 for those who works with the [ejected](https://create-react-app.dev/docs/available-scripts#npm-run-eject) TypeScript create-react-app you can set aliases via webpack in `config/webpack.config.js` with the following:
```
// ... the other keys of webpack config;
    resolve: {
        alias: {
            // ... here set your aliases among ones the CRA sets itself
            // NB: __dirname will refer to ./config so you have to put '../src' to move up from ./config folder and set @src tp ./src, not ./config/src;
            '@src': path.join(__dirname, '../src')
            // Here set your other aliases if needed.
        }
    }
```

Then use the alias in your ts/tsx files like this:
```
import logo from '@src/assets/logo.svg';
```

Here is [webpack resolve docs](https://webpack.js.org/configuration/resolve/) for basics.
Note, that the only alias CRA sets itself is `src`, so you can use `import logo from 'src/assets/logo.svg';` with no changes to webpack config. If the alias works in non-ejected app you have to check yourself.


------------
    
    
## Answer \#7

 Vote: 0

Created at 2023-01-12 20:17:01

------------

Another workaround is just naming a folder inside `./src` `@` and setting the `baseURL` in the `tsconfig.json` to `src`.
Imports will then have a leading `@/` like `import { MyComponent } from "@/components/MyComponent";` or `import { TodoEndpoint } from "@/endpoints/TodoEndpoint";`. Since there is no library named `@` in NPM this will not lead to conflict with other `node_modules` imports.
tsconfig.json
```
{
  "compilerOptions": {
    ...
    "baseUrl": "src"
  }
}
```

folder structure
```
+src
| +@
| | + components
| | +-  MyComponent.tsx
| | + endpoints
| | +-  TodoEndpoint.ts
| index.tsx
```



------------
    
    
## Answer \#8

 Vote: -2

Created at 2020-08-23 10:05:33

------------

Have also solved the solution using [craco](https://github.com/gsoft-inc/craco) with `ts-loader` - described here: [https://stackoverflow.com/a/63545611/10813908](https://stackoverflow.com/a/63545611/10813908)
The stated setup essentially allows CRA to be extended upon by overidding the build-in webpack config via craco and also allows CRA code to directly reference typescript files in the external/shared packages in your mono-repo.


------------
    
    