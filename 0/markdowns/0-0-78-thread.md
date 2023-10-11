
# Post \#58201173 [Link](https://stackoverflow.com/questions/58201173/)

## How to push to vue-router without adding to history?

**Vote**: 23 (239/702) **Views**: 18785 (312/702) 

**Internal ID** \#0-0-78

Created at 2019-10-02 12:06:07

Tags: `javascript` `vue.js` `vue-router`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have the following sequence happening:

- Main screen- Loading screen- Results screen

On homepage, when someone clicks a button, I send them to the loading screen, thru:

`this.$router.push({path: "/loading"});`

And once their task finishes, they are sent to the results screen via  

`this.$router.push({path: "/results/xxxx"});`

The problem is, usually they want to go from results back to the main screen, but when they click back, they're sent to loading again which sends them back to results, so they're stuck in an infinite loop & unable to go back to main screen.

Any ideas how to fix this? I'd ideally like if there was an option like:

`this.$router.push({path: "/loading", addToHistory: false});`

which would send them to the route without adding it to history.


----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2019-10-18 20:31:12

------------



You can use [in-component guard](https://router.vuejs.org/guide/advanced/navigation-guards.html#in-component-guards) to control the route in granule level

Make the following changes in your code



> Add this beofreRouteLeave guard in component options, before leaving to 'result screen' you are setting the route to go only
  through loading screen

```
beforeRouteLeave(to, from, next) {
   if (to.path == "/result") {
      next('/loading')
    }
    next();
  },
```




> If the route go backs from result to loading then , it should not land
  here and directly jump to main screen

```
beforeRouteEnter(to, from, next) {
    if (from.path == "/result") {
      next('/main')
    }
     next();
  },
```


> In loading screen, The beforeRouteEnter guard does NOT have access to
  this, because the guard is called before the navigation is confirmed,
  thus the new entering component has not even been created yet. So taking the advantage of this, you  fired when routing from results screen



> if you use go back then it should not land in loading and directly
  jump to main screen

```
beforeRouteLeave(to, from, next) {
    if (to.path == "/loading") {
      next('/')
    }
    next();
  },
```


I have just created small vue application to reproduce the same issue. It works in my local as per your question. Hope it  as well. 


------------
    
    
## Answer \#1

 Vote: 16

Created at 2021-03-12 21:40:58

------------

This should have a real answer using `this.$router.replace`:
```
// On login page

// Use 'push' to go to the loading page.
// This will add the login page to the history stack.
this.$router.push({path: "/loading"});

// Wait for tasks to finish

// Use 'replace' to go to the results page.
// This will not add '/loading' to the history stack.
this.$router.replace({path: "/results/xxxx"});
```

For further reading the Vue Router is using [History.pushState()](https://developer.mozilla.org/en-US/docs/Web/API/History/pushState) and [History.replaceState()](https://developer.mozilla.org/en-US/docs/Web/API/History/replaceState) behind the scenes.


------------
    
    
## Answer \#2

 Vote: 3

Created at 2019-10-18 21:41:47

------------

I guess `router.replace` is the way to go - but still some lines of thought (untested):


---



Basically on `$router` change it renders the loading-component until it emits `load:stop`, then it renders the `router-view`


---



```
import { Vue, Component, Watch, Prop } from "vue-property-decorator";

@Component<RouteLoader>({
    render(h){
        const rv = (this.$slots.default || [])
        .find(
            child => child.componentOptions
            //@ts-ignore 
            && child.componentOptions.Ctor.extendedOptions.name === "RouterView"
        )
        if(rv === undefined) 
            throw new Error("RouterView is missing - add <router-view> to default slot")

        const loader = (this.$slots.default || [])
        .find(
            child => child.componentOptions
            //@ts-ignore 
            && child.componentOptions.Ctor.extendedOptions.name === this.loader
        )
        if(loader === undefined) 
            throw new Error("LoaderView is missing - add <loader-view> to default slot")
        const _vm = this 
        const loaderNode = loader.componentOptions && h(
            loader.componentOptions.Ctor,
            {
                on: {
                    // "load:start": () => this.loading = true,
                    "load:stop": () => _vm.loading = false
                },
                props: loader.componentOptions.propsData,
                //@ts-ignore
                attrs: loader.data.attrs
            }
        )
        return this.loading && loaderNode || rv
    }
})
export default class RouteLoader extends Vue {
    loading: boolean = false
    @Prop({default: "LoaderView"}) readonly loader!: string
    @Watch("$route")
    loads(nRoute: string, oRoute: string){
        this.loading = true
    }
}

@Component<Loader>({
    name: "LoaderView",
    async mounted(){

        await console.log("async call")
        this.$emit("load:stop")
        // this.$destroy()
    }
})
export class Loader extends Vue {}
```



------------
    
    
## Answer \#3

 Vote: 2

Created at 2019-10-18 18:52:19

------------

This is a tough call considering how little we know about what's occurring in your loading route.

But...

I've never had a need to build a loading route, only ever loading component(s) that gets rendered on multiple routes during init/data gathering stage.

One argument for not having a loading route would be that a user could potentially navigate directly to this URL (accidentally) and then it seems like it wouldn't have enough context to know where to send the user or what action to take. Though this could mean that it falls through to an error route at this point. Overall, not a great experience.

Another is that if you simplify your routes, navigation between routes becomes much simpler and behaves as expected/desired without the use of `$router.replace`.

I understand this doesn't solve the question in the way you're asking. But I'd suggest rethinking this loading route.



```
<shell>
    <router-view></router-view>
</shell>

const routes = [
  { path: '/', component: Main },
  { path: '/results', component: Results }
]

const router = new VueRouter({
  routes,
})

const app = new Vue({
  router
}).$mount('#app')
```




```
<div>
    <header>
        <nav>...</nav>
    </header>
    <main>
        <slot></slot>
    </main>
</div>
```




```
<section>
    <form>...</form>
</section>

{
    methods: {
        onSubmit() {
            // ...

            this.$router.push('/results')
        }
    }
}
```




```
<section>
    <error v-if="error" :error="error" />
    <results v-if="!error" :loading="loading" :results="results" />
    <loading v-if="loading" :percentage="loadingPercentage" />
</section>

{
    components: {
        error: Error,
        results: Results,
    },
    data() {
        return {
            loading: false,
            error: null,
            results: null,
        }
    },
    created () {
        this.fetchData()
    },
    watch: {
        '$route': 'fetchData'
    },
    methods: {
        fetchData () {
            this.error = this.results = null
            this.loading = true

            getResults((err, results) => {
                this.loading = false

                if (err) {
                    this.error = err.toString()
                } else {
                    this.results = results
                }
            })
        }
    }
}
```




Basically the exact same results component you already have, but if `loading` is true, or if `results` is null, however you prefer, you can create a fake dataset to iterate over and create skeleton versions, if you'd like to. Otherwise, you can just keep things the way you have it.


------------
    
    
## Answer \#4

 Vote: 1

Created at 2019-10-24 17:40:57

------------

Another option is to use the [History API](https://developer.mozilla.org/en-US/docs/Web/API/History_API).

Once you are in the Results screen, you can utilize the [ReplaceState](https://developer.mozilla.org/en-US/docs/Web/API/History/replaceState) to replace the URL in history of the browser.


------------
    
    
## Answer \#5

 Vote: 0

Created at 2019-10-18 19:53:44

------------

This can be done with the `beforeEach` hook of the router.

What you need to do is you must save a variable globally or in localStorage in the `loading` component when the data is loaded (before redirecting to the `results` component):

```
export default {
  name: "results",
  ...
  importantTask() {
    // do the important work...
    localStorage.setItem('DATA_LOADED', JSON.stringify(true));
    this.$router.push({path: "/results/xxxx"});
  }
}
```


And then you should check for this variable in the beforeEach hook and skip to the correct component:

```
// router.js...
router.beforeEach((to, from, next) => {
  const dataLoaded = JSON.parse(localStorage.getItem('DATA_LOADED'));
  if (to.name === "loading" && dataLoaded)
  {
    if (from.name === "results")
    {
      next({ name: "main"} );
    }
    if (from.name === "main")
    {
      next({ name: "results"} );
    }
  }
  next();
});
```


Also, do remember to reset the value to false in your `main` component when the query button is clicked (before routing to the `loading` component):

```
export default {
  name: "main",
  ...
  queryButtonClicked() {
    localStorage.setItem('DATA_LOADED', JSON.stringify(false));
    this.$router.push({path: "/loading"});
  }
}
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2020-04-08 16:14:44

------------

Your loading screen should not be controlled by vue-router at all.
The best option is to use a modal/overlay for your loading screen, controlled by javascript. There are lots of examples around for vue. If you cant find anything then vue-bootstrap has some easy examples to implement.


------------
    
    