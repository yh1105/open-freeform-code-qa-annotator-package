
# Post \#64220737 [Link](https://stackoverflow.com/questions/64220737/)

## vue 3 emit warning " Extraneous non-emits event listeners"

**Vote**: 76 (82/702) **Views**: 55924 (150/702) 

**Internal ID** \#0-0-70

Created at 2020-10-06 06:54:04

Tags: `javascript` `vue.js` `eventemitter` `vuejs3` `vue-composition-api`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am trying to emit data from child to parent using the composition API
I get the following warning.
> [Vue warn]: Extraneous non-emits event listeners (updatedcount) were passed to component but could not be automatically inherited because component renders fragment or text root nodes. If the listener is intended to be a component custom event listener only, declare it using the "emits" option.at <HelloWorld onUpdatedcount=fn > at 
childcomponent.vue
```
<template>
  <h1>{{ store.count }}</h1>
  <button @click="fired">click me</button>
</template>

<script>
import useStore from "../store/store.js";
export default {
  name: "HelloWorld",
  setup(_,{ emit }) {
    const store = useStore();

    const fired = () => {
      store.count++;
      emit("updatedcount", store.count);
    };

    return {
      store,
      fired
    };
  },
};
</script>
```

parentcomponent.vue
```
<template>
  <div>
    {{ hello }}
    <br />
    <br />
    <input type="text" v-model="hello.searchQuery" />
    <br><br>
    <button @click="hello.count--">click me too!</button>
    <hello-world @updatedcount="mydata" />
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";
import useStore from "./store/store.js";

export default {
  components: {
    HelloWorld,
  },
  setup() {
    const hello = useStore();

    function mydata(event) {
      console.log(event);
    }

    return {
      hello,
      mydata
    };
  },
};
</script>
```



----------
        
## Answer \#0

**Accepted** Vote: 113

Created at 2020-10-06 07:13:15

------------

I think you need to define the `emits` in your component: [https://v3.vuejs.org/guide/component-custom-events.html#defining-custom-events](https://v3.vuejs.org/guide/component-custom-events.html#defining-custom-events)
```
export default {
  name: "HelloWorld",
  emits: ["updatedcount"], // <--- add this line
  setup(_,{ emit }) {
    ...
  },
};
```



------------
    
    
## Answer \#1

 Vote: 14

Created at 2022-02-24 18:59:59

------------

update:
in vue 3 script setup you would do
```
const emits = defineEmits(["updatedcount"])

emits("updatedcount", store.count);
```



------------
    
    
## Answer \#2

 Vote: 7

Created at 2022-11-17 16:06:30

------------

When seeing this error in my own vue 3 apps I've found that wrapping a component's template content in an empty div fixes the issue for me which I believe relates to the "could not be automatically inherited" portion of the error message.
It seems the way vue works is that vue will attempt to use attribute inheritance for common events like @click and @input to pass to underlying elements but when there are multiple sibling elements at the root of a component it doesn't know which one to choose.
Do be warned that these events could change some behavior since the new wrapping parent div will now have events tied directly to it.
```
<template>
  <div>
    <h1>{{ store.count }}</h1>
    <button @click="fired">click me</button>
  </div>
</template>
```



------------
    
    
## Answer \#3

 Vote: -1

Created at 2022-09-15 11:19:32

------------

In vue3 you have to define emits, your child component would look like this :
childcomponent.vue:
```
<template>
      <h1>{{ store.count }}</h1>
      <button @click="fired">click me</button>
    </template>
    
    <script>
    import useStore from "../store/store.js";
    export default {
      name: "HelloWorld",
      emits :{
          updatedcount: null, <--- add this lines
        },
      data: () => ({
            store: useStore(),
      }),
      methods:
        fire () {
          store.count++;
          this.$emit("updatedcount", store.count);
        },
      
    };
    </script>
```



------------
    
    