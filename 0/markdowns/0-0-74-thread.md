
# Post \#55565631 [Link](https://stackoverflow.com/questions/55565631/)

## How to get previous URL in Next.js?

**Vote**: 34 (179/702) **Views**: 62237 (137/702) 

**Internal ID** \#0-0-74

Created at 2019-04-08 02:53:48

Tags: `next.js`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

How can I get the previous URL in Next.js?
I thought the values `this.props.router.asPath` and `nextProps.router.asPath` are different.
Actually, I want to call `router.push` after login. I know that `router.back` goes to the previous page. But it's possible to go to another site. The users having history stacks go to the previous page, the users not having history stacks go to `/` main page.
```
import { Component } from 'react'
import App, { Container } from 'next/app';
import ErrorComponent from '@/components/error'

export default class MyApp extends App {
  render() {
    console.log(this.props)
    const { Component, pageProps, router } = this.props;
    const props = {
      ...pageProps,
      router
    }
    return (
      <ErrorBoundary>
        <Container>
          <Component {...props} />
        </Container>
      </ErrorBoundary>
    );
  }

  componentWillReceiveProps(nextProps) {
    // previous page url /contents
    console.log(this.props.router.asPath) // /about
    console.log(nextProps.router.asPath) // /about
    console.log('window.history.previous.href', window.history.previous) // undefined
  }
}
```

How can I fix it? Or how can I get the previous URL to move page after login?


----------
        
## Answer \#0

**Accepted** Vote: 11

Created at 2019-04-08 20:12:24

------------

I think you can implement a custom history in global state
Something like this

```
import React from 'react';
import App, { Container } from 'next/app';

class MyApp extends App {
    static async getInitialProps({ Component, ctx }) {
        let pageProps = {};

        if (Component.getInitialProps) {
            pageProps = await Component.getInitialProps(ctx);
        }

        return { pageProps };
    }

    state = {
        history: [] // keep history items in state
    };

    componentDidMount() {
        const { asPath } = this.props.router;

        // lets add initial route to `history`
        this.setState(prevState => ({ history: [...prevState.history, asPath] }));
    }

    componentDidUpdate() {
        const { history } = this.state;
        const { asPath } = this.props.router;

        // if current route (`asPath`) does not equal
        // the latest item in the history,
        // it is changed so lets save it
        if (history[history.length - 1] !== asPath) {
            this.setState(prevState => ({ history: [...prevState.history, asPath] }));
        }
    }

    render() {
        const { Component, pageProps } = this.props;

        return (
            <Container>
                <Component history={this.state.history} {...pageProps} />
            </Container>
        );
    }
}

export default MyApp;
```

so then in your components you can navigate wherever you want within history
```
if (!history || !history.length) {
    router.push('/');
} else {
    router.push(history[history.length - 1]);
}
```

Hope this helps!


------------
    
    
## Answer \#1

 Vote: 36

Created at 2020-08-03 18:39:40

------------

You find the Referer ( so the previous URL ) in the context of [getServerSideProps](https://nextjs.org/docs/basic-features/data-fetching#getserversideprops-server-side-rendering) or any other Data fetching methods
as
```
context.req.headers.referer
```

example in code
```
export async function getServerSideProps(context) {
  console.log(context.req.headers.referer)
}
```



------------
    
    
## Answer \#2

 Vote: 16

Created at 2020-12-15 15:57:11

------------

I've used Context do to this
In 
```
import { HistoryProvider } from '../contexts/History'

const MyApp: React.FC<AppProps> = ({ Component, pageProps }) => {
  return (
    <ThemeProvider theme={theme}>
      <Header />
        <HistoryProvider>
          <Component {...pageProps} />
        </HistoryProvider>...
```


```
import { useRouter } from 'next/router'
import React, { createContext, useState, useEffect, useContext } from 'react'

interface HValidation {
  history: string[]
  setHistory(data: string[]): void
  back(): void
}

const HistoryContext = createContext<HValidation>({} as HValidation)
export const HistoryProvider: React.FC = ({ children }) => {
  const { asPath, push, pathname } = useRouter()
  const [history, setHistory] = useState<string[]>([])

  function back() {
    for (let i = history.length - 2; i >= 0; i--) {
      const route = history[i]
      if (!route.includes('#') && route !== pathname) {
        push(route)

        // if you want to pop history on back
        const newHistory = history.slice(0, i)
        setHistory(newHistory)

        break
      }
    }
  }

  useEffect(() => {
    setHistory(previous => [...previous, asPath])
  }, [asPath])

  return (
    <HistoryContext.Provider
      value={{
        back,
        history,
        setHistory,
      }}
    >
      {children}
    </HistoryContext.Provider>
  )
}

export function useHistory(): HValidation {
  const context = useContext(HistoryContext)
  return context
}
```

In , you can use
```
import { useHistory } from '../../contexts/History'

const ContentHeader: React.FC<ContentHeaderProps> = ({ title, hideBack }) => {
    const { history, back } = useHistory() ...
```

I've used this component to back history ignoring links with hash (#), because the native  was bugging when i have `<a href="#someid" />` to scroll page to some page ids
I wanted to go back to last page, and not the last anchor

You can also set a fallback route for "back".
```
back(fallbackRoute?: string): void

function back(fallbackRoute?: string) {
  for (let i = history.length - 2; i >= 0; i--) {
    const route = history[i]
    console.log({ route, pathname })
    if (!route.includes('#') && route !== pathname) {
      push(route)
      const newHistory = history.slice(0, i)
      setHistory(newHistory)
      return
    }
  }
  if (fallbackRoute) {
    router.push(fallbackRoute)
  }
}
```



------------
    
    
## Answer \#3

 Vote: 3

Created at 2022-04-25 13:02:38

------------

I was looking for a very simple way to do this since some of the answers here seem a bit complex for implementing something this simple. `router.back()` doesn't seem to work well in this scenario as, in my case, it'd go all the way back and out of my site some times.
So, I thought , what better way to do this than `localStorage`?

```
if (!auth.user) {
  window.localStorage.setItem("path", router.asPath);
  router.replace("/login");
  return <div> redirecting to login... </div>;
}
```

 (the route of which has been saved in the localStorage
```
if (auth.user) {
  router.replace(localStorage.getItem("path") || "/");
  return <div> Loading... </div>
  );
}
```

You can observe the `localStorage` while testing to see what is going on. I hope this helps someone.


------------
    
    
## Answer \#4

 Vote: 2

Created at 2021-03-11 07:23:25

------------

Let's say there's a `/profile` page which should be rendered iff user is logged in or else user should be redirected to `/login`, after user login on `/login`, it should be pushed to previous page (here`/profile`) but not on another website or New Tab.

1. In /profile this is how you should redirect to /login


> `Router.push('/login?referer=profile', '/login')`[](https://i.stack.imgur.com/t0yRB.png)

1. In /login after user is successfully logged in, use:


> `Router.push(Router.query.referer?.toString||'/')`[](https://i.stack.imgur.com/1ZRxa.png)
Hope this helped.


------------
    
    
## Answer \#5

 Vote: 2

Created at 2021-12-22 15:02:04

------------

I recently had this problem and used the following solution to route back to the previous page.
In my component I used the `useRouter()` hook from Next.js. This hook produces a router object which has the `back()` function. This function can be used on an `<a>` tag to redirect back in the following way.
```
const Component: React.FC = () => {
  const router = useRouter();

  return (
    <>
      <a onClick={() => router.back()}>Go back to the last page</a>
    </>
  );
};
```

Note that this function does  produce a URL that you can use as a value in the href, which is unfortunate. But I think this solution is simple yet effective.
Reference: [https://nextjs.org/docs/api-reference/next/router#routerback](https://nextjs.org/docs/api-reference/next/router#routerback)


------------
    
    
## Answer \#6

 Vote: 2

Created at 2022-08-11 21:06:09

------------


## Simple Hook


Add this hook, and call it in your `_app.tsx` or where needed. You can compare it to `router.pathname` if you need to know what the change was.
```
const usePreviousRoute = () => {
  const { asPath } = useRouter();

  const ref = useRef<string | null>(null);

  useEffect(() => {
    ref.current = asPath;
  }, [asPath]);

  return ref.current;
};
```

This doesn't leave data behind once the page is closed (storage, cookies) and cleanly resets to null on next visit to the site.


------------
    
    
## Answer \#7

 Vote: 1

Created at 2022-10-18 21:04:56

------------

```
import { useState, useEffect } from "react";
import { useRouter } from "next/router";

// in _app.js

function MyApp({ Component, pageProps }) {
    const router = useRouter();

    const [history, setHistory] = useState({ previous: null, current: router.asPath });

    useEffect(() => {
        setHistory((oldHistory) => ({ ...oldHistory, previous: oldHistory.current, current: router.asPath }));
    }, [router.asPath]);

    return (<Component {...{ ...pageProps, history }} />);
}

export default MyApp;

// in a page

function MyPage({ ...pageProps }) {
    return (<span>Previous route: {pageProps.history?.previous || "/"}</span>);
}

export default MyPage;
```




------------
    
    
## Answer \#8

 Vote: 0

Created at 2020-12-14 18:29:51

------------

I tried doing similar to `iurii`'s answer. My `_app.js` looks like this (I was trying to integrate with segment.com so felt this need)
```
export default class MyApp extends App {
  componentDidMount () {
    const { asPath } = this.props.router;
    this.setState(prevState => ({ history: [...prevState.history, asPath] }));

    const isBrowser = typeof window !== 'undefined';

    if(isBrowser) {
      // For the first page load
      console.log("Going to log first load --> referrer : ", document.referrer);
      // this can get me the document.referrer properly, if I come to the website from a third party source like google search.
      global.analytics.page(window.location.href,
        {referrer: document.referrer}
      )
    }
  }

  static async getInitialProps ({ Component, router, ctx }) {
    let pageProps = {}

    if (Component.getInitialProps) {
      pageProps = await Component.getInitialProps(ctx)
    }

    return { pageProps }
  }

  state = {
    history: [] // keep history items in state
  };

  componentDidUpdate() {

    const { history } = this.state;
    const { asPath } = this.props.router;





    // if current route (`asPath`) does not equal
    // the latest item in the history,
    // it is changed so lets save it
    if (history[history.length - 1] !== asPath) {

      
      global.analytics.page(window.location.href, {
        referrer: history[history.length - 1] ? history[history.length - 1] : ""
      })
      // this simulates the document.referrer on pages after the user navigates
      this.setState(prevState => ({ history: [...prevState.history, asPath] }));
    }
  }
```

So with a combination of `history[history.length - 1] ? history[history.length - 1] : ""`  and `const isBrowser = typeof window !== 'undefined';` I am able to simulate document.referrer for all cases. But I am missing one case, suppose , I am at google, my site landing page is A, then A points to B
Then `google to A` --> I get `document.referrer` as `google`
Then `A to B` --> I get `document.referrer` as `A` which is consistent with the behavior.
But Now If I refresh page B, then my `document.referrer` becomes `google` again.
I think I can save the last known previous URL in local storage , but that will be a anti-pattern as the browser back button can correctly take the user to the previous page (`A`), so somewhere the data is there already. Currently I can live with this solution as I only use this for analytics purpose on segment.com and google analytics, so refreshing will mess up my analytics numbers slightly, but still looking forward to a perfect solution so get exact data.


------------
    
    
## Answer \#9

 Vote: 0

Created at 2022-03-15 11:08:01

------------

I can not get previous URL but with the code below I can find have a back URL or no:
```
typeof window !== 'undefined' && +window?.history?.state?.idx > 0
```


---


```
const back = async () => {
  if (typeof window !== 'undefined' && +window?.history?.state?.idx > 0) {
    await Router.back()
  } else {
    await Router.replace(fallbackURL)
  }
}
```



------------
    
    
## Answer \#10

 Vote: -1

Created at 2022-03-08 02:56:23

------------

A more simple way is
`import { useRouter } from 'next/router';`
`const router = useRouter();`
`router.back()`


------------
    
    