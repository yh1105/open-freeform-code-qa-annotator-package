
# Post \#63661915 [Link](https://stackoverflow.com/questions/63661915/)

## TypeError: expressJwt is not a function

**Vote**: 3 (575/702) **Views**: 8828 (438/702) 

**Internal ID** \#2-5-365

Created at 2020-08-30 20:38:34

Tags: `java` `node.js` `express` `jwt` `express-jwt`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java` `javascript`)

----------

**Notepad**


----------

I'm trying to write middleware for user authorization in my app. I use this function to check if a route requires being sign in.
The code is as follows:
```
const { expressJwt } = require('express-jwt'); 

exports.requireSignin = expressJwt({
secret: process.env.JWT_SECRET,
algorithms: ["HS256"],
userProperty: "auth",});
```

However, I get the following error:
```
TypeError: expressJwt is not a function at Object.<anonymous> (path to the file)\
```

What could be the problem? None of the other answers seem to be helpful.


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2020-08-30 20:45:31

------------

With the curly brackets
```
const { expressJwt } = require('express-jwt');
      ^            ^
```

you are trying to do , which looks for a field named `expressJwt` in the object exported by `express-jwt` module. But according to the error message that object doesn't have such field.
As per [express-jwt's documentation](https://www.npmjs.com/package/express-jwt#usage), you don't need destructuring but to simply assign the exported object into a variable, so try the following form (without curly brackets):
```
const expressJwt = require('express-jwt');
```



------------
    
    
## Answer \#1

 Vote: 6

Created at 2022-05-16 09:00:32

------------


With version greater than 7.x, use this as doc said ([https://www.npmjs.com/package/express-jwt](https://www.npmjs.com/package/express-jwt)) :
```
const { expressjwt: jwt } = require("express-jwt");
```

Then you can use `jwt`:
```
jwt({
  secret: "shhhhhhared-secret",
  audience: "http://myapi/protected",
  issuer: "http://issuer",
  algorithms: ["HS256"],
});
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-10-08 03:07:40

------------

this work for me without high severity vulnerability caused by low version:

1. npm uninstall express-jwt
2. npm install express-jwt@6.1.0




------------
    
    
## Answer \#3

 Vote: 0

Created at 2020-08-30 20:46:20

------------

Reference to the [readme](https://github.com/auth0/express-jwt).
If I am not mistaken the right function is named jwt
```
//const { expressJwt } = require('express-jwt');

//use this 
var jwt = require('express-jwt');
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-05-03 16:16:58

------------

I tried all the possible solution found on multiple programming forums, but nothing worked, so I just downgrade my `express-jwt` version to `5.3.1` and it start working perfectly, so it means error is in new update, just uninstall current version with `npm uninstall express-jwt` and install again `npm I express-jwt@5.3.1`, it will help.


------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-08-15 09:43:29

------------

Following the documentation worked for me:
```
var { expressjwt: jwt } = require("express-jwt");

var jwtCheck = jwt({
      secret: jwks.expressJwtSecret({
          cache: true,
          ...more stuff
})
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-11-04 23:46:54

------------

If you don't have an algorithm in the expressJwt object, add it next to the secret, separated by `,` and use cypher such as RS256. It happened to me.


------------
    
    