
# Post \#59125043 [Link](https://stackoverflow.com/questions/59125043/)

## VueJS Using Prop Type Validation With NULL and 'undefined' Values?

**Vote**: 32 (187/702) **Views**: 57728 (146/702) 

**Internal ID** \#0-0-89

Created at 2019-12-01 11:25:14

Tags: `javascript` `validation` `vue.js` `types` `vuejs2`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

Even though [VueJS 2 official documentation about prop validation](https://v2.vuejs.org/v2/guide/components-props.html#Prop-Validation) is stating (as a code example's comment line):
> // Basic type check (`null` and `undefined` values will pass any type
validation)
[this code reproduction](https://vuep.run/6ecbe302)
```
[Vue warn]: Invalid prop: type check failed for prop "value". Expected String, Number, Boolean, got Null
```

```
<template>
  <div>
    <h1>{{ title }}:</h1>
    <MyInput :value="null" />
  </div>
</template>

<script>
Vue.component('MyInput', Vue.extend({
  props: {
    value: {
      type: [String, Number, Boolean],
      required: true,
    },
  },
  template: `
    <select v-model="value">
      <option value="null">
        null value
      </option>
      <option value="">
        Empty value
      </option>
    </select>`,
}));

export default {
  data: () => ({
    title: 'VueJS Using Prop Type Validation With NULL and `undefined` Values?'
  }),
};
</script>
```



----------
        
## Answer \#0

**Accepted** Vote: 51

Created at 2019-12-01 12:39:24

------------

> // Basic type check (`null` and `undefined` values will pass any type validation)

This applies only when `required: true` is NOT set. In your code,  you are saying that the prop is required and so Vuejs is showing the warning

Related discussion: [https://forum.vuejs.org/t/shouldnt-null-be-accepted-as-prop-value-with-any-type/63887](https://forum.vuejs.org/t/shouldnt-null-be-accepted-as-prop-value-with-any-type/63887)


------------
    
    
## Answer \#1

 Vote: 10

Created at 2019-12-01 12:41:15

------------

It's because of `required: true`
From [API docs](https://v2.vuejs.org/v2/api/#props) (more detail)
> required: Boolean
Defines if the prop is required. In a non-production environment, a console warning will be thrown if this value is truthy and the prop is not passed.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-03-30 15:03:51

------------

As Cato mentioned using a `validator` can help solve this issue.
You will need to provide a `default` and make sure `required` is not set or is `false`.
```
<script>
Vue.component('MyInput', Vue.extend({
    props: {
        value: {
            type: [String, Number, Boolean],
            default: null,
            validator: (p) => {
                return ['string', 'number', 'boolean'].indexOf(typeof p) !== -1 || p === null;
            },
        }
    }
});
</script>
```

This may look like it will work the same as this...
```
<script>
Vue.component('MyInput', Vue.extend({
    props: {
        value: {
            type: [String, Number, Boolean],
            required: false,
            default: null,
        }
    }
});
</script>
```

However, in the second example you can pass `undefined`. In the first you can pass the listed types plus `null`.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2023-02-07 21:44:35

------------

[PropType](https://vuejs.org/api/utility-types.html) with the 
```
<script lang="ts">

import { defineComponent, PropType } from 'vue'

export default defineComponent({
  components: {
  ...
  },
  name: 'Component',

  props: {
    value: { 
      type: null as unknown as PropType<string | null>,
      default: null, required: false
    }
  }
})
```



------------
    
    