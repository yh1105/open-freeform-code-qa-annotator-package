
# Post \#54895883 [Link](https://stackoverflow.com/questions/54895883/)

## Reset to Initial State with React Hooks

**Vote**: 108 (55/702) **Views**: 216457 (34/702) 

**Internal ID** \#0-0-18

Created at 2019-02-26 23:39:09

Tags: `reactjs` `react-hooks`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm currently working on a signup form and the following is a snippet of my code:

```
const Signup = () => {
    const [username, setUsername] = useState('')
    const [email, setEmail] = useState('')
    const [password, setPassword] = useState('')
    const [passwordConfirmation, setPasswordConfirmation] = useState('')

    const clearState = () => {
        setUsername('')
        setEmail('')
        setPassword('')
        setPasswordConfirmation('')
    }

    const handleSubmit = signupUser => e => {
        e.preventDefault()
        signupUser().then(data => {
            console.log(data)
            clearState() // <-----------
        })
    }

    return <JSX />
}

export default Signup
```


Each piece of state is used for a controlled input for the form.

Essentially what I want to do is after the user has successfully signed up, I want the state to go back to the initial state with the fields cleared.

It's quite imperative to manually set each piece of state back to empty strings in`clearState` I was wondering if there is a method or function that comes with React that resets the state back to its initial values?


----------
        
## Answer \#0

**Accepted** Vote: 118

Created at 2019-02-26 23:52:46

------------

There is no built-in way to set the state to its initial value, sadly.

Your code looks good, but if you want to decrease the functions needed you can put your entire form state in a single state variable object and reset to the initial object.



```
const { useState } = React;

function signupUser() {
  return new Promise(resolve => {
    setTimeout(resolve, 1000);
  });
}

const initialState = {
  username: "",
  email: "",
  password: "",
  passwordConfirmation: ""
};

const Signup = () => {
  const [
    { username, email, password, passwordConfirmation },
    setState
  ] = useState(initialState);

  const clearState = () => {
    setState({ ...initialState });
  };

  const onChange = e => {
    const { name, value } = e.target;
    setState(prevState => ({ ...prevState, [name]: value }));
  };

  const handleSubmit = e => {
    e.preventDefault();
    signupUser().then(clearState);
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label>
          Username:
          <input value={username} name="username" onChange={onChange} />
        </label>
      </div>
      <div>
        <label>
          Email:
          <input value={email} name="email" onChange={onChange} />
        </label>
      </div>
      <div>
        <label>
          Password:
          <input
            value={password}
            name="password"
            type="password"
            onChange={onChange}
          />
        </label>
      </div>
      <div>
        <label>
          Confirm Password:
          <input
            value={passwordConfirmation}
            name="passwordConfirmation"
            type="password"
            onChange={onChange}
          />
        </label>
      </div>
      <button>Submit</button>
    </form>
  );
};

ReactDOM.render(<Signup />, document.getElementById("root"));
```
```
<script src="https://unpkg.com/react@16/umd/react.development.js"></script>
<script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>

<div id="root"></div>
```




------------
    
    
## Answer \#1

 Vote: 64

Created at 2019-09-16 10:52:21

------------

I think the voted answer is still correct, but recently React released the new built-in `useReducer` which, in their own words, is

> handy for resetting the state later in response to an action

[https://reactjs.org/docs/hooks-reference.html#usereducer](https://reactjs.org/docs/hooks-reference.html#usereducer)

Also it states that it's usually preferable useReducer when you have complex state logic that involves multiple sub-values or when the next state depends on the previous one.

Using the same sample on the voted answer, you could use useReducer like this:


## Javascript



```
import React, { useReducer } from "react";

const initialState = {
    username: "",
    email: "",
    password: "",
    passwordConfirmation: "",
};

const reducer = (state, action) => {
    if (action.type === "reset") {
        return initialState;
    }

    const result = { ...state };
    result[action.type] = action.value;
    return result;
};

const Signup = () => {
    const [state, dispatch] = useReducer(reducer, initialState);
    const { username, email, password, passwordConfirmation } = state;

    const handleSubmit = e => {
        e.preventDefault();

        /* fetch api */

        /* clear state */
        dispatch({ type: "reset" });
    };

    const onChange = e => {
        const { name, value } = e.target;
        dispatch({ type: name, value });
    };

    return (
        <form onSubmit={handleSubmit}>
            <div>
                <label>
                    Username:
                    <input value={username} name="username" onChange={onChange} />
                </label>
            </div>
            <div>
                <label>
                    Email:
                    <input value={email} name="email" onChange={onChange} />
                </label>
            </div>
            <div>
                <label>
                    Password:
                    <input
                        value={password}
                        name="password"
                        type="password"
                        onChange={onChange}
                    />
                </label>
            </div>
            <div>
                <label>
                    Confirm Password:
                    <input
                        value={passwordConfirmation}
                        name="passwordConfirmation"
                        type="password"
                        onChange={onChange}
                    />
                </label>
            </div>
            <button>Submit</button>
        </form>
    );
};

export default Signup;
```



## Typescript



```
import React, { FC, Reducer, useReducer } from "react";

interface IState {
    email: string;
    password: string;
    passwordConfirmation: string;
    username: string;
}

interface IAction {
    type: string;
    value?: string;
}

const initialState: IState = {
    email: "",
    password: "",
    passwordConfirmation: "",
    username: "",
};

const reducer = (state: IState, action: IAction) => {
    if (action.type === "reset") {
        return initialState;
    }

    const result: IState = { ...state };
    result[action.type] = action.value;
    return result;
};

export const Signup: FC = props => {
    const [state, dispatch] = useReducer<Reducer<IState, IAction>, IState>(reducer, initialState, () => initialState);
    const { username, email, password, passwordConfirmation } = state;

    const handleSubmit = (e: React.FormEvent) => {
        e.preventDefault();

        /* fetch api */

        /* clear state */
        dispatch({ type: "reset" });
    };

    const onChange = (e: React.ChangeEvent<HTMLInputElement>) => {
        const { name, value } = e.target;
        dispatch({ type: name, value });
    };

    return (
        <form onSubmit={handleSubmit}>
            <div>
                <label>
                    Username:
                    <input value={username} name="username" onChange={onChange} />
                </label>
            </div>
            <div>
                <label>
                    Email:
                    <input value={email} name="email" onChange={onChange} />
                </label>
            </div>
            <div>
                <label>
                    Password:
                    <input
                        value={password}
                        name="password"
                        type="password"
                        onChange={onChange}
                    />
                </label>
            </div>
            <div>
                <label>
                    Confirm Password:
                    <input
                        value={passwordConfirmation}
                        name="passwordConfirmation"
                        type="password"
                        onChange={onChange}
                    />
                </label>
            </div>
            <button>Submit</button>
        </form>
    );
};
```


Notice that I created this `reducer` function const to be as generic as possible, but you can completely change it and test different action types (other than simply state property names) and perform complex calculations before returning the state modified. There are some examples in the link provided above.


------------
    
    
## Answer \#2

 Vote: 51

Created at 2020-11-07 10:32:34

------------


# Short answer


This has a very simple solution. You can change the key prop where the rendering component.
e.g when we have a component for editing we can pass a different key to clear previous states.
```
return <Component key={<different key>} />
```



------------
    
    
## Answer \#3

 Vote: 7

Created at 2020-05-15 17:25:25

------------

If you want a quick n' dirty method you could try just changing the component's key which will cause React to unmount your old component instance and mount a fresh one.

I am using Lodash here to generate a unique throwaway ID but you could also probably get away with `Date.now()` or similar, assuming the time resolution needed is above 1 millisecond.

I am passing the key a second time as `debugKey` to make it easier to see what's going on but this is not neccessary.

```
const StatefulComponent = ({ doReset, debugKey }) => {
  const [counter, setCounter] = React.useState(0);
  const increment = () => setCounter(prev => prev + 1); 
  return (
    <React.Fragment>
      <p>{`Counter: ${counter}`}</p>
      <p>{`key=${debugKey}`}</p>
      <button onClick={increment}>Increment counter</button>
      <button onClick={doReset}>Reset component</button>
    </React.Fragment>
  );
};

const generateUniqueKey = () => `child_${_.uniqueId()}`;

const App = () => {
  const [childKey, setChildKey] = React.useState(generateUniqueKey());
  const doReset = () => setChildKey(generateUniqueKey());
  return (
    <div className="App">
      <StatefulComponent key={childKey} debugKey={childKey} doReset={doReset} />
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  rootElement
);
```
```
<script src="https://cdn.jsdelivr.net/npm/lodash@4.17.15/lodash.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/react/16.13.1/umd/react.production.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/react-dom/16.13.1/umd/react-dom.production.min.js"></script>


<div id="root"></div>
```




------------
    
    
## Answer \#4

 Vote: 3

Created at 2020-01-23 10:55:06

------------

You could have used useRef in hooks something like this 

```
const myForm = useRef(null)

 const submit = () => {

   myForm.current.reset(); // will reset the entire form :)

   }

  <form ref={myForm} onSubmit={submit}>

   <input type="text" name="name" placeholder="John Doe">

     <input type="email" name="name" placeholder="usman@gmail.com">

     <button type="submit">Submit</button>

 </form>
```



------------
    
    
## Answer \#5

 Vote: 2

Created at 2019-02-26 23:47:49

------------

You could use one state variable as described in the FAQ here:  [https://reactjs.org/docs/hooks-faq.html#should-i-use-one-or-many-state-variables](https://reactjs.org/docs/hooks-faq.html#should-i-use-one-or-many-state-variables)

It depends on your use case of course.

Rekeying the component from the parent container would also reset it automatically of course.


------------
    
    
## Answer \#6

 Vote: 2

Created at 2019-02-26 23:53:28

------------

Alongside the other answers, I'd recommend picking up a helper library [like this](https://github.com/wsmd/react-use-form-state), or making your own abstraction on top of hooks, if this is something you'll be doing often.

`useState` and friends are really just low-level primitives for you, the user, to build more useful hooks on top of it. I have projects where raw `useState` calls are actually fairly uncommon.


------------
    
    
## Answer \#7

 Vote: 2

Created at 2021-02-25 12:51:38

------------

I just wrote a custom hook that returns the actual hooks, along with a `resetState` function.
Usage:
```
const [{
    foo: [foo, setFoo],
    bar: [bar, setBar],
  },
  resetState,
] = useStateWithReset({
  foo: null,
  bar: [],
})

// - OR -

const [
    [foo, setFoo],
    [bar, setBar],
  ],
  resetState,
] = useStateWithReset([
  null,
  [],
])
```

The latter is less readable but the former duplicates the keys, so there isn't a perfect solution.
The code:
```
const useStateWithReset = initialState => {
  const hooksArray = Object.fromEntries(
    Object.entries(initialState).map(([k, v]) => {
      return [k, useState(v)]
    })
  );
  const resetState = () =>
    Object.entries(initialState).map(
      ([k, v]) => hooksArray[k][1](v)
    );
  return [hooksArray, resetState];
};
```



------------
    
    
## Answer \#8

 Vote: 1

Created at 2019-06-12 13:22:41

------------

I had a similar use case. Completelty unrelated from a Login, Signup mechanism but I changed it to be related to your use case.

An easy way to solve this is with a parent component in my opinion.

```
const initUser = {
  name: '',
  email: '',
  password: '',
  passwordConfirmation: ''      
}

const LoginManager = () => {
  const [user, setUser] = useState(initUser)

  return <Signup user={user} resetUser={setUser} />
}

const Signup = ({user, resetUser}) => {
    const [username, setUsername] = useState(user.name)
    const [email, setEmail] = useState(user.email)
    const [password, setPassword] = useState(user.password)
    const [passwordConfirmation, setPasswordConfirmation] = useState(user.passwordConfirmation)


    const handleSubmit = signupUser => e => {
        e.preventDefault()
        signupUser().then(data => {
            console.log(data)
            resetUser(initUser) // <-----------
        })
    }

    return <JSX />
}

export default Signup
```



------------
    
    
## Answer \#9

 Vote: 1

Created at 2022-02-17 17:51:02

------------

One way to achieve this "reset states to initial" is by using the [use-state-with-deps](https://github.com/peterjuras/use-state-with-deps) package.
Example:
```
import {useStateWithDeps} from "use-state-with-deps";

const Signup = () => {
    const [generation, setGeneration] = useState(0);

    const [username, setUsername] = useStateWithDeps("", [generation])
    const [email, setEmail] = useStateWithDeps("", [generation])
    const [password, setPassword] = useStateWithDeps("", [generation])
    const [passwordConfirmation, setPasswordConfirmation] = useStateWithDeps("", [generation])

    const clearState = () => {
        setGeneration(generation + 1);
    }

    const handleSubmit = signupUser => e => {
        e.preventDefault()
        signupUser().then(data => {
            console.log(data)
            clearState()
        })
    }

    return <JSX />
}

export default Signup
```

If you don't want to pull in a new dependency, you can find other solutions in [this thread](https://github.com/facebook/react/issues/14738), which are short enough to just include directly in your project (eg. in a "utils" file). For example, [this solution](https://github.com/facebook/react/issues/14738#issuecomment-771292736) is only 20 lines long.


------------
    
    
## Answer \#10

 Vote: 0

Created at 2019-02-26 23:46:36

------------

As I know (by reading react docs) - there is no way to do so yet.


------------
    
    
## Answer \#11

 Vote: 0

Created at 2022-01-12 09:06:53

------------

You can 'wrap' your useState in another use[Whatever name you want] and include a reset function - i.e. like a custom hook as suggested by Augustin in his answer.
Taking the example of an input form, as there is a good real example you can use and view the source of as noted below, you would use the custom hook similar to this:
```
function ContactForm(props) {
  const [state, handleSubmit, reset] = useForm("contactForm");

  const clearForm = e => {
    e.preventDefault();
    reset();  // <---- the extra reset function
    // Any other code you want like hiding 
    // or removing the form div from the 
    // DOM etc.
  }

  if (state.succeeded) {
      return (
        <React.Fragment>
          <p>Thanks fro your input!</p>
          <button className="default-button" onClick={clearForm}>Ok</button>
        </React.Fragment>
      );
  }
  return (
      <form onSubmit={handleSubmit}> // <-- the standard setSate type function
      <label htmlFor="email" className="form-element">
        Email Address
      </label>
      <input
        id="email"
        type="email"
        name="email"
        className="form-element"
      />
      // etc - Your form code...
      <button className="default-button" type="submit" disabled={state.submitting}>
        Submit
      </button>
    </form>
  );
}
```

You can see this in action in the fomrspree git respoitory react examples (at the time of writing) - the function is defined in the useForm source and there is an example of its use in 'useForm.test.js':
- [https://github.com/formspree/formspree-react](https://github.com/formspree/formspree-react)


------------
    
    
## Answer \#12

 Vote: 0

Created at 2023-01-28 14:31:41

------------

There is a lot of hooks on GitHub and NPM, here's some of them:
- [https://ahooks.js.org/hooks/use-reset-state](https://ahooks.js.org/hooks/use-reset-state)- [https://github.com/kitze/react-hanger#usesetstate](https://github.com/kitze/react-hanger#usesetstate)- [https://github.com/beizhedenglong/react-hooks-lib#usefieldinitial](https://github.com/beizhedenglong/react-hooks-lib#usefieldinitial)
I just listed a lot of hooks collections and still cannot find one with "is changed" flag.


------------
    
    
## Answer \#13

 Vote: -1

Created at 2020-01-08 09:31:16

------------

This is how you can reset  in  after form submission. 

You can define multiple input values in same `useState` like , , ...

`const [state, setState] = React.useState({ firstName: "", lastName: "" });`

Sample code.

```
export default function App() {
  const [state, setState] = React.useState({ firstName: "", lastName: "" });
  const handleSubmit = e => {
    e.preventDefault();
    setState({firstName:'',lastName:''})
  };
  const handleChange = e => {
    const { name, value } = e.target;
    setState({ ...state, [name]: value });
  };
  console.log(state)
  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        name="firstName"
        placeholder="Enter first name"
        value={state.firstName}
        onChange={handleChange}
      />
      <input
        type="text"
        name="lastName"
        placeholder="Enter last name"
        value={state.lastName}
        onChange={handleChange}
      />
      <input type="submit" value="Submit" />
    </form>
  );
}
```


> If you want multiple input to define in object instead of declaring seperately.


------------
    
    
## Answer \#14

 Vote: -3

Created at 2022-05-08 14:22:52

------------

```
const handleSubmit = e => {
    e.preventDefault();
    reset();
}
```



------------
    
    