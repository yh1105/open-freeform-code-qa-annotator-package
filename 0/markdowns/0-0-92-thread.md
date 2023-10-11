
# Post \#64361940 [Link](https://stackoverflow.com/questions/64361940/)

## Webpack Error - configuration.node has an unknown property 'fs'

**Vote**: 53 (119/702) **Views**: 30613 (230/702) 

**Internal ID** \#0-0-92

Created at 2020-10-14 21:46:33

Tags: `javascript` `node.js` `webpack` `antlr4`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have encountered an error when using the latest version of Webpack (5.1.0). It looks like the configuration is throwing an error because the validation schema is too restrictive. Here is my webpack configuration file in a gist, and the error message I am seeing.

[https://gist.github.com/adarshbhat/3ec5950b66b78102da0cf46e51a3d633](https://gist.github.com/adarshbhat/3ec5950b66b78102da0cf46e51a3d633)

```
[webpack-cli] 
Invalid configuration object. Webpack has been initialized using a configuration object that does not match the API schema.
 - configuration.node should be one of these:
   false | object { __dirname?, __filename?, global? }
   -> Include polyfills or mocks for various node stuff.
   Details:
    * configuration.node has an unknown property 'module'. These properties are valid:
      object { __dirname?, __filename?, global? }
      -> Options object for node compatibility features.
    * configuration.node has an unknown property 'net'. These properties are valid:
      object { __dirname?, __filename?, global? }
      -> Options object for node compatibility features.
    * configuration.node has an unknown property 'fs'. These properties are valid:
      object { __dirname?, __filename?, global? }
      -> Options object for node compatibility features.
npm ERR! code ELIFECYCLE
npm ERR! errno 2
npm ERR! jstest@1.0.0 start: `webpack serve`
npm ERR! Exit status 2
```

I am attempting to use a parser generator library (antlr4) that works both in a Node.js environment, as well as in browsers. It looks like the library code is requiring global objects like fs, and if it is empty, assumes that it is in a browser environment. According to the [documentation of Antlr4](https://github.com/antlr/antlr4/blob/master/doc/javascript-target.md) and [that of Webpack](https://webpack.js.org/configuration/node/) , this is a supported configuration file. But it is not working. Please help.

- - - - 

Antlr JavaScript documentation has now been updated with the new configuration for Webpack 5


----------
        
## Answer \#0

**Accepted** Vote: 73

Created at 2020-10-16 15:55:17

------------

I managed to get this to work with some help from the Webpack team. Using the following webpack configuration as recommended by the antlr4 documentation is no longer supported.

```
{
  node: {
    fs: 'empty',
    module: 'empty',
    net: 'empty'
  }
}
```


```
{
  resolve: {
    fallback: {
      fs: false
    }
  }
}
```

With this, I was able to get my JavaScript parser working.
Please note that there is an ongoing effort to update antlr4 to generate ES6 based code. This configuration may not be necessary in the future.


------------
    
    
## Answer \#1

 Vote: 30

Created at 2021-07-24 15:54:34

------------

 users:
```
module.exports = {
  webpack: (config, { isServer }) => {
    if (!isServer) {
      config.resolve.fallback.fs = false;
    }
    return config;
  },
}
```

[Source](https://github.com/vercel/next.js/issues/7755#issuecomment-812805708)


------------
    
    
## Answer \#2

 Vote: 12

Created at 2021-03-16 13:46:14

------------

Exactly for rails webpacker, if you are using it, the solution should be a:
Inside any of config/webpack/*.js which uses the plain custom config (see [https://github.com/rails/webpacker#webpack-configuration](https://github.com/rails/webpacker#webpack-configuration) for more), just add the code resolving and removing the node property.
```
const { environment } = require('@rails/webpacker')

const customConfig = {
  resolve: {
    fallback: {
      dgram: false,
      fs: false,
      net: false,
      tls: false,
      child_process: false
    }
  }
};

environment.config.delete('node.dgram')
environment.config.delete('node.fs')
environment.config.delete('node.net')
environment.config.delete('node.tls')
environment.config.delete('node.child_process')

environment.config.merge(customConfig);

module.exports = environment
```



------------
    
    
## Answer \#3

 Vote: 4

Created at 2022-01-19 10:36:18

------------

I have encountered with same issue while working with Next js, I have searched different solutions like `webpack5: false` or `fs: false`, but that didn't work for me.
```
config.node = {
  // fs: 'empty'
  global: true,
  __filename: true,
  __dirname: true,
}
```

this did work for me, because in webpack 3.0.0, the node option may be set to false to completely turn off the NodeStuffPlugin, as we are working in JS and specially in Next-JS which requires Node-JS, so we don't have to turn it off completely because while 'false' the Webpack wouldn't touch your '__filename' code and  your '__dirname' code.



------------
    
    
## Answer \#4

 Vote: 1

Created at 2020-10-23 15:05:40

------------

Interesting enough, I get in case of `path` (but not with `file`) the following message, more clear of webpack CLI:
```
BREAKING CHANGE: webpack < 5 used to include polyfills for node.js core modules by default.
This is no longer the case. Verify if you need this module and configure a polyfill for it.

If you want to include a polyfill, you need to:
    - add a fallback 'resolve.fallback: { "path": require.resolve("path-browserify") }'
    - install 'path-browserify'
If you don't want to include a polyfill, you can use an empty module like this:
    resolve.fallback: { "path": false }
```

However it seems to me this info should also be found here, and removed the statement that any built-in module can be used as property there: [https://webpack.js.org/configuration/node/#node](https://webpack.js.org/configuration/node/#node)


------------
    
    
## Answer \#5

 Vote: 1

Created at 2021-07-01 19:42:50

------------

If someone is facing this error in Next JS, I found the solution by disabling webpack 5 which is set by default.
disable it by `webpack5: false` in next.config.js


------------
    
    