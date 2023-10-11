
# Post \#54879588 [Link](https://stackoverflow.com/questions/54879588/)

## What will happen if sourcemap is set as false in Angular

**Vote**: 30 (193/702) **Views**: 37029 (201/702) 

**Internal ID** \#0-2-160

Created at 2019-02-26 06:28:31

Tags: `html` `node.js` `angular` `typescript` `angular-cli`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

I'm new in Angular. I saw `sourcemap` in `tsconfig.json` and by default it is `"sourceMap": true`. I had few doubts and found [this link](https://stackoverflow.com/questions/21719562/javascript-map-files-javascript-source-maps) useful. Still I have the following doubt regarding the same.

I set `"sourceMap": false`, but couldn't find any change in the app. What will be the actual change if I set so?


----------
        
## Answer \#0

**Accepted** Vote: 62

Created at 2019-02-26 06:33:53

------------

Nothing will change in how the app runs. 

The change will be in your debugging experience.

[Source maps](https://www.html5rocks.com/en/tutorials/developertools/sourcemaps/) are helpful for debugging code. You write your code in TypeScript, and the compiler turns that  code into JavaScript. When your app is running in a browser like Firefox, the browser is running the JavaScript. Even though the browser is running that JavaScript, if you open the debugger in Firefox, the debugger will display the TypeScript source code and will let you set break points in it. The debugger is able to do that because of source maps, which map the TypeScript source code to the JavaScript runtime code. That is what source maps do: they map the source code to the runtime code to enable source code debugging at runtime.


------------
    
    
## Answer \#1

 Vote: 6

Created at 2019-02-26 07:02:07

------------

`sourceMap` is just for development experience (debug) and normally you don't need these files in production build and if you check angular.json you will found that it 's set to false for you

```
"configurations": {
    "production": {
      "fileReplacements": [
        {
          "replace": "src/environments/environment.ts",
          "with": "src/environments/environment.prod.ts"
        }
      ],
      "optimization": true,
      "outputHashing": "all",
      "sourceMap": false,  <----
      "extractCss": true,
      "namedChunks": false,
      "aot": true,
      "extractLicenses": true,
      "vendorChunk": false,
      "buildOptimizer": true
    }
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2019-02-26 06:31:36

------------

When the `sourceMap` set to `false`. the output will be built with out a sourcemap file. And you can't debug with the browser on ts file without that.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-12-22 10:29:08

------------

sourcemap property enhances your debugging experience, even though the browser can't be able to understand typescript it manages to map your typescript code to javascript code. if in case we need to disable it we need to modify in  file
```
{
  "sourceMap": false, -- modify this attribute
    "fileReplacements": [
      {
        "replace": "src/environments/environment.ts",
        "with": "src/environments/environment.prod.ts"
      }
    ]
}
```



------------
    
    