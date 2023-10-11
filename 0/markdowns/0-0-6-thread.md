
# Post \#70371723 [Link](https://stackoverflow.com/questions/70371723/)

## ReactJS redirecting to another page on successful statement not working

**Vote**: 2 (631/702) **Views**: 3558 (554/702) 

**Internal ID** \#0-0-6

Created at 2021-12-15 23:54:14

Tags: `reactjs` `react-router`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am trying to create this single-page-application in react, and I want to redirect to another page based on if the username and password inputs are correct. I have tried multiple approaches, such as the <Redirect to .. tag and history.push(), but none of them seem to be getting rendered. If I try alerts or console.log , I can see that the statement was successful, but the redirect code is never rendered.
This is how my CustomerLoginPage render method looks :
```
render() {
    return (
     
        <div class="App-header">
          <NavLink to="/main">
            <button id="backButton">Go Back</button>
          </NavLink>
          <h4>Login with your customer account</h4>
          <br />
          <input id="customerLoginUsername" placeholder=" Username"></input>
          <input id="customerLoginPassword" type="password" placeholder=" Password"></input>
          <br />
          <button id="customerLoginButton" onClick={this.handleSubmit}> 
            Login
          </button>
         
  
          <div className="content"></div>
        </div>
     
    );
  }
}
export default withRouter(CustomerLoginPage);
```

And this is how the handleSubmit() looks like :
```
handleSubmit() {
    
    let usernameInput = document.getElementById('customerLoginUsername');
    let passwordInput = document.getElementById('customerLoginPassword');
  
    if (usernameInput.value !== 'customer' || passwordInput.value !== 'customer') {
      // alert("Wrong username or password!");
    } else {
      this.props.history.replace('/customerhomepage');
    }
  }
```

What could I be missing here? Thanks!

I managed to fix this issue by changing my CustomerLoginPage from a class component to a function component , and then using useHistory to change the page.
After reading the suggestions about using my Router as a HOC, I removed it from all the files except for index.js and switched to a BrowserRouter instead of HashRouter.
This is how CustomerLoginPage looks now :
```
function CustomerLoginPage () {

  const history = useHistory();

  const loginButtonClicked = () =>{ 
    let path = '/customerhomepage'; 
    history.push(path);
  }

  const backButtonClicked = () => {
    let path = '/main'; 
    history.push(path);
  }

  const handleSubmit = () => {
    
    let usernameInput = document.getElementById('customerLoginUsername');
    let passwordInput = document.getElementById('customerLoginPassword');
  
    if (usernameInput.value !== 'customer' || passwordInput.value !== 'customer') {
      alert("Wrong username or password!");
    } else {
      loginButtonClicked();
    }
  }
  
    return (
     
        <div class="App-header">
  
          <button id="backButton" onClick={backButtonClicked}>Go Back</button>
          <h4>Login with your customer account</h4>
          <br />
          <input id="customerLoginUsername" placeholder=" Username"></input>
          <input id="customerLoginPassword" type="password" placeholder=" Password"></input>
          <br />
          <button id="customerLoginButton" onClick={handleSubmit}> 
            Login
          </button>
         
  
          <div className="content"></div>
        </div>
     
    );
  }

export default withRouter(CustomerLoginPage);
```



----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2021-12-16 00:20:13

------------


# Issue



1. You can't return JSX from an asynchronous callback and expect it to be rendered and take effect. The Redirect must be rendered in the return of the component.
2. The reason using the history object wasn't working is because the handleSubmit handler is outside the router providing any routing context to navigate against.



# Solution



1. Use the history object to issue an imperative redirect.
2. Move the HashRouter to wrap App and provide a routing context for the history object. In fact, you should have only one router wrapping your app to provide the routing context, so remove all other extraneous routers you may have in your app.


Example:
index.js
```
import { HashRouter as Router } from 'react-router-dom';

...

ReactDOM.render(
  <React.StrictMode>
    <Router>
      <App/>
    </Router>
  </React.StrictMode>,
  document.getElementById('root')
);
```

CustomerHomePage
```
handleSubmit() {
  const usernameInput = document.getElementById("customerLoginUsername");
  const passwordInput = document.getElementById("customerLoginPassword");

  if (usernameInput.value !== "customer" || passwordInput.value !== "customer") {
    // alert("Wrong username or password!");
  } else {
    this.props.history.replace('/customerhomepage');
  }
}
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-12-16 00:01:13

------------

By the time the user clicks on the button and your `handleSubmit` fires, the React component has long since finished rendering, so returning a redirect JSX from the handler won't really have any effect.
If your app architecture supports it (i.e. this isn't an SPA) consider just doing a plain old `window.location.reload` to send the user to the right place.
If not (and I guess you said this is an SPA), you will have to be clever about how to get the `<Redirect>` to get acted upon in the next render. React state might be a good start here... in your else clause, instead of returning the redirect, set some state variable that your render method could check for, and render the `<Redirect>` instead. (Your component is guaranteed to re-render if you change any of its state.)
In other words, modify your render method to either return what it already does, or a `<Redirect>`, depending on the value of a state variable. You can initialize the state to `null` and then have your `handleSubmit` method alter it to, say, the URL you want to redirect to. Then the flow is: render the login page, user provides bad data, your `handleSubmit` sets state saying so, you re-render now as a `<Redirect>` component.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-12-16 00:17:54

------------

Since you are using `react-router` I suggest you use the history api to push to the next route.
You can use the withRouter HOC to provide the history api to your props.
```
import { withRouter } from "react-router";


handleSubmit() {
  let usernameInput = document.getElementById('customerLoginUsername');
  let passwordInput = document.getElementById('customerLoginPassword');

  if (usernameInput.value !== 'customer' || passwordInput.value !== 'customer') {
    // alert("Wrong username or password!");
  } else {
    this.props.history.push('/customerhomepage')
  }
}

render() {
  return (
    <HashRouter>
      <Switch>
        <Route exact path="/main" component={Main}></Route>
        <Route exact path="/customerhomepage" component={CustomerHomePage}></Route>
      </Switch>
      <div class="App-header">
        <NavLink to="/main">
          <button id="backButton">Go Back</button>
        </NavLink>
        <h4>Login with your customer account</h4>
        <br />
        <input id="customerLoginUsername" placeholder=" Username"></input>
        <input id="customerLoginPassword" type="password" placeholder=" Password"></input>
        <br />
        <button id="customerLoginButton" onClick={this.handleSubmit}>
          Login
        </button>

        <div className="content"></div>
      </div>
    </HashRouter>
  );
}

export default withRouter(CustomerLoginPage);
```


# Edit:


As other have pointed out, you will need to raise the HashRouter component to a higher component, in order for withRouter to work.


------------
    
    