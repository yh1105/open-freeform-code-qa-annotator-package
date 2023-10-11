
# Post \#53739908 [Link](https://stackoverflow.com/questions/53739908/)

## Trigger client-side reload in next.js

**Vote**: 27 (213/702) **Views**: 72444 (120/702) 

**Internal ID** \#0-0-36

Created at 2018-12-12 09:30:39

Tags: `node.js` `next.js`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------


# Scenario



The index page uses "getInitialProps" to load data. Then we create a dialog which can create a new data. After creating a new data, the current page should be reloaded.


# Problem



We use `Router.replace('/')` to reload the page. But it triggers a server-side rendering.


# Question



What we need is a client-side reload. The "getInitialProps" function should be called in the browser. So, how to do the client-side reload?


----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2022-11-02 22:28:24

------------

as of of NextJs 13 Being released the new router Object has the `refresh()` method, which allows you to refresh ( re-fetch ) the data client-side only without having to do a full page reload.
it can be used like the following :
```
import { useRouter } from "next/navigation";

export default function Component(){
  const router = useRouter();

  //this will reload the page without doing SSR
  router.refresh();
}
```

Please keep in mind that this is feature of NextJs 13 Which is currently in beta.
check the beta docs [here](https://beta.nextjs.org/docs/api-reference/use-router)
credits to fireship for showing the feature 
[https://www.youtube.com/watch?v=__mSgDEOyv8](https://www.youtube.com/watch?v=__mSgDEOyv8)


------------
    
    
## Answer \#1

 Vote: 20

Created at 2020-04-26 06:35:15

------------

Assume, a user is on the
`http://www.example.com/profile`
the user has edited the profile and for some reason, you need to reload the same page.
If your case is similar to this, you could use `Router.reload();`
, you must import Router object like this `import Router from "next/router";`
For more information: [https://nextjs.org/docs/api-reference/next/router#routerreload](https://nextjs.org/docs/api-reference/next/router#routerreload)


------------
    
    
## Answer \#2

 Vote: 15

Created at 2021-06-17 08:43:04

------------

A better option is to use `router.replace(router.asPath)`
replace does not create a new history item so Back button works as expected
Details explained in this article
[https://www.joshwcomeau.com/nextjs/refreshing-server-side-props/](https://www.joshwcomeau.com/nextjs/refreshing-server-side-props/)


------------
    
    
## Answer \#3

 Vote: 12

Created at 2020-05-15 04:52:47

------------

Didn't see an answer that was really what I was looking for, 

this worked for me:

```
import Router from 'next/router'

Router.reload(window.location.pathname);
```



------------
    
    
## Answer \#4

 Vote: 2

Created at 2018-12-13 12:51:12

------------

Running the following code



```
import Router from 'next/router';

function Index({ isServer }) {
  console.log('render', new Date());

  return (
    <div>
      <h1>Home page. Is it on server? - {isServer ? 'Yes' : 'No'}</h1>

      <button type="button" onClick={() => Router.push('/')}>
        Reload me
      </button>
    </div>
  );
}

Index.getInitialProps = async ({ req }) => {
  return { isServer: !!req };
};

export default Index;
```


I can see that it `console.log`s render only once on the server even after clicking "Reload me". I can see that `isServer` equals to false after "reload" as well.

How do you exactly reload the page so you see it is rendered on server side?

PS

Here are screenshots:

- [initial load](https://www.dropbox.com/s/r7comkxx571xlpe/Screenshot%202018-12-13%2013.51.54.png?dl=0)- [after clicking reload](https://www.dropbox.com/s/0i7wdf0aosl0uvf/Screenshot%202018-12-13%2013.52.44.png?dl=0)


------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-07-09 11:17:36

------------

It may help someone, If you are using nextjs Link its actually preloades page if you need default reload you need to use a tag not a Link


------------
    
    