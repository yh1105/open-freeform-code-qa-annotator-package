
# Post \#61228114 [Link](https://stackoverflow.com/questions/61228114/)

## Absolute Imports: React and Typescript

**Vote**: 6 (473/702) **Views**: 16085 (341/702) 

**Internal ID** \#0-0-24

Created at 2020-04-15 11:57:00

Tags: `reactjs` `typescript` `webpack` `babeljs`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------


## Background



I have a React app bootstrapped using `create-react-app` and typescript. As the application has grown, () I would like to implement absolute imports. I am using VS Code (Visual Studio Code) and with very little configuration, I got TS and VS Code to recognize my absolute imports.

For TS, I took the following steps in my `tsconfig.json`:

- `"baseUrl": "client"`- `include``"include": ["./**/*.ts", "./**/*.tsx", "client"]`

For VS Code, I changed my User Settings: Typescript -> Preferences: Import Module Specifier -> non-relative

That worked great. All of my imports were using absolute imports, no errors. But, when I ran the app, I got an error: `Error: Cannot find module "component"` I expected to see my app like I did before the absolute imports. 


## What I Tried



Figured, the error was a webpack or babel issue. 


1. Created env File Added the following to an env file in the root of the app (same location as my package.json)



```
NODE_PATH=client/
```


That did not work. Same error: `Error: Cannot find module "components"`. Also tried changing NODE_PATH to REACT_APP_NODE_PATH that did not work either.


1. Modify Babel Config Added babel plugin module resolver with yarn add -D babel-plugin-module-resolver. Then modified my babel.config.js to:



```
module.exports = { 
  env {...}, 
  plugins: [
    [
      'module-resolver',
      {
        cwd: 'babelrc',
        extensions: ['.ts', '.tsx', '.js'],
        alias: {
          client: './client',
        },
      },
    ],
  ]
}
```


That returns the same error. (I am restarting the server after every change to my config files)


I used a lot of different articles to try to find clarity. Here are some:

- [here](https://medium.com/@ktruong008/absolute-imports-with-create-react-app-4338fbca7e3d)- [here](https://dev.to/mr_frontend/absolute-imports-in-create-react-app-3ge8)- [here](https://justinnoel.dev/2019/06/18/configuring-react-absolute-imports-for-typescript/)- [StackOverflow](https://stackoverflow.com/questions/56908357/use-absolute-path-in-react-components)- 

And many others. None of that worked. 


## Project Structure



My project structure is a little "unconventional" or not my typical pattern which could be causing an issue.

```
└── root dir
    ├── assets
    │   └── client
    │       ├── assets
    │       ├── components
    │       ├── hooks
    │       └── ...
    │   └── babel.config.js
    │   └── .babelrc
    │   └── webpack.config.js
    │   └── package.json
    └── server files (no server dir)
```


So `client` is like my `src` in a typical react app. `assets` is the "entry dir" for my server which is in the `root dir`.

Any help would be appreciated.


----------
        
## Answer \#0

**Accepted** Vote: 24

Created at 2020-06-23 13:33:19

------------

Found the answer. There is no need for the `env` file. Only need to modify the `tsconfig.json` and the `webpack.config.js` files.

Added two keys: `baseUrl` and `paths`. Set the `baseUrl` to your `src` or `client` directory. The `paths` key will contain an object with whatever you want to reference in your absolute path.
```
{
  "compilerOptions": {
    ...
    "baseUrl": "./client",
    "paths": {
      "client/*": ["./client/*"]
    },
    ...
  },
  "include": ["./**/*.ts", "./**/*.tsx"]
}
```


Added an alias for "src" (in my case, "client").
```
module.exports = (env, options) => ({
  ...
  resolve: {
    alias: {
      client: path.resolve(__dirname, 'client/') // added this
    },
    extensions: ['.ts', '.tsx', '.js', '.jsx', '.json']
  }
})
```


To import something now, I can replace the relative import with "client".
This:
```
import TableHeader from '../../../components/Table/TableHeader'
```

becomes this:
```
import TableHeader from 'client/components/Table/TableHeader'
```

If there is a better way to do this, post your solution. 

## Update: Day 2022


To do this with newer versions of `tsc` or `create-react-app`
```
npx tsc --init
```

or
```
npx create-react-app my-new-app --template typescript
```

you only need to modify the `baseUrl` property in your `tsconfig.json`. If you want the base directory to be your root directory you would set `baseUrl` to `.` or `src` if you want it to be your source directory.

File tree
```
.
└── my-new-app/
    ├── node_modules
    ├── src/
    │   ├── hello/
    │   │   └── world/
    │   │       └── foo/
    │   │           ├── bar.tsx
    │   │           └── sibling.tsx
    │   ├── components
    │   ├── layouts
    │   └── utils
    ├── index.ts
    ├── tsconfig.json
    ├── .prettierrc.json
    ├── package.json
    ├── yarn.lock
    └── .gitignore
```

```
// baseUrl: "." root
import Bar from "src/hello/world/foo/bar";

// baseUrl: "src" dir
import Bar from "hello/world/foo/bar";
```

You can still specify paths if you would like however I find the `baseUrl` to be enough.

### VS Code


If you are using VS Code and you do this and it does not work you may need to tinker with your `typescript.preferences.importModuleSpecifier` setting.


1. Go to your VS Code settings (not the JSON file the GUI)
2. Search for "typescript.preferences.importModuleSpecifier"
3. Verify that you have "shortest" or "non-relative" selected


Shortest means it will use a "non-relative" import only if that is shorter than using a relative import.

```
import Bar from "src/hello/world/foo/bar";
import Bar from "./foo/bar"; // <- this is shorter so it will use this even though it's relative
```

If that isn't working you may need to . You can do that by

1. Opening a TS file
2. Entering your command palette
3. Searching "TypeScript: Restart TS server"


Hopefully that helps.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-10-31 04:44:00

------------

My solution was to use
[babel-plugin-module-resolver](https://github.com/tleunen/babel-plugin-module-resolver). Why? because it allowed me do this.
```
import Comp1 from 'components/comp1';
import AnotherAppComp from '@app/anotherComp';
```

It's configuration is more flexible, allowing alias, or direct access.
In , add this plugin:
```
plugins: [
[
  require.resolve('babel-plugin-module-resolver'),
  {
    cwd: 'babelrc',
    extensions: [
      '.js',
      '.jsx',
      '.ts',
      '.tsx',
      '.android.js',
      '.android.tsx',
      '.ios.js',
      '.ios.tsx',
    ],
    root: ['.'],
    alias: {
      '@app': './another-app',  // path to your app folder
    },
  },
],
...
]
```

Then in , change  to:
```
"compilerOptions": {
...
    "baseUrl": ".",                           /* Base directory to resolve non-absolute module names. */
    "paths": {
      "@app/*": ["./another-app/*"],
      "*": ["./src/*"],
    },
...
  }
```

I had to restart my vscode in order for it to work, the first time. After that when I edit I don't have to restart anymore.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-11-04 05:25:32

------------


Here's how it works.modify your  file to look like this:
```
{
      "compilerOptions": {
        ...
        "baseUrl": "src"
      },
      "include": ["src"]
    }
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-09-04 18:53:50

------------

Here I wrote an article for [How to Use Absolute Imports in React](https://javascript.plainenglish.io/why-and-how-to-use-absolute-imports-in-react-d5b52f24d53c). For your case it is specially for React Typescript. Therefore you need to add//update your `tsconfig.json` file in the root directory of the project. Then you need to update the compiler option baseUrl in the file.
```
{
  "compilerOptions": {
    "baseUrl": "src"
  },
  "include": ["src"]
}
```

VS Code is smart enough to understand the `tsconfig.json`.

Select the src folder in the project window and right-click on it. Select the option Mark Directory as and then select the Resources Root option. Then go to Settings -> Editor -> Code Style -> JavaScript and select the Imports tab. Then check the Use paths relative to the project, resource or sources roots.
By Convention
Absolute imports have been possible for a long time with Webpack. When you are naming your aliased folder, you need to use PascalCase/CamelCase because it is the convention follow in the [Webpack](https://webpack.js.org/configuration/resolve/#resolvealias).


------------
    
    