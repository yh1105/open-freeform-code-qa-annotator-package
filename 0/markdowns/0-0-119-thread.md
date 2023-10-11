
# Post \#62378796 [Link](https://stackoverflow.com/questions/62378796/)

## Cannot destructure property of object from context

**Vote**: 6 (473/702) **Views**: 38597 (196/702) 

**Internal ID** \#0-0-119

Created at 2020-06-14 22:08:29

Tags: `reactjs` `react-hooks` `react-context` `use-effect` `use-state`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

Re-posting a similar question to my last because of a new issue. I'm trying to use context with hooks to manage authentication in my react app. I'm getting the error `TypeError: Cannot destructure property 'isAuthenticated' of 'Object(...)(...)' as it is undefined.`, yet when I `console.log` the property where it's defined, I see `false` not `undefined`.

I have the context definition and provider in `authContext.js`

```
import React, { useState, useEffect, createContext } from "react";
import axios from "axios";

const AuthContext = createContext();
export { AuthContext };

const AuthContextProvider = (props) => {
  const [isAuthenticated, setIsAuthenticated] = useState(false);

  const setAuth = (boolean) => {
    setIsAuthenticated(boolean);
  };

  const apiOptions = {
    url: "users/is-verified",
    method: "GET",
    headers: {
      token: localStorage.token,
    },
  };

  function isAuth() {
    axios(apiOptions)
      .then((response) => {
        console.log("auth ran");
        const resData = response.data;
        resData === true ? setIsAuthenticated(true) : setIsAuthenticated(false);
      })
      .catch((error) => {
        console.log(error.response);
      });
  }

  useEffect(() => {
    isAuth();
  }, []);
  console.log(isAuthenticated);
  return (
    <AuthContext.Provider
      value={{ isAuthenticated, setIsAuthenticated, setAuth }}
    >
      {props.children}
    </AuthContext.Provider>
  );
};

export default AuthContextProvider;
```


Then I have my routes wrapped in the provider in `app.js`

```
import React from "react";
import {
  Switch,
  Route,
} from "react-router-dom";
import "./App.css";
import Register from "./components/pages/register";
import AuthContextProvider from "./components/context/authContext";
import RegisterRoutes from "./components/routing/registerRoutes";

function App() {

  return (
    <AuthContextProvider>
      <div className="App h-100 ">
        <Switch>
          <Route
            exact
            path="/register"
            render={(props) => (
              <RegisterRoutes {...props} />
            )}
          />
        </Switch>
      </div>
    </AuthContextProvider>
  );
}

export default App;
```


Then I have a `RegisterRoutes` component that returns one of two pages based on the `isAuthenticated` value

```
import React, { useContext } from "react";
import AuthContext from "../context/authContext";
import { Redirect } from "react-router-dom";
import Register from "../pages/register";

function RegisterRoutes(props) {
  const { isAuthenticated, setAuth } = useContext(AuthContext);
  console.log(isAuthenticated);

  return !isAuthenticated ? (
    <Register {...props} setAuth={setAuth} />
  ) : (
    <Redirect to="/login" />
  );
}

export default RegisterRoutes;
```



----------
        
## Answer \#0

**Accepted** Vote: 10

Created at 2020-06-14 22:22:03

------------

Your default export is AuthContextProvider (a component), not AuthContext (a context object): that won't work as you expect. Furthermore you are trying to export the context object inside another object:

```
// WRONG: export context inside {}
const AuthContext = createContext();
export { AuthContext };
```




Instead export the context variable normally (not as default):

```
// Export the variable
export const AuthContext = createContext();

// This works
import { AuthContext } from "../context/authContext";
```




A better practice is to keep the context in a separate file and export it as default:

```
// File AuthContext.js

import React, { createContext } from "react";

const AuthContext = createContext();

export default AuthContext;

// Import it in another file
import AuthContext from "./AuthContext.js";
```



------------
    
    
## Answer \#1

 Vote: 5

Created at 2021-01-26 03:44:54

------------

I solved this by doing a simple thing. In index, react-app, just put my provider involving my App.
```
ReactDOM.render(
  <React.StrictMode>
    <AuthProvider>
      <App />
    </AuthProvider>
  </React.StrictMode>,
  document.getElementById("root")
);
```

I have the same problem when I try set logging in localStorage.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-01-27 04:05:32

------------

For people using `gatsby` and context API in the page component:
you need to wrap root element with context provider in `gatsby-browser.js` and in `gatsby-ssr.js`.
Example:
```
import React from 'react';

import { CustomProvider } from './src/context/CustomProvider';

export const wrapRootElement = ({ element }) => {
  return <CustomProvider>{element}</CustomProvider>;
};
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-10-07 11:40:07

------------

Try in your App.js
define the value = {{state, dispatch}} in provider
```
<UserContext.Provider value ={{state, dispatch}}>
        <Routing/>
      </UserContext.Provider>
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-12-07 17:28:54

------------

you must put this {} for wrape the context during the send in other file
```
import {AuthContext} from "../context/authContext";

import { Redirect } from "react-router-dom";

import Register from "../pages/register";

function RegisterRoutes(props) {

  const { isAuthenticated, setAuth } = useContext(AuthContext);
```



------------
    
    
## Answer \#5

 Vote: -1

Created at 2022-01-01 02:16:31

------------

[](https://i.stack.imgur.com/SZNxT.png)
This is the way I solved it, wrapping all the component in app.js file with `authProvider`.


------------
    
    