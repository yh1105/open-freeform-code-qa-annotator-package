
# Post \#61308006 [Link](https://stackoverflow.com/questions/61308006/)

## How to correctly import custom types in typescript

**Vote**: 22 (248/702) **Views**: 19744 (306/702) 

**Internal ID** \#0-0-71

Created at 2020-04-19 16:44:00

Tags: `reactjs` `typescript` `types`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have custom type in `src/@types/app.d.ts` ( `export type AuthKeyT = string` )

I would like to use it in `tsx` react file (using `import { AuthKeyT } from '@types/app'`), but I am getting an error:

> Cannot import type declaration files. Consider importing 'app' instead of '@types/app' 

So, when I use `import { AuthKeyT } from 'app'` I am getting:

> Cannot find module 'app'.

So.. what is wrong? How should I import custom type?

I am using typescript `3.7.2` in react app.


----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2020-04-19 16:53:51

------------

As the error says, you can't import a `.d.ts` file in TypeScript.

`import ... from 'app'` mean implicitly `import ... from 'app.ts'` or this file don't exist so you get the second error.

If you want to use your types using this import, you have to write them in a simple `.ts` file like that:



```
export type AuthKeyT = string
```


With this structure you will be able to import this type using:

```
import { AuthKeyT } from 'app'
```



------------
    
    
## Answer \#1

 Vote: 35

Created at 2021-06-08 18:39:25

------------

You're facing this error because `@types/...` is a global import. For example `import { AuthKeyT } from '@types/app'` means your're trying to import from @types that is in node_modules.
You can fix this by changing `@types` to something like `@customTypes` or by changing the path for your types folder something like `@src/types...` while importing.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-05-22 12:34:26

------------

You can also add `path` to your compilterOptons in the `tsconfig.json` like this:
```
"compilerOptions": {
    "paths": {
      "types/*": [
        "./@types/*"
      ]
    }
}
```

and then `import { AuthKeyT } from 'types/app'`


------------
    
    