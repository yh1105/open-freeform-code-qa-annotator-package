
# Post \#60250800 [Link](https://stackoverflow.com/questions/60250800/)

## Lazy loaded React router routes loading anyway

**Vote**: 14 (336/702) **Views**: 16648 (338/702) 

**Internal ID** \#0-0-90

Created at 2020-02-16 16:48:08

Tags: `reactjs` `lazy-loading` `react-router-dom`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I've been trying to lazy load routes in React using React.lazy and Suspense. But some components are loading regardless of the current route, exactly: Feed, Profile and Settings.
Notice I don't actually want to lazy load Components like MenuAppBar and SnackAlert but if I import them normally and remove their Suspense, code-splitting straight doesn't even work and everything loads and the whole app is just a single chunk.
![](https://i.stack.imgur.com/a3gNw.jpg)
```
import {createMuiTheme, MuiThemeProvider} from "@material-ui/core";
import {yellow} from "@material-ui/core/colors";
import CssBaseline from "@material-ui/core/CssBaseline";
import axios from "axios";
import React, {lazy, Suspense, useEffect, useState} from "react";
import {BrowserRouter as Router, Route, Switch} from "react-router-dom";
import "./css/feed.css";
import "./css/style.css";

const Feed = lazy(() => import("./routes/Feed"));
const Profile = lazy(() => import("./routes/Profile"));
const Home = lazy(() => import("./routes/Home"));
const Settings = lazy(() => import("./routes/Settings"));
const NotFound = lazy(() => import("./routes/NotFound"));
const MenuAppBar = lazy(() => import("./components/MenuAppBar"));
const SnackAlert = lazy(() => import("./components/SnackAlert"));

const App: React.FC = () => {
    const [isLogged, setIsLogged] = useState(localStorage.getItem("token") ? true : false);

    const [user, setUser] = useState<User>(
        isLogged ? JSON.parse(localStorage.getItem("userInfo") as string) : {admin: false}
    );
    const [openError, setOpenError] = useState<boolean>(false);
    const [errorMsg, setErrorMsg] = useState<string>("");
    const [severity, setSeverity] = useState<Severity>(undefined);
    const [pwa, setPwa] = useState<any>(null);
    const [showBtn, setShowBtn] = useState<boolean>(false);
    const [isLight, setIsLight] = useState<boolean>(
        (JSON.parse(localStorage.getItem("theme") as string) as boolean) ? true : false
    );

    const theme: customTheme = {
        darkTheme: {
            palette: {
                type: "dark",
                primary: {
                    main: yellow[600]
                }
            }
        },
        lightTheme: {
            palette: {
                type: "light",
                primary: {
                    main: yellow[700]
                }
            }
        }
    };

    window.addEventListener("beforeinstallprompt", (event) => {
        event.preventDefault();
        setPwa(event);
        setShowBtn(true);
    });

    window.addEventListener("appinstalled", (e) => {
        setShowBtn(false);
        setErrorMsg("App installed!");
        setSeverity("success");
        setOpenError(true);
    });

    const handleClick = () => {
        if (/iPhone|iPad|iPod/i.test(navigator.userAgent)) {
            setErrorMsg(`Please, open the share menu and select "Add to Home Screen"`);
            setSeverity("info");
            setOpenError(true);
        } else {
            if (pwa) {
                pwa.prompt();
                pwa.userChoice.then((choiceResult: {outcome: "accepted" | "refused"}) => {
                    if (choiceResult.outcome === "accepted") {
                        setErrorMsg("App downloading in the background..");
                        setSeverity("info");
                        setOpenError(true);
                    }
                    setPwa(null);
                });
            }
        }
    };

    useEffect(() => {
        const token: string | null = localStorage.getItem("token");
        let userInfo: User = JSON.parse(localStorage.getItem("userInfo") as string);
        if (userInfo && token && !userInfo.admin) {
            setUser(userInfo);
            setIsLogged(true);
        }
        if (isLogged) {
            axios
                .get("/api/auth/user", {
                    headers: {
                        "x-auth-token": `${token}`
                    }
                })
                .then((res) => {
                    if (!userInfo || !token) {
                        setUser(res.data as User);
                    }
                    localStorage.setItem(`userInfo`, JSON.stringify(res.data as User));
                    setIsLogged(true);
                })
                .catch((err) => {
                    if (err) {
                        setIsLogged(false);
                    }
                });
        } else {
            localStorage.removeItem("token");
            localStorage.removeItem("userInfo");
        }
    }, [isLogged]);

    return (
        <MuiThemeProvider theme={isLight ? createMuiTheme(theme.lightTheme) : createMuiTheme(theme.darkTheme)}>
            <CssBaseline />
            <Router>
                <Suspense fallback={<div></div>}>
                    <Route
                        path="/"
                        render={() => (
                            <>
                                <MenuAppBar
                                    isLogged={isLogged}
                                    setIsLogged={setIsLogged}
                                    user={user}
                                    setUser={setUser}
                                    isLight={isLight}
                                    setIsLight={setIsLight}
                                />
                                <SnackAlert severity={severity} errorMsg={errorMsg} setOpenError={setOpenError} openError={openError} />
                            </>
                        )}
                    />
                </Suspense>
                <Suspense fallback={<div></div>}>
                    <Switch>
                        <Route exact path="/" render={() => <Home />} />

                        <Route exact path="/profile/:id" render={() => <Profile />} />

                        <Route exact path="/feed" render={() => <Feed isLogged={isLogged} user={user} />} />

                        <Route
                            exact
                            path="/settings"
                            render={() => (
                                <Settings isLight={isLight} setIsLight={setIsLight} handleClick={handleClick} showBtn={showBtn} />
                            )}
                        />
                        <Route render={() => <NotFound />} />
                    </Switch>
                </Suspense>
            </Router>
        </MuiThemeProvider>
    );
};

export default App;
```



----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2020-07-12 14:16:40

------------

In case someone is having the same problem, the actual problem was that some of the components had other components within them which weren't exported as default and that's why they weren't being lazy-loaded.
So if you're having the same problem, you should check the import tree of the component you're trying to lazy-load and make sure every component in this tree is exported as default.
For more information refer to the [named exports section in the react docs](https://reactjs.org/docs/code-splitting.html#named-exports).
Thanks everyone for your help!


------------
    
    
## Answer \#1

 Vote: 14

Created at 2020-02-16 22:46:20

------------

You are wrapping your entire `Switch` in a single `Suspense`, so all components will be lazily loaded at the same time. You probably only want each to be fetched/loaded when the specific route is rendered the first time.
```
<Switch>
  <Route
    exact
    path="/"
    render={props => (
      <Suspense fallback={<div>Loading...<div>}>
        <Home {...props} />
      </Suspense>
    )}
  />
  <Route
    exact
    path="/profile/:id"
    render={props => (
      <Suspense fallback={<div>Loading...<div>}>
        <Profile {...props} />
      </Suspense>
    )}
  />
  <Route
    exact
    path="/feed"
    render={() => (
      <Suspense fallback={<div>Loading...<div>}>
        <Feed isLogged={isLogged} user={user} {...props} />
      </Suspense>
    )}
  />
  <Route
    exact
    path="/settings"
    render={() => (
      <Suspense fallback={<div>Loading...<div>}>
        <Settings
          isLight={isLight}
          setIsLight={setIsLight}
          handleClick={handleClick}
          showBtn={showBtn}
          {...props}
        />
      </Suspense>
    )}
  />
  <Route
    render={() => <NotFound />}
  />
</Switch>
```

There is a lot of repetition here, so it is practical to factor out the suspense into a HOC.
```
const withSuspense = (WrappedComponent, fallback) => props => (
  <Suspense fallback={fallback}>
    <WrappedComponent {...props} />
  </Suspense>
);
```

You can either decorate each perspective default export, i.e.
```
export default withSuspense(Home, <div>Loading...<div>);
```

App.js
```
...
<Switch>
  <Route exact path="/" render={props => <Home {...props} />} />
```

or decorate them in your App
```
const HomeWithSuspense = withSuspense(Home, <div>Loading...<div>);

...

<Switch>
  <Route
    exact
    path="/"
    render={props => <HomeWithSuspense {...props} />}
  />
  ...
</Switch>
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-07-11 13:12:15

------------

That should work, I would look other problems, like build scripts, or some other piece of code using those same bundles. (e.g. the inheritance thing you mentioned in comments)


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-11-10 09:58:15

------------


```
import React, { Suspense, lazy } from "react";
import { BrowserRouter as Router, Routes, Route } from "react-router-dom";
const Home = lazy(() => import("./components/Home"));
const About = lazy(() => import("./components/About"));

const App = () => (
   <Router>
     <Suspense fallback={<div>Loading...</div>}>
       <Routes>
         <Route exact path='/' element={<Home/>}/>
         <Route exact path='/about' element={<About/>}/>
         </Routes>
     </Suspense>
   </Router>
 );

export default App
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-07-12 16:53:17

------------

There's no need for the whole tree that you're trying to lazy load to have default imports and exports. The component tree with its unique dependencies will be bundled into lazy chunk by default.
For eg.
Component.js
```
import { x, y } from z
.....
export default Component
```

main.js
```
const Component = React.lazy(() => import('Component.js')
```

Here the main.js chunk will not include code any code from z or
any of the code from Component.js and its unique dependencies
[https://webpack.js.org/guides/code-splitting/#dynamic-imports](https://webpack.js.org/guides/code-splitting/#dynamic-imports)
[https://create-react-app.dev/docs/code-splitting/#appjs](https://create-react-app.dev/docs/code-splitting/#appjs)


------------
    
    