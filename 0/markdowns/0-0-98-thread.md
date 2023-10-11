
# Post \#59978450 [Link](https://stackoverflow.com/questions/59978450/)

## How to do app versioning in create react app?

**Vote**: 9 (409/702) **Views**: 14774 (359/702) 

**Internal ID** \#0-0-98

Created at 2020-01-30 02:51:37

Tags: `reactjs` `webpack` `version` `versioning` `build-numbers`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I need to display  of my react app in the footer in  format.

I need this version to increment every time I deploy the app by being provided a choice if I want to increment  or  or .

How do I achieve this? :)


----------
        
## Answer \#0

**Accepted** Vote: 11

Created at 2020-10-29 10:09:39

------------

To bump the version of your app you can use [npm version](https://docs.npmjs.com/cli/v6/commands/npm-version).
For example:
```
npm version minor
```



------------
    
    
## Answer \#1

 Vote: 7

Created at 2021-09-03 16:24:59

------------

Once you have a way to bump the version in package.json (e.g. `npm version`, as suggested by @bertrand-p), you can then assign the version to an environment variable.  For example, in `.env` you can set:
```
REACT_APP_VERSION=$npm_package_version
```

Then you can access the variable from within your app via `process.env.REACT_APP_VERSION`.
See also: [https://github.com/facebook/create-react-app/issues/2466#issuecomment-357490359](https://github.com/facebook/create-react-app/issues/2466#issuecomment-357490359)


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-04-10 18:19:07

------------

I don't think the answers from @Bertrand P or @VulfCompressor tell the complete picture. I used genversion [https://www.npmjs.com/package/genversion](https://www.npmjs.com/package/genversion). The steps I did were:

1. npm install genversion --save-dev
2. Modify the build script in package.json to genversion --es6 src/autobuild_version.js && react-scripts build (I couldn't figure out a way to import the generated module from the lib directory as suggested in the genversion documentation so I had to put it in the src directory instead)
3. In the React app, import { version } from './autobuild_version' and use as appropriate
4. Add src/autobuild_version.js to .gitignore (other source code control tools are available)
5. npm run build




------------
    
    
## Answer \#3

 Vote: 0

Created at 2020-01-30 06:03:05

------------

You can use [grunt-bump](https://www.npmjs.com/package/grunt-bump) to handle your app versioning. As for displaying your app version, refer to [Is there a way to get version from package.json in nodejs code?](https://stackoverflow.com/questions/9153571/is-there-a-way-to-get-version-from-package-json-in-nodejs-code). 


------------
    
    
## Answer \#4

 Vote: 0

Created at 2023-02-04 22:10:31

------------

You can use npm commands below in different conditions mentioned below and the commands will effect the version number in package.json in your react project.
You can access that version by installing dotenv by `npm i dotenv` or`yarn add dotenv` and use the .env file in your root folder and access the version number in the whole app with `REACT_APP_VERSION=$npm_package_version`. You can find more commands at [npm version documentary](https://docs.npmjs.com/cli/v6/commands/npm-version) page. commands below will add to version numbers like this: `"version":"{major}.{minor}.{patch}"`
If its a major change(bug fixing) in your app use:
```
npm version major
```

If its a minor change(bug fixing) in your app use:
```
npm version minor
```

And if it's just a patch upgrade such as changing some styles use:
```
npm version patch
```

You can use versioning for many purposes and one of them is for making sure client's cache will be renew and they would have the updated content.


------------
    
    