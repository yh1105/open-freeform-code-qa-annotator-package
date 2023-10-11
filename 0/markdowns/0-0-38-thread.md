
# Post \#71420974 [Link](https://stackoverflow.com/questions/71420974/)

## Angular make subscribe to wait for response

**Vote**: 2 (631/702) **Views**: 15775 (344/702) 

**Internal ID** \#0-0-38

Created at 2022-03-10 08:31:10

Tags: `javascript` `angular` `typescript` `asynchronous`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm trying to subscribe to an Observable and assign some data from the response, but somehow my code it's not waiting for the response. Basically the console.log(this.newIds) is runned first and  is always empty because the subscribe doesn't wait for response to come from the backend. How I can force my code to wait for the response to come?
```
this.repository.getById(Ids).subscribe((response) => {
      console.log(response);
      this.newIds = response.map((id) => {
        return id;
      });
    });
    console.log(this.newIds);
```



----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2022-03-10 08:37:09

------------

If you put the code in the subscribe callback. It will execute after your receive a response from the back-end. All code you write outside this function is directly execute.
```
this.repository.getById(Ids).subscribe((response) => {
          //Code will execute when back-end will respond
          console.log(response);
          this.newIds = response.map((id) => {
            return id;
          });
          console.log(this.newIds);
        });
//Code will execute immediately
```

See also : [https://angular.io/guide/observables#creating-observables](https://angular.io/guide/observables#creating-observables)


------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-03-10 08:37:18

------------

This is the normal behaviour because your `console.log(this.newIds);` is outside of the subscription, you just need to move it inside the .subscribe() method:
```
this.repository.getById(Ids).subscribe((response) => {
          console.log(response);
          this.newIds = response.map((id) => {
            return id;
          });
          console.log(this.newIds);
    });
```

if you want to use this.newIds outside the subscription and immediately after the result of the observer you can use RxJs .toPromise() to use it as a promise and change the method to async:
```
async callerFn(){

    const response = await this.repository.getById(Ids).toPromise();
  
    this.newIds = response.map((id) => {
        return id;
    });
    console.log(this.newIds);
    // use your property here

 }
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-03-10 08:37:41

------------

Yes, Because the Javascript is interpreting line-by-line execution so that it will not wait for other processes to complete. That's why the last console will return undefined. In the same time if you use the console inside of the subscriber then you will get proper log because the subscriber will wait for the response and bind it with this.newIds
```
this.repository.getById(Ids).subscribe((response) => {
      console.log(response);
      this.newIds = response.map((id) => {
        return id;
      });
     console.log(this.newIds);
    });
```

Here I'm attaching a good read about the observable subscribe
[https://betterprogramming.pub/observables-vs-promises-which-one-should-you-use-c19aef53c680](https://betterprogramming.pub/observables-vs-promises-which-one-should-you-use-c19aef53c680)
In addition to this, If you want to go with the newIds access outside of subscriber scope please use promise with async await. Here I'm adding a sample
```
async getAsyncData() {
    this.asyncResult = await this.httpClient.get<Employee>(this.url).toPromise();
    console.log('No issues, I will wait until promise is resolved..');
  }
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-03-10 09:35:25

------------

You can do like this..
your component file like below
```
newIds: Observable<any> = of(this.id).pipe(
concatMap((id) =>
  this.getId(id).pipe(map((data) => data.map((rowId) => rowId.id)))
)
);
getId(id: any) {
  return of([{ id: 1 }, { id: 2 }, { id: 3 }]);
}
```

your html file like below, and use async pipe for subscription. here you can use concateMap pipe rxjs operator to sequentially calling observable and then assing value to your newId varible.
```
<pre>
  {{ newIds | async }}
</pre>
```

Demo in this live link [Stackblitz Link](https://stackblitz.com/edit/angular-ivy-n56dxw?file=src%2Fapp%2Fapp.component.ts)


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-03-10 08:45:05

------------

I would approach in a different way: if you have to remap the value you can use [map](https://www.learnrxjs.io/learn-rxjs/operators/transformation/map) operator:
```
this.repository.getById(Ids)
  .pipe(map(response) => response.map(id => id))
  .subscribe((id) => {
      console.log(response);
      this.newIds = id;
  });
```

Actually, I don't understand why you need to map a value that you already have, but I think this is the clear solution.


------------
    
    