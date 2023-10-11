
# Post \#57712235 [Link](https://stackoverflow.com/questions/57712235/)

## `ReferenceError: TextEncoder is not defined` when running `react-scripts test --env=jsdom`

**Vote**: 33 (185/702) **Views**: 33796 (211/702) 

**Internal ID** \#0-0-130

Created at 2019-08-29 14:20:48

Tags: `reactjs` `tensorflow.js`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm using a TensorFlow encoder in my application. It works fine in my browser when the application is running but I get issues when testing that it builds:

```
$ npx react-scripts test --env=jsdom
FAIL  src/App.test.js
  ● Test suite failed to run

    ReferenceError: TextEncoder is not defined

      16 | import TextField from '@material-ui/core/TextField';
      17 | import Typography from '@material-ui/core/Typography';
    > 18 | import * as mobilenet from '@tensorflow-models/mobilenet';
         | ^
      19 | import * as UniversalSentenceEncoder from '@tensorflow-models/universal-sentence-encoder';
      20 | import * as tf from '@tensorflow/tfjs';
      21 | import axios from 'axios';

      at new PlatformBrowser (node_modules/@tensorflow/tfjs-core/src/platforms/platform_browser.ts:26:28)
      at Object.<anonymous> (node_modules/@tensorflow/tfjs-core/src/platforms/platform_browser.ts:50:30)
      at Object.<anonymous> (node_modules/@tensorflow/tfjs-core/src/index.ts:29:1)
      at Object.<anonymous> (node_modules/@tensorflow/tfjs-converter/src/executor/graph_model.ts:18:1)
      at Object.<anonymous> (node_modules/@tensorflow/tfjs-converter/src/index.ts:17:1)
      at Object.<anonymous> (node_modules/@tensorflow-models/mobilenet/dist/index.js:38:14)
      at Object.<anonymous> (src/components/model.js:18:1)
      at Object.<anonymous> (src/App.js:8:1)
      at Object.<anonymous> (src/App.test.js:3:1)
```


I'd like to get rid of that error. I've tried using the 'text-encoding' package but I'm not sure how get TextEncoder properly defined before the import happens.

Maybe I can set a different option for `--env`?

I get the same error without `--env=jsdom`. I believe I added it after getting similar types of not defined errors and it corrected an issue.

Here is my test:

```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div);
  ReactDOM.unmountComponentAtNode(div);
});
```


So setting `--env=node` does not work either because: `ReferenceError: document is not defined`.


----------
        
## Answer \#0

**Accepted** Vote: 39

Created at 2019-08-29 16:02:04

------------

`jsdom` Doesn't seem to have `TextEncoder` defined in global for the DOM. So you can fill it in with the node.js one.
test/custom-test-env.js:
```
const Environment = require('jest-environment-jsdom');

/**
 * A custom environment to set the TextEncoder that is required by TensorFlow.js.
 */
module.exports = class CustomTestEnvironment extends Environment {
    async setup() {
        await super.setup();
        if (typeof this.global.TextEncoder === 'undefined') {
            const { TextEncoder } = require('util');
            this.global.TextEncoder = TextEncoder;
        }
    }
}
```

`npx react-scripts test --env=./test/custom-test-env.js`


------------
    
    
## Answer \#1

 Vote: 19

Created at 2021-10-27 07:54:49

------------

I am getting same error for my Node.Js project. For testing purpose I used jest there. So following steps are resolved my issue
`step-1:` on the root folder of your project add a file named as jest.config.js
`step-2:` Add the following lines in the jest.config.file:



------------
    
    
## Answer \#2

 Vote: 12

Created at 2022-05-19 20:37:03

------------

Thanks for these answers. A simpler format that seems to work, at least with `testEnvironment: 'jsdom'` is:
```
setupFiles: [`<rootDir>/jest-shim.js`],
```

jest-shim.js:
```
import { ArrayBuffer, TextDecoder, TextEncoder, Uint8Array } from 'util';

global.TextEncoder = TextEncoder;
global.TextDecoder = TextDecoder;
global.ArrayBuffer = ArrayBuffer;
global.Uint8Array = Uint8Array;
```



------------
    
    
## Answer \#3

 Vote: 4

Created at 2021-09-01 20:10:05

------------

I faced this problem when using mongodb. I used [@Phoenix](https://stackoverflow.com/a/57713960/4023416) solution with a little change.
First I used `jest-environment-node` instead of `jest-environment-jsdom`:
```
const NodeEnvironment = require('jest-environment-node');

// A custom environment to set the TextEncoder that is required by mongodb.
module.exports = class CustomTestEnvironment extends NodeEnvironment {
  async setup() {
    await super.setup();
    if (typeof this.global.TextEncoder === 'undefined') {
      const { TextEncoder } = require('util');
      this.global.TextEncoder = TextEncoder;
    }
  }
}
```

Then I added the environment in the jest configs for all tests as [Cava](https://stackoverflow.com/users/3845972/cava) said in the comments:
```
// package.json
{
  ...
  "jest": {
    ...
    "testEnvironment": "<rootDir>/tests/custom-test-env.js"
  }
  ...
}
```



------------
    
    
## Answer \#4

 Vote: 3

Created at 2022-05-04 07:58:30

------------

According to the latest jest v28 ([https://jestjs.io/docs/upgrading-to-jest28](https://jestjs.io/docs/upgrading-to-jest28)) and react 18
I had to modify a bit the script
so my preSetup.js
```
const Environment = require('jest-environment-jsdom-global');
/**
 * A custom environment to set the TextEncoder
 */
module.exports = class CustomTestEnvironment extends Environment {
    constructor({ globalConfig, projectConfig }, context) {
        super({ globalConfig, projectConfig }, context);
        if (typeof this.global.TextEncoder === 'undefined') {
            const { TextEncoder } = require('util');
            this.global.TextEncoder = TextEncoder;
        }
    }
};
```



------------
    
    
## Answer \#5

 Vote: 2

Created at 2022-06-23 17:28:33

------------

next babel works
```
● Test suite failed to run

    ReferenceError: TextEncoder is not defined

      1 | import assert from 'assert'
    > 2 | import { fromUrl, parseDomain, ParseResultType } from 'parse-domain'
        | ^
      3 | import { toUnicode } from 'punycode'
```

```
// ...
setupFiles: [`<rootDir>/jest-shim.js`],
testEnvironment: 'jest-environment-jsdom',
// ...
```

```
import { TextDecoder, TextEncoder } from 'util'

global.TextEncoder = TextEncoder
global.TextDecoder = TextDecoder
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-05-05 03:22:49

------------

Doesnt work using below config
```
"testEnvironment": "<rootDir>/tests/custom-test-env.js"

const NodeEnvironment = require('jest-environment-node');

// A custom environment to set the TextEncoder that is required by mongodb.
module.exports = class CustomTestEnvironment extends NodeEnvironment {
  async setup() {
    await super.setup();
    if (typeof this.global.TextEncoder === 'undefined') {
      const { TextEncoder } = require('util');
      this.global.TextEncoder = TextEncoder;
    }
  }
}
```


using "jest": "^28.0.3",


------------
    
    
## Answer \#7

 Vote: 0

Created at 2022-10-09 09:53:59

------------

Although all other answers seems to work fine, but I couldn't use any of them in my `react-scripts` app.
to fix the problem, I used [this approach](https://stackoverflow.com/a/69588364/2396376) and it solved my issue.
This approach doesn't require adding a test environment as `jest` config.


------------
    
    
## Answer \#8

 Vote: 0

Created at 2023-02-02 11:58:40

------------

Add this to `src/setupTests.ts`
```
import '@testing-library/jest-dom';
import { TextEncoder } from 'util';

global.TextEncoder = TextEncoder;
```



------------
    
    