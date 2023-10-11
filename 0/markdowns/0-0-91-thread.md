
# Post \#58078702 [Link](https://stackoverflow.com/questions/58078702/)

## error An unexpected error occurred: "https://registry.yarnpkg.com/react-native-template-react-native-template-typescript: Not found"

**Vote**: 15 (318/702) **Views**: 33064 (218/702) 

**Internal ID** \#0-0-91

Created at 2019-09-24 10:52:40

Tags: `typescript` `react-native`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

After uninstalling the `react-native-cli`
i run this command to initiate a RN project with typescript template:

```
npx react-native init MyApp --template react-native-template-typescript
```


but i got this error:

> warning jest > jest-cli > jest-config > jest-environment-jsdom > jsdom> left-pad@1.3.0: use String.prototype.padStart()              error An unexpected error occurred:
    "[https://registry.yarnpkg.com/react-native-template-react-native-template-typescript](https://registry.yarnpkg.com/react-native-template-react-native-template-typescript):
    Not found".info  If you think this is a bug, please open .....
    [1/2] Removing module
    react-native-template-react-native-template-typescript...
    error This module isn't specified in a package.json file.
    info Visit [https://yarnpkg.com/en/docs/cli/remove](https://yarnpkg.com/en/docs/cli/remove) for documentation
    about this command.                                              warn
    Failed to clean up template temp files in
    node_modules/react-native-template-react-native-template-typescript.
    This is not a critical error, you can work on your app.
    (node:8548) UnhandledPromiseRejectionWarning: Error: Command failed:
    yarn add react-native-template-react-native-template-typescript


----------
        
## Answer \#0

**Accepted** Vote: 8

Created at 2019-09-24 11:15:47

------------

Resolved: i had to remove react native cli using yarn too

> yarn remove global react-native-cli


------------
    
    
## Answer \#1

 Vote: 20

Created at 2020-04-10 12:19:16

------------

Remove the legacy react-native-cli

`npm uninstall -g react-native-cli`

Install new one from "react-native-community":

`npm i -g @react-native-community/cli`

after that, init the Project with: 

`npx react-native init MyApp --template react-native-template-typescript`


------------
    
    
## Answer \#2

 Vote: 5

Created at 2020-12-01 15:05:56

------------

This will also help, `--ignore-existing`
```
npx --ignore-existing react-native init MyApp --template react-native-template-typescript
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2020-06-28 13:35:07

------------


## Best solution I found


Remove
`npm uninstall -g react-native-cli`
Create the folder where the project will be
`mkdir [folder name] && cd [folder name]`
Install the dependencies and everything will work
```
yarn init
yarn add react-native
yarn react-native init [Project Name] ...
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-10-06 17:13:40

------------

According to my experience with Centos 7, it must be executed as root
```
npx react-native init AwesomeTSProject --template react-native-template-typescript
```

in the location where the project needs to be created and then `chown user -R folder`, and in this way accepts the `yarn add react-native- template-typescript` without problems.


------------
    
    