
# Post \#53441292 [Link](https://stackoverflow.com/questions/53441292/)

## Why `downlevelIteration` is not on by default?

**Vote**: 149 (34/702) **Views**: 86969 (97/702) 

**Internal ID** \#0-0-43

Created at 2018-11-23 05:54:35

Tags: `typescript`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

When targeting ES5 with usage of spread operator `...` to convert an `Iterator` to an `Array`, it shows the error to use `-downlevelIteration` compiler option. Once it is on, spread operators seem just work flawlessly.

I wonder why is there a need to specify this? Are there any downsides/limitation when it is enabled besides adding more emitted generated code from tslib?

Another example:  (eg. 3):

```
[...Array(3).keys()]  // output: [0, 1, 2]
```


It displays an error in VS Code:
[](https://i.stack.imgur.com/cSrYT.png)

Error message from `tsc`:

> Type 'IterableIterator' is not an array type or a string type. Use compiler option '--downlevelIteration' to allow iterating of iterators.

[Edit and view the code and error in TypeScript Playground](https://www.typescriptlang.org/play/#src=%5B...Array(3).keys()%5D)


----------
        
## Answer \#0

**Accepted** Vote: 97

Created at 2019-03-11 18:37:38

------------

After reading the [release notes](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-3.html#new---downleveliteration) and the article [Downlevel Iteration for ES3/ES5 in TypeScript](https://mariusschulz.com/articles/downlevel-iteration-for-es3-es5-in-typescript), I believe the answer to this question is that `downlevelIteration` is disabled because you need to decide (via configuration) how you want TypeScript to handle the compilation of compatibility code (to support older versions of Javascript).
As the more lengthy explanation in the article makes clear, you have to make a decision as to if you want TypeScript to inline necessary helper functions (simple, but can result in larger production bundle size) or if you wish to configure TypeScript to use [tslib](https://www.npmjs.com/package/tslib) as a dependency and then make calls to its external methods.
I highly recommend reading [Downlevel Iteration for ES3/ES5 in TypeScript](https://mariusschulz.com/articles/downlevel-iteration-for-es3-es5-in-typescript) for a deeper understanding… and possibly an alternate solution to your initial issue.


------------
    
    
## Answer \#1

 Vote: 65

Created at 2021-09-03 13:58:13

------------

tl;dr: in your case, use `Array.from(foo)`:
```
Array.from(Array(3).keys())
```

Downleveling is TypeScript’s term for transpiling to an older version of JavaScript. This flag is to enable support for a more accurate implementation of how modern JavaScript iterates through new concepts in older JavaScript runtimes.
ECMAScript 6 added several new iteration primitives: the `for / of` loop (`for (el of arr)`), Array spread (`[a, ...b]`), argument spread (`fn(...args)`), and `Symbol.iterator`. `--downlevelIteration` allows for these iteration primitives to be used more accurately in ES5 environments if a `Symbol.iterator` implementation is present.
Since in the question we faced the case of array spread, lets dig this one. Here is an array spread:
```
// Make a new array who elements are 1 followed by the elements of arr2
const arr = [1, ...arr2];
```

Based on the description, it sounds easy to downlevel to ES5:
```
// The same, right?
const arr = [1].concat(arr2);
```

However, this is observably different in certain rare cases. For example, if an array has a “hole” in it, the missing index will create an own property if spreaded, but will not if built using `concat`:
```
// Make an array where the '1' element is missing
let missing = [0, , 1];
let spreaded = [...missing];
let concated = [].concat(missing);
// true
"1" in spreaded;
// false
"1" in concated;
```

`downlevelIteration` will use `Symbol.iterator` (if present) to more accurately emulate ES 6 behavior.
more informations and example [here](https://www.typescriptlang.org/tsconfig#downlevelIteration)
Now in your case, you don't want to change this behavior just to generate a basic range. Instead of `[...Array(3).keys()]`, you can rely on `Array.from(Array(3).keys())`


------------
    
    
## Answer \#2

 Vote: 24

Created at 2022-10-29 05:50:44

------------

All you have to do is change  to  in the tsconfig.json file.
[](https://i.stack.imgur.com/XJ6xJ.png)


------------
    
    