
# Post \#59787574 [Link](https://stackoverflow.com/questions/59787574/)

## TypeScript tsconfig settings for Node.js 12?

**Vote**: 92 (70/702) **Views**: 42481 (187/702) 

**Internal ID** \#0-0-96

Created at 2020-01-17 12:42:08

Tags: `node.js` `typescript`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

What is the optimal TypeScript `tsconfig` settings for outputting code that's going to be run on Node.js 12?


----------
        
## Answer \#0

**Accepted** Vote: 250

Created at 2020-01-17 12:42:08

------------

As of Node.js `12.0.0`, 100% of ES2019 is supported. If you know that you are targeting that version or newer, the optimal config would look like this:
- `"module": "commonjs"`Node.js is on its way to add ES-Modules, but for now we'll have to stick with CommonJS.- `"target": "es2019"`This tells TypeScript that it's okay to output JavaScript  with features from ES2019. In practice, this means that it will e.g. output object rest/spread properties & async/await syntax instead of embedding a polyfill.- `"lib": ["es2019", "es2020.bigint", "es2020.string", "es2020.symbol.wellknown"]`This tells TypeScript that it's okay to use  introduced in ES2019 or earlier. In practice, this means that you can use e.g. `String.prototype.trimStart` and `Array.prototype.flat`.In addition to ES2019, Node.js 12 also supports `BigInt` & `matchAll` from ES2020, therefor we include the additional definitions from ES2020.
The full config would thus be:
```
{
  "compilerOptions": {
    "lib": ["es2019", "es2020.bigint", "es2020.string", "es2020.symbol.wellknown"],
    "module": "commonjs",
    "target": "es2019"
  }
}
```


---


`12.9.0`, you can simply specify `"lib": ["es2020"]` as that version supports all new  introduced in ES2020. It doesn't support the new JavaScript  though, so you still have to stay on `"target": "es2019"`.
The full config would thus be:
```
{
  "compilerOptions": {
    "lib": ["es2020"],
    "module": "commonjs",
    "target": "es2019"
  }
}
```


---


If you are running Node.js 18 you can see my [similar answer for Node.js 18 here](https://stackoverflow.com/a/72380008/148072)
If you are running Node.js 16 you can see my [similar answer for Node.js 16 here](https://stackoverflow.com/a/67371788/148072)
If you are running Node.js 14 you can see my [similar answer for Node.js 14 here](https://stackoverflow.com/a/61305579/148072)
If you are running Node.js 10 you can see my [similar answer for Node.js 10 here](https://stackoverflow.com/a/57607634/148072)
If you are running Node.js 8 you can see my [similar answer for Node.js 8 here](https://stackoverflow.com/a/50466512/148072)


------------
    
    
## Answer \#1

 Vote: 25

Created at 2020-09-04 11:56:11

------------

TL:DR
TypeScript maintains a mapping of target, module and lib corresponding to the node version. You can find it here
[https://github.com/microsoft/TypeScript/wiki/Node-Target-Mapping](https://github.com/microsoft/TypeScript/wiki/Node-Target-Mapping)


------------
    
    
## Answer \#2

 Vote: 7

Created at 2021-09-06 09:34:23

------------

It is much easier using [tsconfig bases](https://github.com/tsconfig/bases).
Run `npm install --save-dev @tsconfig/node12` and then in tsconfig.json
```
{
  "extends": "@tsconfig/node12/tsconfig.json",
  // ...your configrations
}
```

There is a few tsconfig bases for different environments and the community keeps adding more.


------------
    
    