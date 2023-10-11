
# Post \#71083110 [Link](https://stackoverflow.com/questions/71083110/)

## Vue Uncaught ReferenceError: process is not defined

**Vote**: 38 (160/702) **Views**: 30560 (231/702) 

**Internal ID** \#0-0-121

Created at 2022-02-11 16:05:20

Tags: `typescript` `vue.js` `webpack`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm working on a `Vue + typescript` project. I want to use `process.env.var_name` to adjust the project is in development mode or production mode, just like
```
const isProduct = process.env.APP_ENV === "prod";
```

; however I got an error below. I never meet this error before, so could any one give me a clue why this would happen so I can try to figure out what happend in the project and then fix it.[](https://i.stack.imgur.com/VEk1H.png)


----------
        
## Answer \#0

**Accepted** Vote: 84

Created at 2022-03-09 08:17:26

------------

If you are using Vite, use `import.meta.env` instead, `process.env` is removed.
And make sure variables start with `VITE_` in `.env` file.
```
VITE_SOME_KEY=123
```

more
[https://vitejs.dev/guide/env-and-mode.html](https://vitejs.dev/guide/env-and-mode.html)


------------
    
    
## Answer \#1

 Vote: 5

Created at 2022-07-16 20:47:35

------------

You can use `if (import.meta.env.PROD) ...` instead.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-08-28 14:47:16

------------

```
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  server: { 
    host: '0.0.0.0'
  },
  define: {
    'process.env': {}
  }
})
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-09-07 07:42:14

------------

 

```
mounted () {
  window.process = {
    env: {
       NODE_ENV: 'development'
    }
}
```




------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-02-11 16:21:34

------------

Env's variables must start with `VUE_APP_`, so try to change `APP_ENV` into `VUE_APP_ENV`.
About the CORS error you have to enable your FE from your BE.


------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-02-13 14:56:29

------------

I have a quasar project before and this one works for me.
I installed the `dotenv` library by running the yarn command
```
yarn add --dev dotenv
```

Please let me know.


------------
    
    
## Answer \#6

 Vote: 0

Created at 2023-02-28 11:13:17

------------

I am using Vite and and after all setup setup it says process is not defined.
then i got a Solution.
you can access the key like this:-
```
//like this you can access the key in vite

const API_URl = `http://www.omdbapi.com/?apikey=${import.meta.env.VITE_APP_API_KEY}&s=titanic`;


//And Here is the way to define the key in .env file

VITE_APP_API_KEY=5588hfhf
```


const API_URl = `http://www.omdbapi.com/?apikey=${import.meta.env.VITE_APP_API_KEY}&s=titanic`;


------------
    
    
## Answer \#7

 Vote: 0

Created at 2023-03-04 03:31:12

------------

A workaround that I found was have the vite.config.js file like this
```
import { fileURLToPath, URL } from "node:url";
import { defineConfig, loadEnv } from "vite";
import vue from "@vitejs/plugin-vue";

// https://vitejs.dev/config/

export default ({ mode }) => {
  const env = loadEnv(mode, process.cwd(), "");
  return defineConfig({
    define: {
      "process.env": env,
    },
    plugins: [vue()],
    server: {
      host: true,
      port: 8080,
    },
    resolve: {
      alias: {
        "@": fileURLToPath(new URL("./src", import.meta.url)),
      },
    },
  });
};
```

Use loadEnv from vite and then define the "process.env" variable to point to env.


------------
    
    