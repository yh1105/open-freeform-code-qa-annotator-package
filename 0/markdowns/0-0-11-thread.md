
# Post \#59552387 [Link](https://stackoverflow.com/questions/59552387/)

## How to reload a page in Angular 8 the proper way

**Vote**: 9 (409/702) **Views**: 64314 (134/702) 

**Internal ID** \#0-0-11

Created at 2020-01-01 12:09:21

Tags: `angular` `typescript` `router` `angular8`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------



I have two utility methods - one for navigating to the parent node and one for reloading self. The first one works as supposed to, while the other one fails to cause the reload.

```
navigateToParent(route: ActivatedRoute) {
  const options: NavigationExtras = { relativeTo: route };
  this.router.navigate([".."], options);
}

navigateToSelf(route: ActivatedRoute) {
  this.router.routeReuseStrategy.shouldReuseRoute = () => false;
  this.router.onSameUrlNavigation = "reload";
  const self = ".";
  this.router.navigate([self]);

  // const options: NavigationExtras = { relativeTo: route };
  // this.router.navigate(["."], options);
}
```


I followed the answer [here](https://stackoverflow.com/a/57157316/1525840), with the only exception that I'd like my path navigated to, to be generic, not hard-coded. I've tried passing different parameters, like  and  etc. Nothing seems to give me the desired reload.

What do I miss?

I also tried to pick the parts from the  passed into the service's method but I only see a bunch of observables, not the actual segments. And, of course,  only caused an error.

Googling produced a lot of hints with vastly varying suggestions (e.g. [this](https://stackoverflow.com/questions/56715352/how-to-refresh-element-in-angular-8-without-page-reload)), which leads me to the suspicion that it might be heavily dependent on the version (I'm on 8.0) and, also, that many of the suggestions, although accepted, might be misleading and more harmful in the long run, without me realizing it.


----------
        
## Answer \#0

**Accepted** Vote: 13

Created at 2020-01-01 12:55:02

------------

You can find total working example here in this [StackBlitz Link](https://stackblitz.com/edit/angular-nqts82?file=src%2Fapp%2Fhome%2Fhome.component.ts)

First of all doing `window.location.reload()` is totally against of Single-Page-Application nature. rather, You can reload particular component when actually clicking on that link.. so, to do this task we have angular router. For particular component reload you can push this line of code in main  once for full application.
```
mySubscription;

 constructor(private router: Router, private activatedRoute: ActivatedRoute){
    this.router.routeReuseStrategy.shouldReuseRoute = () => false;
    this.mySubscription = this.router.events.subscribe((event) => {
      if (event instanceof NavigationEnd) {
         // Trick the Router into believing it's last link wasn't previously loaded
         this.router.navigated = false;
      }
    }); 
 }
```

above code we are going to `subscribing` to `router-events`. and then we are checking each and every `router-NavigationEnd` event, then just telling router, forget to add current navigation of router to its own history. So, each time whenever we are trying to reload same component each and every events are firing for that particular component only, thats a SPA.
IN  of `ngOnDestroy()` we have to unsubscribe from router events, when component is destroyed.
```
ngOnDestroy(){
  if (this.mySubscription) {
    this.mySubscription.unsubscribe();
  }
}
```

Now, for example you have Home and Details component or anything else of component... You can reload each component by calling `this.router.navigate([this.router.url])` this will reload all current component. for example, In `home component` we have `reload-button` and click event of that we are just calling `this.router.navigate([this.router.url])`. same for details component too.. or any other component..

```
reLoad(){
  this.router.navigate([this.router.url])
}
```


```
reLoad(){
  this.router.navigate([this.router.url])
}
```

You can check in updated above StackBlitz link, all working example of reloading with full router-state reloading. In browser console see each and every events of component is firing by clicking button `reload()`.


------------
    
    
## Answer \#1

 Vote: 19

Created at 2020-08-20 15:51:38

------------

ok this next example works for me without reload the page:
on router component you have tu add 'onSameUrlNavigation'
router.component:
```
@NgModule({
  imports: [RouterModule.forRoot(routes, { onSameUrlNavigation: 'reload' })],
  exports: [RouterModule],
})
```

now component you want reload
```
constructor(private router: Router){
  this.router.routeReuseStrategy.shouldReuseRoute = () => {
    return false;
  };
}

someFunction(){
  this.router.navigateByUrl('/route');
}
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2020-01-02 08:23:21

------------

on your `app-routing-module.ts` pls verify you have the `{onSameUrlNavigation: 'reload'}`

```
@ngModule({
 imports: [RouterModule.forRoot(routes, {onSameUrlNavigation: 'reload'})],
 exports: [RouterModule],
 })
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2020-06-12 05:19:36

------------

Try the following:
```
imports: [RouterModule.forRoot(routes, { onSameUrlNavigation: 'reload' })]
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-10-13 12:26:51

------------

```
public routerreuse: any;

ngOnInit(): void {
    this.routerreuse = this._router.routeReuseStrategy.shouldReuseRoute;
    this._router.routeReuseStrategy.shouldReuseRoute = (future: ActivatedRouteSnapshot, curr: ActivatedRouteSnapshot) => {
      return (curr != this.route.snapshot)
    };
}
   
ngOnDestroy(): void {
    this._router.routeReuseStrategy.shouldReuseRoute = this.routerreuse;
}
```

this should work when the same route url is changing with browser back in history. It reloads only the same content with the same route


------------
    
    
## Answer \#5

 Vote: -1

Created at 2021-12-15 21:00:51

------------

```
<button type="button" mat-raised-button color="warn" onclick="window.location.reload();">
```

Window.location.reload() on Button onClick property


------------
    
    
## Answer \#6

 Vote: -2

Created at 2020-01-02 07:07:26

------------

Try the following.

```
this.ngOnInit();
```


It works for me with same route reload.


------------
    
    
## Answer \#7

 Vote: -3

Created at 2020-01-02 08:25:51

------------

To Reload the same page...Try This 

```
window.location.reload();
```



------------
    
    