
# Post \#62777834 [Link](https://stackoverflow.com/questions/62777834/)

## How fix __dirname not defined when using electron events with Vue?

**Vote**: 16 (306/702) **Views**: 13513 (378/702) 

**Internal ID** \#0-0-69

Created at 2020-07-07 14:42:18

Tags: `vue.js` `electron` `vuex`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I used [nklayman/vue-cli-plugin-electron-builder](https://github.com/nklayman/vue-cli-plugin-electron-builder) to create an electron app prepared with Vue/Vuex. It ships with files `main.js`, `background.js` including Vue component starting point. But I can't get the events to work. My attempt below yields `Uncaught ReferenceError: __dirname is not defined` when rendering (compile is fine).
Component: Splash.vue
```
<template>
    <div @click="open">open</div>      
</template>

<script>

const { ipcMain } = require('electron')

export default {
    methods: {
        open()
        {
            ipcMain.on('my-open-event', (event, arg) => {
                console.log(event, arg)
            })
        }
    }
}
</script>
```

background.js
```
import { app, protocol, BrowserWindow } from 'electron'

...

app.on('my-open-event', async () => {
    try {
        "Will call some executable here";
    } catch (e) {
        console.error(e)
    }
})
```

main.js
```
import Vue from 'vue'
import App from './App.vue'
import store from './store'

Vue.config.productionTip = false

new Vue({
  store,
  render: h => h(App)
}).$mount('#app')
```

Full error:
```
Uncaught ReferenceError: __dirname is not defined
    at eval (webpack-internal:///./node_modules/electron/index.js:4)
    at Object../node_modules/electron/index.js (chunk-vendors.js:1035)
    at __webpack_require__ (app.js:849)
    at fn (app.js:151)
    at eval (webpack-internal:///./node_modules/cache-loader/dist/cjs.js?!./node_modules/babel-loader/lib/index.js!./node_modules/cache-loader/dist/cjs.js?!./node_modules/vue-loader/lib/index.js?!./src/components/Splash.vue?vue&type=script&lang=js&:6)
    at Module../node_modules/cache-loader/dist/cjs.js?!./node_modules/babel-loader/lib/index.js!./node_modules/cache-loader/dist/cjs.js?!./node_modules/vue-loader/lib/index.js?!./src/components/Splash.vue?vue&type=script&lang=js& (app.js:986)
    at __webpack_require__ (app.js:849)
    at fn (app.js:151)
    at eval (webpack-internal:///./src/components/Splash.vue?vue&type=script&lang=js&:2)
    at Module../src/components/Splash.vue?vue&type=script&lang=js& (app.js:1271)
```

Any ideas what I'm doing wrong?


----------
        
## Answer \#0

**Accepted** Vote: 33

Created at 2020-08-02 07:51:37

------------

To solve this I created a file `vue.config.js` in project root with content
```
module.exports = {
    pluginOptions: {
        electronBuilder: {
            nodeIntegration: true
        }
    }
}
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2020-12-04 11:25:43

------------

There are two processes in electron, the main process and the renderer process. Your Vue component is the renderer process. In order to communicate between these two processes, you need inter-processes communication. So in your case, you'd define a channel perhaps in `background.js` using `ipcMain`. You'd write something like:
```
ipcMain.on("my-custom-channel", (event, args) => handleEvent(event, args));
```

Then in your Vue component, you'd use the renderer process, ipcRendere, such as:
```
import { ipcRenderer } from "electron";

export default {
    methods: {
        open() {
            ipcRenderer.send('my-custom-channel', "hello from Vue!")
        }
    }
}
```

Point is: you cannot use `app.on` for your custom events. `app.on` handles predefined electron events. Use `ipcMain.on` instead.
reference: [https://www.electronjs.org/docs/api/ipc-main](https://www.electronjs.org/docs/api/ipc-main)


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-09-02 18:37:44

------------

You can apply the solution described on this post
[How to import ipcRenderer in vue.js ? __dirname is not defined](https://stackoverflow.com/questions/63615355/how-to-import-ipcrenderer-in-vue-js-dirname-is-not-defined)
In this way you can call window.ipcRenderer.send(channel, args...) from vue files.
Just make sure you configure preload.js on vue.config.js:
```
// vue.config.js - project root
module.exports = {
  pluginOptions: {
    electronBuilder: {
       preload: 'src/preload.js'  //make sure you have this line added
    }
  }
}
```

Another solution can be found here [https://medium.com/swlh/how-to-safely-set-up-an-electron-app-with-vue-and-webpack-556fb491b83](https://medium.com/swlh/how-to-safely-set-up-an-electron-app-with-vue-and-webpack-556fb491b83) and it use __static to refer to preload file instead of configure it on vue.config.js. To make it work you can disable preload es-lint warning inside of BrowserWindow constructor:
```
// eslint-disable-next-line no-undef
preload: path.resolve(__static, 'preload.js')
```

And make sure you added preload.js file on /public folder


------------
    
    