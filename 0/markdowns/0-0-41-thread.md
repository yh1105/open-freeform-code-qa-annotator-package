
# Post \#60502097 [Link](https://stackoverflow.com/questions/60502097/)

## Angular geolocation

**Vote**: 5 (499/702) **Views**: 15718 (345/702) 

**Internal ID** \#0-0-41

Created at 2020-03-03 07:28:29

Tags: `angular` `typescript`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm stuck at `geolocation`. Actually it working fine but some problem appear when I using `If else`. When User click allow location it working but the problem appear when user click not allow, it not insert in `else`. This [DEMO](https://stackblitz.com/edit/angular-geolocation-blitz-yvyeyv) as reference.



```
ngOnInit() {
       this.getUserLocation();
    }

    getUserLocation() {
       if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(position => {
            this.lat = position.coords.latitude;
            this.lng = position.coords.longitude;
          });
    }else {
       console.log("User not allow")

    }
}
```



----------
        
## Answer \#0

**Accepted** Vote: 8

Created at 2020-03-03 08:00:31

------------

@shayan ans is the correct approach to get your desire result. I will modify his approach a little bit to meet your expectations.

Try something like this

```
navigator.geolocation.getCurrentPosition(function(){
        alert('Location accessed')
},function(){
       alert('User not allowed')
},{timeout:10000})
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2020-03-03 07:31:39

------------

Trying this might help you:

`navigator.geolocation.getCurrentPosition(successCallback,errorCallback,{timeout:10000});`

It worked for me though. Your thoughts?


------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-03-21 17:25:45

------------

If you are working with the geolocation API in Angular, this simple library might be useful [https://github.com/ng-web-apis/geolocation](https://github.com/ng-web-apis/geolocation)

Just catch error from geolocation$ observable(from library) with RxJs

The library also has the GEOLOCATION_SUPPORT token, which returns false if the user's browser does not support the geolocation API


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-12-19 14:10:40

------------

```
getCurrentLocation() {
    // GeolocationCoordinates is global interface
    return new Observable<GeolocationCoordinates>((observer) => {
      window.navigator.geolocation.getCurrentPosition(
        (position) => {
          // Observable will call the Observer’s next(value) method to provide data.
          observer.next(position.coords);
          // observer.complete() marks the observable as done. It means we can no longer emit any more values out of this observable
          observer.complete();
        },
        // observer.error puts observable into an error state. It means we can no longer emit any more values out of this observable
        (err) => observer.error(err)
      );
    }).pipe(
      // When retry receives the error, it will resubscribe to that observable with the same kind of pipe. In rxjs, all observables are cold and unicast. That means whenever we subscribe to an observable, all the logic inside there is going to be executed right away. When retry operator resubscribes to this observable, that really means, all the source observable code re executes, because we are adding a second subcription to it. It does not somehow just magically get that logic to rerun. it actually resubscribes to that observable.
      // the argument to retry is an integer. `retry(1)` it is the number of times you want to retry your logic or really resubscribe. if you do not provide a number, and then it is going to try to resubscribe an infinite number of times.
      retry(1), // OPTIONAL
      // "TAP" generally used for notification or logging system. tap executes if the observable emits value which will be the first arg of tab. if it emits error, second arg of "tab" will run
      // the second argument is a function that gets called any time an error comes out
      // third argument can be a function that will be invoked any time the observable is marked as complete

      tap(
        () => {
          // create a notification service or console.log it
          this.notificationsService.addSuccess('Got your location');
        }
        // instead of this, use catchError which returns an observable
        // (err) => {
        //   this.notificationsService.addError('failed to get your location');
        // }
      ),
      // it will be invoked if our source observable emits an error.
      catchError((error) => {
        // inside here we always two different things
        // 1st handle the error
        this.notificationsService.addError('failed to get your location');
        // #2 return a new observable. that is the requirement of catchError operator
        // why do we return a new observable. that's the reason is why we want to use the catch operator instead of second arg to the tap function.
        // if we cannot fetch anything, we could show a default location
        // or we want to pass the error to the rest of the pipeline.

        return throwError(error);
      })
    );
  }
```



------------
    
    