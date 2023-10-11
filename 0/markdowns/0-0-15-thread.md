
# Post \#71889648 [Link](https://stackoverflow.com/questions/71889648/)

## TypeError: _API.default is not a constructor with Jest tests

**Vote**: 1 (672/702) **Views**: 7655 (472/702) 

**Internal ID** \#0-0-15

Created at 2022-04-15 22:53:32

Tags: `javascript` `reactjs` `react-native` `jestjs` `expo`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have an API class that I am trying to use in a React app.
```
// API file

class API {
...
}

export default API;

// Other file
import API from "utils/API";

const api = new API();
```

And I am getting the error:
```
TypeError: _API.default is not a constructor
```

But.. it seems like my default is set?
My Jest setup is like this:
```
"jest": {
    "setupFiles": [
      "./jestSetupFile.js"
    ],
    "testEnvironment": "jsdom",
    "preset": "jest-expo",
    "transformIgnorePatterns": [
      "node_modules/(?!((jest-)?react-native|@react-native(-community)?)|expo(nent)?|@expo(nent)?/.*|@expo-google-fonts/.*|react-navigation|@react-navigation/.*|@unimodules/.*|unimodules|sentry-expo|native-base|react-native-svg|react-router-native/.*|@invertase/react-native-apple-authentication/.*)"
    ]
  },
```

My  guess is that this is due to a configuration of my babel, webpack or package.json.
What could be causing this?
Note, I want to be clear, this doesn't happen whatsoever in my main application,  in Jest testing

---


If I change it to a named export/import, I get this:
```
TypeError: _API.API is not a constructor
```

 confusing behavior.


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2022-04-20 19:35:17

------------

This was ultimately due to additional code inside the file that I was exporting the class from.
```
import { store } from "root/App";

if (typeof store !== "undefined") {
  let storeState = store.getState();
  let profile = storeState.profile;
}
```

At the top, outside my class for some functionality I had been working on.
This caused the class default export to fail, but  in Jest, not in my actual application.


------------
    
    
## Answer \#1

 Vote: 9

Created at 2022-04-21 22:07:27

------------

As mentioned by others, it would be helpful to see a minimum reproducible example.
However, there is one other possible cause. Are you mocking the API class in your test file at all? This problem can sometimes happen if a class is mistakenly mocked as an "object" as opposed to a function. An object cannot be instantiated with a "new" operator.
For example, say we have a class file `utils/API` like so:
```
class API {
  someMethod() {
    // Does stuff
  }
}

export default API;
```

The following is an "incorrect" way to mock this class and will throw a `TypeError... is not a constructor` error if the class is instantiated after the mock has been created.
```
import API from 'utils/API';

jest.mock('utils/API', () => {
  // Returns an object
  return {
    someMethod: () => {}
  };
})

// This will throw the error
const api = new API();
```

The following will mock the class as a function and will accept the `new` operator and will not throw the error.
```
import API from 'utils/API';

jest.mock('utils/API', () => {
  // Returns a function
  return jest.fn().mockImplementation(() => ({
    someMethod: () => {}
  }));
})

// This will not throw an error anymore
const api = new API();
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2022-04-20 14:40:23

------------

Trying adding `"esModuleInterop": true,` in your `tsconfig.json`. BY default `esModuleInterop` is set to false or is not set. B setting esModuleInterop to true changes the behavior of the compiler and fixes some ES6 syntax errors.
Refer the documentation [here](https://www.typescriptlang.org/tsconfig#esModuleInterop).


------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-04-18 10:16:08

------------

You'll need to export it like this :
```
export default class API
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-04-18 11:01:16

------------

You could try with:

```
export default class API {
...
}
```


```
import API from "utils/API";

const api = new API();
```



------------
    
    
## Answer \#5

 Vote: 1

Created at 2022-10-12 10:50:08

------------

I'm adding this because the issue I had presented the same but has a slightly different setup.
I'm not exporting the class with default, i.e.
MyClass.ts
```
// with default
export default MyClass {
   public myMethod()
   {
      return 'result';
   }
}

// without default, which i'm doing in some instances.
export MyClass {
   public myMethod()
   {
      return 'result';
   }
}
```

When you don't have the default, the import syntax changes.
In a (jest) test if you follow the convention where you do have `export default MyClass(){};`
then the following works.
```
const MOCKED_METHOD_RESULT = 'test-result'
jest.mock("MyClass.ts", () => {
    // will work and let you check for constructor calls:
    return jest.fn().mockImplementation(function () {
        return {
            myMethod: () => {
                return MOCKED_METHOD_RESULT;
            },
   
        };
    });
});
```

However, if you don't have the `default` and or are trying to mock other classes etc. then you need to do the following.
Note, that the `{get MyClass(){}}` is the critical part, i believe you can swap out the `jest.fn().mockImplementation()` in favour of `jest.fn(()=>{})`
```
jest.mock("MyClass.ts", () => ({
    get MyClass() {
        return jest.fn().mockImplementation(function () {
            return {
               myMethod: () => {
                   return MOCKED_METHOD_RESULT;
               },
   
           };
        });
    },
}));
```

So the issue is the way in which you access the contents of the class your mocking. And the `get` part allows you to properly define class exports.


------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-11-08 11:15:57

------------

I resolved this error by using below code.
```
jest.mock('YOUR_API_PATH', () => ({
 __esModule: true,
 default: // REPLICATE YOUR API CONSTRUCTOR BEHAVIOUR HERE BY ADDING CLASS
})
```

If you want to mock complete API class, please check the below snippet.
```
jest.mock('YOUR_API_PATH', () => ({
  __esModule: true,
  default: class {
    constructor(args) {
     this.var1 = args.var1
    }
    someMethod: jest.fn(() => Promise.resolve())
  },
}));
```



------------
    
    