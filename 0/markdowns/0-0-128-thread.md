
# Post \#61184749 [Link](https://stackoverflow.com/questions/61184749/)

## How to properly reset Vue Composition Api's reactive values

**Vote**: 22 (248/702) **Views**: 17952 (322/702) 

**Internal ID** \#0-0-128

Created at 2020-04-13 09:17:19

Tags: `javascript` `typescript` `vuejs3` `vue-composition-api`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm wondering how should I reset a reactive in vuejs setup? (i know if change it to the ref and using view.value will solve this problem, but there should be an answer to this for using reactive)
```
setup(props, context){
  // states
  const DataTable = reactive((null as unknown) as DataTable);
  const PolicyForm = reactive((null as unknown) as PolicyForm);
  let view = reactive(resetViewState());
  let config = reactive(
    (resetPreRegisterConfig() as unknown) as PreRegisterConfig
  );
  // methods:
  const fetchProfilelist = (
    pagination: Pagination{ page:1, size:15},
    sort_label: string = ""
  ) => {
    DataTable.fetchTablelist(api_fetchProfilelist, pagination, sort_label);
  };
  const pageRefresh = () => {
    view = resetViewState(); // 
    config = resetPreRegisterConfig();
    fetchProfilelist();

  };
  return {
    DataTable,
    PolicyForm,
    view,
    config,
    fetchProfilelist,
    pageRefresh
  }
```



----------
        
## Answer \#0

**Accepted** Vote: 47

Created at 2020-04-29 18:50:10

------------

You can use `Object.assign`:

```
setup() {
    const initialState = {
      name: "",
      lastName: "",
      email: ""
    };

    const form = reactive({ ...initialState });

    function resetForm() {
      Object.assign(form, initialState);
    }

    function setForm() {
      Object.assign(form, {
        name: "John",
        lastName: "Doe",
        email: "john@doe.com"
      });
    }

    return { form, setForm, resetForm };
  }
```


[See it live on codesandbox](https://codesandbox.io/s/set-reset-reactive-object-8rdgz?file=/src/App.vue:317-731)

credits: [taken from here](https://github.com/vuejs/vue-next/issues/1081#issuecomment-621385050)


------------
    
    
## Answer \#1

 Vote: 7

Created at 2021-02-15 10:48:45

------------

`Object.assign` didn't work for me. (Maybe because I used a shim for the Composition API in Nuxtjs 2?) For anybody that run into the same problem: I had to use a compact loop.
```
setup() {
    const initialState = {
      name: "",
      lastName: "",
      email: ""
    };

    const form = reactive({ ...initialState });

    function resetForm() {
        for (const [key, value] of Object.entries(initialState)) {
          form[key] = value
        }
    }

    function setForm(values = {name: "John", lastName: "Doe", email: "john@doe.com"}) {
        // only loop with the supported keys of initial state
        for (const key of Object.keys(initialState)) {
          form[key] = values[key]
        }
    }

    return { form, setForm, resetForm };
  }
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-04-27 17:33:35

------------

Citing from the official Vueland Discord server:



```
const myData = reactive({
  foo: true,
  bar: ''
})

function resetValues () {
 myData.foo = true
 myData.bar = ''
}
```


Therefore, if you don't change properties you should be able to use `Object.assign()`. (Correct me if I'm wrong)


------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-03-26 17:42:13

------------

How about use ref instead of reactive?
```
const myData = ref({ xxx: 11 })

// ... After many operations on myData 

// resetData
myData.value = { xxx: 11 }
```

The disadvantage is that you need to add .value when using it in script.
But this is the same as reactive in vue template.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-08-28 18:58:40

------------

If you have deep objects you can use this trick to reset the values converting them to JSON
```
setup(){
    const form = ref({
      order: '',
      user:{},
    });
    const defaultForm = JSON.parse(JSON.stringify(form));
    form.value.user = {firstname:"Joe", lastname:"Doe"};
    const onSubmit = () => {
      // Reset values to default
      form.value = JSON.parse(JSON.stringify(defaultForm));
    }

}
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-12-26 13:12:13

------------

If you do not want to use `ref` and want to reset value of a `reactive` variable then you have to take one key variable for reference in the reactive variable, because reinitializing reactive variable directly will cause to loose reactivity in the process.
So to reset the value use extra key variable in reactive variable, see code as below
```
setup() {
    const initialState = {
      name: "",
      email: ""
    };

    const form = reactive({ data: ...initialState });

    function resetForm() {
      form.data = { ...initialState };
    }

    function setForm() {
      form.data = {
        name: "Bahubali",
        email: "bahubali@mahismati.com"
      });
    }

    return { form, setForm, resetForm };
}
```

So basically you are taking `data` as key of your reactive variable and when you want to reset or set your values you just need to change the `form.data` and it will be reactive and refreshes the elements which uses `form.data`.


------------
    
    