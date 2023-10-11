
# Post \#52759220 [Link](https://stackoverflow.com/questions/52759220/)

## Importing images in TypeScript React - "Cannot find module"

**Vote**: 182 (24/702) **Views**: 172625 (44/702) 

**Internal ID** \#2-9-471

Created at 2018-10-11 11:42:33

Tags: `reactjs` `typescript` `visual-studio-code` `bundler` `parceljs`

----------

#### Metadata:

Area: `Back-end`

Language: `ruby` (full parsed tag list: `javascript` `ruby`)

----------

**Notepad**


----------

I am trying to import images to use inside a React component with TypeScript. The bundler I'm using is Parcel (not Webpack).

I have created a `.d.ts` file inside the project with the image file extension, and included it inside `tsconfig.json`. However, when I try to import an image, TS yells at me about `Cannot find module`.

My project structure:

```
+ src
  + assets
    - image.jpg
  + components
    - Box.tsx
    - App.tsx
  - index.d.ts
  - index.html
  - index.tsx
- tsconfig.json
- tslint.json
```


I tried to import the image in `App.tsx` like this. VS Code underlined  `'../assets/image.jpg'` and said `Cannot find module '../assets/image.jpg'`.

```
import * as React from 'react';
import * as img from '../assets/image.jpg';

const Box = props => {
  // do things...
}

export default Box;
```


The discussions I found online point to the need of defining a `.d.ts` file myself, so I created that `index.d.ts` file with this line.

```
declare module '*.jpg';
```


Then added `"include": ["./src/index.d.ts"]` inside `tsconfig.json`, after `"compilerOptions" : {...}`.

What did I miss? How can I fix the error TS is throwing?


----------
        
## Answer \#0

**Accepted** Vote: 129

Created at 2018-10-11 12:58:29

------------

If you literally wrote `"include": ["./src/index.d.ts"]` in `tsconfig.json` and you don't have a `"files"` setting, that means the project defined by `tsconfig.json` includes only the single file `./src/index.d.ts`.  When you open any other file in VS Code, VS Code uses a separate language service instance that doesn't use your `tsconfig.json`.  Adjust your `"include"` setting to match all the `.ts` and `.tsx` files in your project, or just delete it and rely on the default behavior of including all files under the directory containing `tsconfig.json`.


## Round 2



TypeScript is ignoring `index.d.ts` because it assumes that `index.d.ts` is generated from `index.tsx` and `index.tsx` is more likely to be up to date.  Name your `index.d.ts` file something else, e.g., `declaration.d.ts`.


------------
    
    
## Answer \#1

 Vote: 212

Created at 2019-07-24 06:27:58

------------

create `index.d.ts` file in folder `src`,and add this line
```
declare module '*.jpg';
```



------------
    
    
## Answer \#2

 Vote: 104

Created at 2020-09-14 13:45:26

------------

Create module/type
```
# src/types/images.d.ts
declare module '*.jpg';
declare module '*.jpeg';
```

Change tsconfig.json
```
{
    "compilerOptions": {
        "typeRoots" : ["node_modules/@types", "src/types"]
    }
}
```



------------
    
    
## Answer \#3

 Vote: 23

Created at 2019-06-19 21:04:51

------------

there is a work around to it
for example,

```
import logo from "../../assets/logo.png"
```


change this to

```
const logo =  require("../../assets/logo.png")
```



------------
    
    
## Answer \#4

 Vote: 19

Created at 2022-01-09 03:54:50

------------

This took me about an hour so I'm going to leave the solution that worked for me.
Matt McCutchen's solution half-worked for me, but I was still facing a VSCode error. To combat this, I moved my tsconfig.json and index.d.ts to the root directory, NOT the src. Now you want your tsconfig.json to look like this:
```
"include": ["src", "index.d.ts"]
```

And for your index.d.ts, this:
```
declare module '*.png';
declare module '*.jpg';
```

(^ tweak to your typing needs)


------------
    
    
## Answer \#5

 Vote: 19

Created at 2022-07-17 03:40:43

------------

There can be many solutions to this problem



Use `require` to include image
```
<img src={require('../../assets/logo.png')} alt="" />
```


---



Instead of importing images as
`import logo from "../../assets/logo.png"`
Import as
`const logo = require("../../assets/logo.png");`
then use it like
```
<img src={logo} alt="" />
```


---




There is a `react-app-env.d.ts` file in your `src` folder.
If you don't see it, create a new file in the `src` folder named `react-app-env.d.ts`.
Now in your `react-app-env.d.ts`, add this code.
```
declare module "*.png";
declare module "*.svg";
declare module "*.jpeg";
declare module "*.jpg";
```


---




Create `declaration.d.ts` in your `src` folder. 
Then add following text in it.
```
declare module "*.png";
declare module "*.svg";
declare module "*.jpeg";
declare module "*.jpg";
```

Now you have to make your `tsconfig.json` look like this:
```
{
  "compilerOptions": {},
  "include": [
    "src",
    "src/declaration.d.ts",
  ]
}
```

[](https://i.stack.imgur.com/B8UxU.png)

---



Create a `types` folder in your `src` folder and add `images.d.ts` in this folder.
Add below text in it.
```
declare module "*.png";
declare module "*.svg";
declare module "*.jpeg";
declare module "*.jpg";
```

Now configure `tsconfig.json` like this:
```
{
  "compilerOptions": {
    "typeRoots": [
      "node_modules/@types",
      "src/types"
    ],
}
```

[](https://i.stack.imgur.com/1Lcyz.png)

---




------------
    
    
## Answer \#6

 Vote: 9

Created at 2022-10-29 13:09:16

------------

For `"react": "^18.2.0"` with Typescript do the following:

1. Create a file called: declaration.d.ts in your src/ dir.
2. Add this lines of code:


```
declare module "*.jpg"
declare module "*.png"
```

That's it the error should be turned off.
Good lock!


------------
    
    
## Answer \#7

 Vote: 7

Created at 2021-12-31 18:37:00

------------

There should be a file called "react-app-env.d.ts" that is created when you use create-react-app. This file classes all types of image files and many more as modules that can be imported. inside the file there should be the following:
```
/// <reference types="react-scripts" />
```



------------
    
    
## Answer \#8

 Vote: 4

Created at 2022-03-28 16:21:34

------------

I declared multiple image types, and had to put them in  to make VS Code happy:
src/types/image.jpg.d.ts:
```
declare module '*.jpg' {
  import { ImageSourcePropType } from 'react-native'

  const content: ImageSourcePropType

  export default content
}
```

src/types/image.png.d.ts:
```
declare module '*.png' {
  import { ImageSourcePropType } from 'react-native'

  const content: ImageSourcePropType

  export default content
}
```

src/types/image.svg.d.ts:
```
declare module '*.svg' {
  import React from 'react'
  import { SvgProps } from 'react-native-svg'

  const content: React.FC<SvgProps>

  export default content
}
```

No tweaks to tsconfig, etc required.


------------
    
    
## Answer \#9

 Vote: 3

Created at 2022-03-28 08:09:20

------------

Create a global.d.ts file inside your src directory or any rootDir
```
declare module "*.png" {
 export default "" as string
}
```

This will make use of an asset, in this case png image and also clear a vscode error


------------
    
    
## Answer \#10

 Vote: 2

Created at 2020-04-29 02:19:21

------------

I've pasted:
```
/// <reference types="react-scripts" />
```

from an older project in my: `react-app-env.d.ts`  and it worked.
In the latest versions of `CRA` (when `TS` is the chosen language), this file is automatically added to the src folder and contains the single line shown above. It is a directive to reference types from `react-scripts`.


------------
    
    
## Answer \#11

 Vote: 1

Created at 2022-01-16 12:10:22

------------

This is worked for me
```
declare module "*.png" {
const src: string
export default src
}
```



------------
    
    
## Answer \#12

 Vote: 1

Created at 2022-08-31 16:44:27

------------

I test all of the replies and found the answer.
First, create a file with the name `.d.ts` in your route folder.
Then copy these lines into that.
```
declare module "*.png" {
  export default "" as string;
}
declare module "*.svg" {
  export default "" as string;
}
declare module "*.jpeg" {
  export default "" as string;
}
declare module "*.jpg" {
  export default "" as string;
}
```

Finally, go to tsconfig.json file in your route folder. and define new child.
```
{
   "include": ["src", ".d.ts"]
}
```

your're go to go.


------------
    
    
## Answer \#13

 Vote: 0

Created at 2022-01-01 07:54:35

------------

This error happens when you not included   file in your src folder
becoz you need two line of code for get rid of this error  ==>
`<reference types="react-scripts" />`


------------
    
    
## Answer \#14

 Vote: 0

Created at 2023-01-31 11:16:52

------------

I fixed adding file  in root:
Paste the following code inside of it: `<reference types="react-scripts"`
-
inside , add: `"include": ["src", "index.d.ts"]`


------------
    
    