
# Post \#56238356 [Link](https://stackoverflow.com/questions/56238356/)

## Understanding esModuleInterop in tsconfig file

**Vote**: 368 (4/702) **Views**: 272605 (25/702) 

**Internal ID** \#0-0-50

Created at 2019-05-21 12:31:12

Tags: `typescript`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I was checking out someone `.tsconfig` file and there I spotted `--esModuleInterop`
This is his `.tsconfig` file
```
{
  "compilerOptions": {
    "moduleResolution": "node",
    "target": "es6",
    "module": "commonjs",
    "lib": ["esnext"],
    "strict": true,
    "sourceMap": true,
    "declaration": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "declarationDir": "./dist",
    "outDir": "./dist",
    "typeRoots": ["node_modules/@types"]
  },
  "include": ["src/**/*.ts"],
  "exclude": ["node_modues"]
}
```

Here, My primary question is what is   `"esModuleInterop": true,` and
`"allowSyntheticDefaultImports": true,`. I know they are sort of dependent on the  `"module": "commonjs",` . Can someone try to explain it in the best human language possible?
The official docs for `allowSyntheticDefaultImports` states
> Allow default imports from modules with no default export. This does
not affect code emit, just typechecking.
What does that mean? If there isn't any export default then I think the only use case of the import default would be to initialize something? Like a singleton?
The following question/answer does not make sense as well
[Is there a way to use --esModuleInterop in tsconfig as opposed to it being a flag?](https://stackoverflow.com/questions/48785451/is-there-a-way-to-use-esmoduleinterop-in-tsconfig-as-opposed-to-it-being-a-fla)
And `--esModuleInterop` definition on the compiler page
> Emit __importStar and __importDefault helpers for runtime babel
ecosystem compatibility and enable --allowSyntheticDefaultImports for
typesystem compatibility.
Also seemed difficult for me to understand/comprehend


----------
        
## Answer \#0

**Accepted** Vote: 471

Created at 2019-05-28 18:29:08

------------


# Problem statement


Problem occurs when we want to import CommonJS module into ES6 module codebase.
Before these flags we had to import CommonJS modules with star (`* as something`) import:
```
// node_modules/moment/index.js
exports = moment
```

```
// index.ts file in our app
import * as moment from 'moment'
moment(); // not compliant with es6 module spec

// transpiled js (simplified):
const moment = require("moment");
moment();
```

We can see that `*` was somehow equivalent to `exports` variable. It worked fine, but it wasn't compliant with es6 modules spec. In spec, the namespace record in star import (`moment` in our case) can be only a plain object, not callable (`moment()` is not allowed).

# Solution


With flag `esModuleInterop` we can import CommonJS modules in compliance with `es6` modules spec. Now our import code looks like this:
```
// index.ts file in our app
import moment from 'moment'
moment(); // compliant with es6 module spec

// transpiled js with esModuleInterop (simplified):
const moment = __importDefault(require('moment'));
moment.default();
```

It works and it's perfectly valid with es6 modules spec, because `moment` is not namespace from star import, it's default import.
But how does it work? As you can see, because we did a default import, we called the `default` property on a `moment` object. But we didn't declare a `default` property on the `exports` object in the moment library. The key is the `__importDefault` function. It assigns module (`exports`) to the `default` property for CommonJS modules:
```
var __importDefault = (this && this.__importDefault) || function (mod) {
    return (mod && mod.__esModule) ? mod : { "default": mod };
};
```

As you can see, we import es6 modules as they are, but CommonJS modules are wrapped into an object with the `default` key. This makes it possible to import defaults on CommonJS modules.
`__importStar` does the similar job - it returns untouched esModules, but translates CommonJS modules into modules with a `default` property:
```
// index.ts file in our app
import * as moment from 'moment'

// transpiled js with esModuleInterop (simplified):
const moment = __importStar(require("moment"));
// note that "moment" is now uncallable - ts will report error!
```

```
var __importStar = (this && this.__importStar) || function (mod) {
    if (mod && mod.__esModule) return mod;
    var result = {};
    if (mod != null) for (var k in mod) if (Object.hasOwnProperty.call(mod, k)) result[k] = mod[k];
    result["default"] = mod;
    return result;
};
```


# Synthetic imports


And what about `allowSyntheticDefaultImports` - what is it for? Now the docs should be clear:
`Allow default imports from modules with no default export. This does not affect code emit, just typechecking.`
In `moment` typings we don't have specified default export, and we shouldn't have, because it's available only with flag `esModuleInterop` on. So `allowSyntheticDefaultImports` will not report an error if we want to import default from a third-party module which doesn't have a default export.


------------
    
    
## Answer \#1

 Vote: 40

Created at 2019-05-28 08:38:37

------------

`esModuleInterop` generates the helpers outlined in the docs. Looking at the generated code, we can see exactly what these do:

```
//ts 
import React from 'react'
//js 
var __importDefault = (this && this.__importDefault) || function (mod) {
    return (mod && mod.__esModule) ? mod : { "default": mod };
};
Object.defineProperty(exports, "__esModule", { value: true });
var react_1 = __importDefault(require("react"));
```


`__importDefault`: If the module is not an `es` module then what is returned by require becomes the default. This means that if you use default import on a `commonjs` module, the whole module is actually the default.

`__importStar` is best described in [this PR](https://github.com/microsoft/TypeScript/issues/16093): 

> TypeScript treats a namespace import (i.e. `import * as foo from "foo"`) as equivalent to `const foo = require("foo")`. Things are simple here, but they don't work out if the primary object being imported is a primitive or a value with call/construct signatures.
  ECMAScript basically says a namespace record is a plain object.Babel first requires in the module, and checks for a property named `__esModule`. If `__esModule` is set to `true`, then the behavior is the same as that of TypeScript, but otherwise, it synthesizes a namespace record where:
1. All properties are plucked off of the require'd module and made available as named imports.
2. The originally require'd module is made available as a default import.



So we get this: 

```
// ts
import * as React from 'react'

// emitted js
var __importStar = (this && this.__importStar) || function (mod) {
    if (mod && mod.__esModule) return mod;
    var result = {};
    if (mod != null) for (var k in mod) if (Object.hasOwnProperty.call(mod, k)) result[k] = mod[k];
    result["default"] = mod;
    return result;
};
Object.defineProperty(exports, "__esModule", { value: true });
var React = __importStar(require("react"));
```


`allowSyntheticDefaultImports` is the companion to all of this, setting this to false will not change the emitted helpers (both of them will still look the same). But it will raise a typescript error if you are using default import for a commonjs module. So this `import React from 'react'` will raise the error `Module '".../node_modules/@types/react/index"' has no default export.` if `allowSyntheticDefaultImports` is `false`.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-08-23 13:44:35

------------

P.S. : This answer is the understanding I got from reading `@Krzysztof Grzybek`'s answer and documentation link I provided
```
// node_modules/moment/index.js
1. exports = moment
```

```
// index.ts file in our app
2. import * as moment from 'moment'
3. moment(); // not compliant with es6 module spec

// transpiled js (simplified):
4. const moment = require("moment");
5. moment();
```

Assuming `allowSyntheticDefaultImports` is set to true, which tells typescript that it is ok to write default import from modules which do not actually have default export. (Moment here does not have default export)
If we set `esModuleInterop` to true, then line no 3, will give following error
```
This expression is not callable.
  Type 'typeof moment' has no call signatures.
```

Because, AMD/UMD modules are not treated as es6 modules and two separate functions are used to import  viz. __importStar and __importDefault [https://www.typescriptlang.org/tsconfig#esModuleInterop](https://www.typescriptlang.org/tsconfig#esModuleInterop) so line no 3 is trying to import object, and thus it is not callable. However most projects that I have seen tries to keep `esModuleInterop`:`false` thus they do not want to treat UMD modules different from es6 modules


------------
    
    