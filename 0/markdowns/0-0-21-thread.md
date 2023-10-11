
# Post \#57713402 [Link](https://stackoverflow.com/questions/57713402/)

## How can I copy text from Vuetify's v-text-field to clipboard?

**Vote**: 11 (370/702) **Views**: 20567 (302/702) 

**Internal ID** \#0-0-21

Created at 2019-08-29 15:27:19

Tags: `javascript` `vue.js` `vuejs2` `vue-component` `vuetify.js`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am using  and trying to copy text from `v-text-field` component to clipboard when button is clicked. [Sample code available on codepen](https://codepen.io/milost1982/pen/abowjpJ?&editable=true&editors=101):

```
<template>
    <div id="app">
      <v-app id="inspire">
        <v-container>
          <v-text-field v-model="text1"></v-text-field>
          <v-btn @click="copyText">copy</v-btn>
        </v-container>
      </v-app>
    </div>
</template>

<script>
    new Vue({
      el: '#app',
      vuetify: new Vuetify(),
      data() { 
        return {
          text1: 'lorem ipsum 123'
        }
      },
      methods: {
        copyText (){
          // copy to clipboard
        }
      }
    })
</script>
```


The question is what code to put in `copyText` method of the Vue instance?


----------
        
## Answer \#0

**Accepted** Vote: 14

Created at 2019-08-29 15:54:42

------------

You could do it by assigning a value to `ref` attribute and then in the method request the `input` element, select its content using `select` function and copy that content using `document.execCommand("copy");`:

```
<template>
    <div id="app">
      <v-app id="inspire">
        <v-container>
          <v-text-field v-model="text1" ref="textToCopy"></v-text-field>
          <v-btn @click="copyText">copy</v-btn>
        </v-container>
      </v-app>
    </div>
</template>

<script>
    new Vue({
      el: '#app',
      vuetify: new Vuetify(),
      data() { 
        return {
          text1: 'lorem ipsum 123'
        }
      },
      methods: {
        copyText () {
          let textToCopy = this.$refs.textToCopy.$el.querySelector('input')
          textToCopy.select()
          document.execCommand("copy");
        }
      }
    })
</script>
```



------------
    
    
## Answer \#1

 Vote: 31

Created at 2020-04-29 14:15:37

------------

This solution worked for me. It uses the new [Clipboard API writeText method](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard/writeText) which is supported by most modern browsers (see [Can I use](https://caniuse.com/#feat=mdn-api_clipboard_writetext) for more details). Writing to the clipboard does not require special permissions.

```
methods: {
    copyText() {
      navigator.clipboard.writeText(this.text1);
    }
  }
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-09-16 18:33:12

------------

I solved with vue-clipboards.
1.- npm install vue-clipboards 

2.- add this in your main.js
```
import VueClipboards from 'vue-clipboards'
Vue.use(VueClipboards)
```

3.- Create vuetify btn and add v-clipboard like below
```
<v-btn v-clipboard="clipboardValue">add to clipboard</v-btn>
```

4.- In your data section (you can fill your var in any method or where you want).
```
data: () => ({
clipboardValue: 'something'
}),
```

5.- Run your code. its all.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-01-24 08:05:07

------------

This does not requrie any kind of third party library
Just Pass Dynamic String Using On click method
```
<template>
     <v-btn @click="copyToClipBoard(errorMsg)">
       <v-icon>mdi-content-copy</v-icon>
     </v-btn>
</template>
```

method will copy it to clipboard directly
```
methods: {
    copyToClipBoard(textToCopy) {
      navigator.clipboard.writeText(textToCopy);
    },
  }
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2019-08-29 15:42:48

------------

Assign an id to the `v-text-field` element like :

```
<v-text-field v-model="text1" id="tocopy" ></v-text-field>
```


and add the following code to your method :

```
copyText(){
    let input=document.getElementById("tocopy");
   input.select();
         document.execCommand("copy");
    }
```


check this [codepen](https://codepen.io/boussadjra/pen/yLBXqKO?editors=1011)


------------
    
    