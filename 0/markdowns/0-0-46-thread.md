
# Post \#64933543 [Link](https://stackoverflow.com/questions/64933543/)

## Parsing error: Cannot read file '.../tsconfig.json'.eslint

**Vote**: 228 (15/702) **Views**: 151624 (50/702) 

**Internal ID** \#0-0-46

Created at 2020-11-20 16:51:35

Tags: `node.js` `typescript` `eslint` `typescript-eslint`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

The error `Parsing error: Cannot read file '.../tsconfig.json'.eslint` shows in all `.ts` files in the `src` folder including `index.ts`.
I have no idea how to set up configs. The issue just shows a red line and makes the file red. However, everything compiles and run fine. The entire Node project was created using the firebase CLI.
`tsconfig.json` file:
```
{
  "compilerOptions": {
    "module": "commonjs",
    "noImplicitReturns": true,
    "noUnusedLocals": true,
    "outDir": "lib",
    "sourceMap": true,
    "strict": true,
    "target": "es2017"
  },
  "compileOnSave": true,
  "include": [
    "src"
  ]
}
```

`.eslintrc.js` file:
```
module.exports = {
  env: {
    browser: true,
    es6: true,
    node: true,
  },
  extends: [
    "plugin:import/errors",
    "plugin:import/warnings",
    "plugin:import/typescript",
  ],
  parser: "@typescript-eslint/parser",
  parserOptions: {
    project: "tsconfig.json",
    sourceType: "module",
  },
  plugins: [
    "@typescript-eslint",
    "import",
  ],
  rules: {
    "@typescript-eslint/adjacent-overload-signatures": "error",
    "@typescript-eslint/no-empty-function": "error",
    "@typescript-eslint/no-empty-interface": "warn",
    "@typescript-eslint/no-floating-promises": "error",
    "@typescript-eslint/no-namespace": "error",
    "@typescript-eslint/no-unnecessary-type-assertion": "error",
    "@typescript-eslint/prefer-for-of": "warn",
    "@typescript-eslint/triple-slash-reference": "error",
    "@typescript-eslint/unified-signatures": "warn",
    "comma-dangle": "warn",
    "constructor-super": "error",
    eqeqeq: ["warn", "always"],
    "import/no-deprecated": "warn",
    "import/no-extraneous-dependencies": "error",
    "import/no-unassigned-import": "warn",
    "no-cond-assign": "error",
    "no-duplicate-case": "error",
    "no-duplicate-imports": "error",
    "no-empty": [
      "error",
      {
        allowEmptyCatch: true,
      },
    ],
    "no-invalid-this": "error",
    "no-new-wrappers": "error",
    "no-param-reassign": "error",
    "no-redeclare": "error",
    "no-sequences": "error",
    "no-shadow": [
      "error",
      {
        hoist: "all",
      },
    ],
    "no-throw-literal": "error",
    "no-unsafe-finally": "error",
    "no-unused-labels": "error",
    "no-var": "warn",
    "no-void": "error",
    "prefer-const": "warn",
  },
  settings: {
    jsdoc: {
      tagNamePreference: {
        returns: "return",
      },
    },
  },
};
```

I had tried restarting VScode, clearing the cache, and all to no avail. I am guessing I need to change some of the paths but I am not very good at changing the config files so I don't want to accidentally break the entire project.


----------
        
## Answer \#0

**Accepted** Vote: 410

Created at 2020-11-21 07:34:04

------------

By default, the `project`s (in `parserOptions`) are resolved relative to the current working directory. If you run `eslint` in a different working directory to the folder containing `tsconfig.json`, @typescript-eslint/parser will not be able to locate the file.
To fix this, you can set [tsconfigRootDir](https://github.com/typescript-eslint/typescript-eslint/tree/50a46c60fb81d8434aa4268a13d17d8fcf499e21/packages/parser#parseroptionstsconfigrootdir) to `__dirname`, which would make the parser resolve the project configuration relative to `.eslintrc.js`:
```
module.exports = {
  // ...
  parserOptions: {
    project: "tsconfig.json",
    tsconfigRootDir: __dirname,
    sourceType: "module",
  },
  // ...
}
```


---


If you’re having some trouble with
```
/path/to/.eslintrc.js
  0:0  error  Parsing error: "parserOptions.project" has been set for @typescript-eslint/parser.
The file does not match your project config: .eslintrc.js.
The file must be included in at least one of the projects provided
```

see [this question](https://stackoverflow.com/q/64271575/8289918).


------------
    
    
## Answer \#1

 Vote: 114

Created at 2021-01-04 11:59:51

------------

A VSCode-specific approach, that worked for me was to create a `.vscode` folder in the root project directory and add the following property to the `settings.json` file inside it:
```
{
  "eslint.workingDirectories": [
    "src"
  ]
}
```

The `"src"` could be any directory, which should be in eslint's scope.


------------
    
    
## Answer \#2

 Vote: 62

Created at 2021-05-20 03:36:19

------------

Update your eslintrc.json file with the following line:
`"project": "PROJECT_NAME/tsconfig.json"`
with PROJECT_NAME being the name of your project (not a macro).


------------
    
    
## Answer \#3

 Vote: 53

Created at 2021-09-22 20:20:27

------------

For those using Angular and eslintrc.json, add the 2 stars to the project node like this:
```
"overrides": [
    {
        "files": ["*.ts"],
        "parserOptions": {
            "project": ["**/tsconfig.json"],
        },
```



------------
    
    
## Answer \#4

 Vote: 9

Created at 2021-06-10 07:45:23

------------

Another approach is if you happened to open the Workspace folder one level above the Project (as was in my case), just start a new Workspace with the ACTUAL project folder. That solved my Eslint error.


------------
    
    
## Answer \#5

 Vote: 9

Created at 2021-10-07 02:23:07

------------

I suddenly started seeing this error using vscode. Restarting vscode has resolved my issue


------------
    
    
## Answer \#6

 Vote: 9

Created at 2022-02-03 19:21:09

------------

I solved it by adding `@typescript-eslint/parser` to the extends:
[](https://i.stack.imgur.com/p15ho.png)


------------
    
    
## Answer \#7

 Vote: 3

Created at 2021-11-02 14:25:28

------------

What fixed this for me is to simply navigate into the project directory containing my tsconfig.json, and that it was able to locate the file.
Otherwise, I liked @dheeraj9499's answer that you can modify your .eslintrc.json "project" array within "parserOptions" and locate where your tsconfig.json is located from within the current directory. So something like the following was able to find tsconfig.json in the correct directory:
```
"parserOptions": {
  "project": [
    "code/eMOS/Scripts/tsconfig.json",
    "code/eMOS/Scripts/tsconfig.spec.json"
  ],
 }
```



------------
    
    
## Answer \#8

 Vote: 2

Created at 2021-07-22 07:51:45

------------


One of the major issues I faced here because of the way the project folder is opened in vs code.
assume the folder structure is
 where '' is the angular project you are using.
if you are opening the test folder in vs code then navigating to testproject as below
```
D:\test>
D:\test>cd testproject
D:\test\testproject>
```

Doing this will create 2 settings.json files, one for the main opened folder and one for the child folder which in turn doesn't allow the settings to be applied properly.
So always opening the project folder directly in the vscode would help overcoming the issue, when all the dependencies are mentioned properly in eslintrc.json and tsconfig.json files would help in solving the issue.


------------
    
    
## Answer \#9

 Vote: 1

Created at 2022-01-03 03:40:33

------------

I got this error in Visual Studio Code when working on a typescript project via WSL 2 on Windows 10.
It was fixed by installing the [Remote - WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl) extension in VS Code.
The files were hosted in my \wsl$\Ubuntu\home<user>\ location. The error message specified the correct file location, and VS Code could open the file.
The above fixes using the root directory and parser options did not help.


------------
    
    
## Answer \#10

 Vote: 1

Created at 2022-08-15 09:29:52

------------

It works for me
```
"parserOptions": {
  "project": ["<YourProjectName>/tsconfig.json"],
}
```



------------
    
    
## Answer \#11

 Vote: 0

Created at 2021-06-28 11:06:20

------------

If you get the following error message in a  (e.g. a folder containing several backend services as well as a frontend module), IntelliJ might not be able to tell what to do.
In my case I got the following error on top of a `.ts`-file:
[](https://i.stack.imgur.com/MLJUi.png)
To fix this, I have clicked on "ESLint settings..." and picked "Manual ESLint configuration"
[](https://i.stack.imgur.com/1ZVpY.png)
Then I entered the path to the  folder in "Working directories:" and picked the correct path to eslint in "ESLint package" (which - in my case - was in `<frontend-module-root>/node_modules/eslint`).
This way I still get eslint to work and do not have to change any project-related configuration at all (it works from frontend module root folder in CLI already).


------------
    
    