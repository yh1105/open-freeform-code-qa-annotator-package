
# Post \#43002444 [Link](https://stackoverflow.com/questions/43002444/)

## Make Axios send cookies in its requests automatically

**Vote**: 280 (6/702) **Views**: 524329 (5/702) 

**Internal ID** \#0-0-77

Created at 2017-03-24 14:44:09

Tags: `javascript` `node.js` `express` `cookies` `axios`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am sending requests from the client to my Express.js server using Axios. 

I set a cookie on the client and I want to read that cookie from all Axios requests without adding them manually to request by hand. 

This is my clientside request example:

```
axios.get(`some api url`).then(response => ...
```


I tried to access headers or cookies by using these properties in my Express.js server:

```
req.headers
req.cookies
```


Neither of them contained any cookies. I am using cookie parser middleware:

```
app.use(cookieParser())
```


How do I make Axios send cookies in requests automatically? 

Edit:

I set cookie on the client like this:

```
import cookieClient from 'react-cookie'

...
let cookie = cookieClient.load('cookie-name')
if(cookie === undefined){
      axios.get('path/to/my/cookie/api').then(response => {
        if(response.status == 200){
          cookieClient.save('cookie-name', response.data, {path:'/'})
        }
      })
    }
...
```


While it's also using Axios, it is not relevant to the question. I simply want to embed cookies into all my requests once a cookie is set. 


----------
        
## Answer \#0

**Accepted** Vote: 517

Created at 2017-04-03 06:43:43

------------

You can use [withCredentials](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/withCredentials) property.
XMLHttpRequest from a different domain cannot set cookie values for their own domain unless withCredentials is set to true before making the request.
```
axios.get(BASE_URL + '/todos', { withCredentials: true });
```

Also its possible to force credentials to every Axios requests
```
axios.defaults.withCredentials = true
```

Or using credentials for some of the Axios requests as the following code
```
const instance = axios.create({
   withCredentials: true,
   baseURL: BASE_URL
})
instance.get('/todos')
```



------------
    
    
## Answer \#1

 Vote: 70

Created at 2019-01-31 23:25:09

------------



`{ withCredentials: true }` or `axios.defaults.withCredentials = true`


---



From the axios documentation

> `withCredentials: false, // default`

`withCredentials` indicates whether or not cross-site Access-Control requests should be made using credentials

If you pass `{ withCredentials: true }` with your request it should work.

A better way would be setting `withCredentials` as `true` in `axios.defaults`

> `axios.defaults.withCredentials = true`


------------
    
    
## Answer \#2

 Vote: 29

Created at 2018-01-12 17:36:26

------------

It's also important to set the necessary headers in the express response. These are those which worked for me:

```
app.use(function(req, res, next) {
  res.header('Access-Control-Allow-Origin', yourExactHostname);
  res.header('Access-Control-Allow-Credentials', true);
  res.header('Access-Control-Allow-Headers', 'Origin, X-Requested-With, Content-Type, Accept');
  next();
});
```



------------
    
    
## Answer \#3

 Vote: 18

Created at 2017-04-08 10:03:01

------------

I am not familiar with Axios, but as far as I know in javascript and ajax there is an option 

```
withCredentials: true
```


This will automatically send the cookie to the client-side. As an example, this scenario is also generated with passportjs, which sets a cookie on the server


------------
    
    
## Answer \#4

 Vote: 17

Created at 2020-01-30 22:06:34

------------

So I had this exact same issue and lost about 6 hours of my life searching, I had the

`withCredentials: true`

But the browser still didn't save the cookie until for some weird reason I had the idea to shuffle the configuration setting:

```
Axios.post(GlobalVariables.API_URL + 'api/login', {
        email,
        password,
        honeyPot
    }, {
        withCredentials: true,
        headers: {'Access-Control-Allow-Origin': '*', 'Content-Type': 'application/json'
    }});
```


Seems like you should always send the 'withCredentials' Key first.


------------
    
    
## Answer \#5

 Vote: 14

Created at 2020-04-09 07:03:04

------------

You can use `withCredentials` property to pass cookies in the request.

```
axios.get(`api_url`, { withCredentials: true })
```


By setting `{ withCredentials: true }` you may encounter cross origin issue. To solve that 
you need to use 

```
expressApp.use(cors({ credentials: true, origin: "http://localhost:8080" }));
```


Here you can read about [withCredentials](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/withCredentials)


------------
    
    
## Answer \#6

 Vote: 10

Created at 2020-09-30 19:01:31

------------

What worked for me:
Client Side:
```
import axios from 'axios';

const url = 'http://127.0.0.1:5000/api/v1';

export default {
  login(credentials) {
    return axios
      .post(`${url}/users/login/`, credentials, {
        withCredentials: true,
        credentials: 'include',
      })
      .then((response) => response.data);
  },
};
```

Note: Credentials will be the body of the post request, in this case the user login information (Normally obtained from the login form):
```
{
    "email": "user@email.com",
    "password": "userpassword"
}
```

Server Side:
```
const express = require('express');
const cors = require('cors');

const app = express();
const port = process.env.PORT || 5000;

app.use(
  cors({
    origin: [`http://localhost:${port}`, `https://localhost:${port}`],
    credentials: 'true',
  })
);
```



------------
    
    
## Answer \#7

 Vote: 10

Created at 2021-08-26 09:29:56

------------

Fatih's answer is still valid and great in 2022.
Also `axios.defaults.withCredentials = true` will do the trick.
It seems passing `{ withCredentials: true }` to individual axios calls is deprecated.


------------
    
    
## Answer \#8

 Vote: 8

Created at 2019-05-18 10:51:39

------------

> How do I make Axios send cookies in requests automatically?

set `axios.defaults.withCredentials = true;`

or for some specific request you can use `axios.get(url,{withCredentials:true})`

> this will give CORS error if your 'Access-Control-Allow-Origin' is set to
  wildcard(*).
  Therefore make sure to specify the url of origin of your request

for ex: if your front-end which makes the request runs on localhost:3000 , then set the response header as

```
res.setHeader('Access-Control-Allow-Origin', 'http://localhost:3000');
```


also set 

```
res.setHeader('Access-Control-Allow-Credentials',true);
```



------------
    
    
## Answer \#9

 Vote: 7

Created at 2019-05-24 08:47:42

------------

for people still not able to solve it, this answer helped me.
[stackoverflow answer: 34558264](https://stackoverflow.com/questions/34558264/fetch-api-with-cookie)

TLDR;
one needs to set `{withCredentials: true}` in both GET request as well the POST request (getting the cookie) for both axios as well as fetch.


------------
    
    
## Answer \#10

 Vote: 6

Created at 2017-10-07 03:40:49

------------

Another solution is to use this library:

[https://github.com/3846masa/axios-cookiejar-support](https://github.com/3846masa/axios-cookiejar-support)

which integrates "Tough Cookie" support in to Axios.  Note that this approach still requires the `withCredentials` flag.


------------
    
    
## Answer \#11

 Vote: 2

Created at 2022-08-22 05:59:28

------------

After trying for 2 days long and after trying out from the suggestions here this is what worked for me.

1. express: cors: cors({ origin: "http:127.0.0.1:3000", credentials: true, }) Cookie : Make sure your cookie has secure: true, sameSite: "None"
2. Frontend(React)


- `axios.defaults.withCredentials = true;`
Hope this helps others as well.


------------
    
    
## Answer \#12

 Vote: 1

Created at 2017-04-05 10:18:40

------------

You are getting the two thinks mixed.

You have "react-cookie" and "axios"

react-cookie => is for handling the cookie on the client side

axios => is for sending ajax requests to the server

With that info, if you want the cookies from the client side to be communicated in the backend side as well, you will need to connect them together.

Note from "react-cookie" Readme:

> Isomorphic cookies!To be able to access user cookies while doing server-rendering, you
  can use plugToRequest or setRawCookie.

[link to readme](https://github.com/reactivestack/react-cookie)

If this is what you need, great.

If not, please comment so I could elaborate more.


------------
    
    
## Answer \#13

 Vote: 1

Created at 2020-11-01 07:47:00

------------

For anyone where none of these solutions are working, make sure that your request origin equals your request target, see [this github issue](https://github.com/axios/axios/issues/1661).
I short, if you visit your website on 127.0.0.1:8000, then make sure that the requests you send are targeting your server on 127.0.0.1:8001 and not localhost:8001, although it might be the same target theoretically.


------------
    
    
## Answer \#14

 Vote: 1

Created at 2021-07-28 15:10:01

------------

This worked for me:
- - 
See code below:
```
const ax = axios.create({
  baseURL: 'yourbaseUrl',
  withCredentials: true,
});

const loginUser = () => { const body ={username:state.values.email, password:state.values.password};
ax.post('/login',body).then(function(response){
return response}).then().catch(error => console.log(error));}
```

source:
[https://www.npmjs.com/package/axios#creating-an-instance](https://www.npmjs.com/package/axios#creating-an-instance)


------------
    
    
## Answer \#15

 Vote: 0

Created at 2022-08-09 01:55:15

------------

This won't apply to everyone, but I was using a React frontend with Vite and it was serving the `localhost` as `127.0.0.1:5173`, which is what I put as the CORS allowable domain. As soon as I both to `localhost` everything worked as expected!


------------
    
    
## Answer \#16

 Vote: 0

Created at 2022-08-16 02:06:29

------------


```
const API = axios.create({
    baseURL: "http://localhost:4000", // API URL
    withCredentials: true,
});
```


`npm i cors`
```
var cors = require("cors"); // This should be at the end of all middlewares

const corsOptions = {
    origin: "http://localhost:3000",
    credentials: true, //access-control-allow-credentials:true
    optionSuccessStatus: 200,
};

app.use(cors(corsOptions));
```



------------
    
    
## Answer \#17

 Vote: 0

Created at 2022-09-23 18:40:29

------------

In my case, the problem was with the cookie, not with Axios; although I was receiving and sending the cookie from / to the same domain / subdomain / host, I was expecting it to work with different resources in different paths - but my coookie was acting like I had set it to a single `Path`, even though I omitted that attribute.  Explicitly setting `Path=/;` in the cookie solved the issue.


------------
    
    
## Answer \#18

 Vote: 0

Created at 2023-01-06 06:59:25

------------

Set the proxy in  and  the server again (problem solved)


------------
    
    