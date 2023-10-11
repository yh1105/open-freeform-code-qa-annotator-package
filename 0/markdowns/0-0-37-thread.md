
# Post \#62371711 [Link](https://stackoverflow.com/questions/62371711/)

## how to use optional url parameters with NestjS

**Vote**: 18 (275/702) **Views**: 30908 (229/702) 

**Internal ID** \#0-0-37

Created at 2020-06-14 11:12:22

Tags: `node.js` `typescript` `express` `nestjs` `fastify`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm trying to replace our current backend service using Nestjs library, 
however, I want to create a route with 2 optional parameters in the URL something like : 

`/route/:param1/config/:OptionalParam3?/:OptionalParam3?` 

that means the route should catch : 


1. route/aa/config
2. route/aa/config/bb
3. route/aa/config/bb/cc



how can I achieve that, I have tried to use `?` and `()` but it's not working well.


----------
        
## Answer \#0

**Accepted** Vote: 14

Created at 2020-06-16 12:12:00

------------

Router params name should be unique. The correct route path is:

Existing one is:

```
/route/:param1/config/:OptionalParam3?/:OptionalParam3?
```


Correction:

```
/route/:param1/config/:OptionalParam3?/:OptionalParam4?
```


Opinion: You can use query params if the params are optional. It is never a good idea to create optional param routes (disagreements agreed). Both serve the same purpose, but having them as the query params makes it more understandable for debugging and fellow developers.


------------
    
    
## Answer \#1

 Vote: 17

Created at 2021-09-07 16:03:14

------------

If you are looking for how to annotate an optional query parameter, you can do it like so:
```
@ApiQuery({
  name: "myParam",
  type: String,
  description: "A parameter. Optional",
  required: false
})
async myEndpoint(
  @Query("myParam") myParam?: string
): Promise<blah> { 
  [...] 
}
```



------------
    
    
## Answer \#2

 Vote: 5

Created at 2022-04-06 12:35:57

------------

I solved this problem by using `@Query` decorator as below:
Here is my controller:
```
@Get()
async getAll(@Query('someParameter') someParameter?: number) {
  return this.service.getAll(someParameter);
}
```

Here is my client (Angular) service:
```
getAll(someParameter?: number) {
  return this.http.get(`apiUrl/controllerAddress?someParameter=${someParameter}`
  );
}
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-12-02 09:14:38

------------

You can use this structure:
-route
-aa
-config
-[[...id]].js
It will work for the routes :
route/aa/config/{anything}


------------
    
    