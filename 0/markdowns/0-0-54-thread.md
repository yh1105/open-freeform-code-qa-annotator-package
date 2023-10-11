
# Post \#57302715 [Link](https://stackoverflow.com/questions/57302715/)

## how to get input field value on button click in react?

**Vote**: 9 (409/702) **Views**: 36417 (202/702) 

**Internal ID** \#0-0-54

Created at 2019-08-01 06:06:49

Tags: `javascript` `reactjs`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

Could you please tell me how to get input field value on button click in react , I am using react hooks .I want to get `first name` and `lastname` value on button click. I already pass `name` attribute in my function component.

Here is my [code](https://stackblitz.com/edit/react-2vyn2k)

```
import React, { Component, useState } from 'react';
import { render } from 'react-dom';

export default function InputField({name,label}) {
  const [state, setState] = useState('')
  return (
    <div>
     <label>{label}</label>
      <input type="text" 
      value={state} 
      name={name}
      onChange={(e) => setState(e.target.value)} />

      {state}
    </div>
  );

}
```



----------
        
## Answer \#0

**Accepted** Vote: 11

Created at 2019-08-01 07:15:52

------------

> Use `<form>` tag with `useRef` hook

Wrap your `<InputField>` tags with an html `<form>` tag and put a react `ref` on the later. Like this: 

```
import React, { Component, useRef } from 'react'
import { render } from 'react-dom'

import InputField from './inputfield'

import './style.css'

function App () {
  const nameForm = useRef(null)

  const handleClickEvent = () => {
     const form = nameForm.current
     alert(`${form['firstname'].value} ${form['lastname'].value}`)
  }

  return (
    <div>
      <form ref={nameForm}>
       <InputField label={'first name'} name={'firstname'}/>
       <InputField label={'last name'} name={'lastname'}/>
      </form>
      <button onClick={handleClickEvent}>gett value</button>
    </div>
  )
}

render(<App />, document.getElementById('root'))
```


 [https://stackblitz.com/edit/react-shtnxj](https://stackblitz.com/edit/react-shtnxj)


------------
    
    
## Answer \#1

 Vote: 5

Created at 2020-11-21 12:44:22

------------

`useRef`
With `useRef` it's pretty simple. Just add `ref` name and then submit.
```
const email = useRef(null);

function submitForm(e){
 e.preventDefault();
 
 console.log(email.current.value);
}

return (
 <div>
  <form onSubmit={submitForm}>
   <input type="text" ref={email} />
   <button>Submit</button>
  </form>
 </div>
)
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2019-08-01 06:35:06

------------

You could always lift up the state in parent component.
[codeSandbox link](https://codesandbox.io/s/tender-butterfly-0xugf)

Parent Component

```
import React from "react";
import ReactDOM from "react-dom";
import ChildComponent from "./Child";

const { useState } = React;

function App() {
  const [first_name, setFirstName] = useState("");
  const [last_name, setLastName] = useState("");
  const handleFirstNameChange = ({ target }) => {
    setFirstName(target.value);
  };
  const handleLastNameChange = ({ target }) => {
    setLastName(target.value);
  };
  const handleClick = () => {
    console.log(first_name);
    console.log(last_name);
  };
  return (
    <div className="App">
      <ChildComponent
        label="first name"
        onChange={handleFirstNameChange}
        value={first_name}
      />
      <ChildComponent
        label="last name"
        onChange={handleLastNameChange}
        value={last_name}
      />
      <button onClick={handleClick}>Click me</button>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```


Child Component

```
import React from "react";

const ChildComponent = ({ label, onChange, value, name }) => {
  return (
    <div>
      <label>{label}</label>
      <input type="text" value={value} name={name} onChange={onChange} />
    </div>
  );
};

export default ChildComponent;
```


You could always combine onChange handler for first name and last name.

Hope that helps!!!


------------
    
    
## Answer \#3

 Vote: 1

Created at 2019-08-01 06:39:48

------------

A good solution is to move the state from InputField component into index: 

```
const [F_name, setF_name] = useState('')
    const [L_name, setL_name] = useState('')
```


now you should pass state value and event handler to InputField to change the state when input is changed: 

```
<InputField label={'first name'} name={'firstname'} value={F_name} changed={(name) => setF_name(name)}/>
```


In Your InputField field: edit it to be like: 

```
<input type="text" 
      value={value} 
      name={name}
      onChange={(e) => changed(e.target.value)} />
```


[See Working Demo Here](https://stackblitz.com/edit/react-ka5s74?file=inputfield.js)


------------
    
    
## Answer \#4

 Vote: 1

Created at 2021-02-09 13:17:34

------------

```
import React, { useRef } from 'react'
    const ViewDetail = () => {
      const textFirstName = useRef(null)   
      const onChange = e => {
        console.log(textFirstName.current.state.value) 
      } 
    
    return <Input maxLength={30} ref={textFirstName} placeholder="Nombre" onChange=onChange} />
  }
```




------------
    
    
## Answer \#5

 Vote: 0

Created at 2019-08-01 06:35:08

------------

I can think of these approaches - 

- 

App.js

```
const [user, setUser] = useState('');

return (
 <Inputfield setValue={setUser} value={user} />
);
```


InputField.js

```
<input value={props.value}  onChange={(e) => setValue(e.target.value)} />
```



---



- `ref`


---



- `Context`


---



Hope this helps!

Do let me know if you need more info on any of the option. Thanks!


------------
    
    
## Answer \#6

 Vote: 0

Created at 2019-08-01 06:53:57

------------

You should do the react hooks work on your index and pass the value and the onChange function to your InputField component.

```
//index page
import React, { Component, useState } from 'react';
import { render } from 'react-dom';
import InputField from './inputfield';
import './style.css';

function App() {
  const [firstname, setFirstName] = useState('');
  const [lastname, setLastName] = useState('');
  const handleClickEvent = ()=>{
    setFirstName('Will');
    setLastName('smith');
  }

  return (
    <div>
      <InputField
        label={'first name'}
        name={'firstname'}
        value={firstname}
        onChange={setFirstName}
      />
      <InputField
        label={'last name'}
        name={'lastname'}
        value={lastname}
        onChange={setLastName}
      />
      <button 
        onClick={handleClickEvent}

      >Get value</button>
    </div>
  );

}

render(<App />, document.getElementById('root'));
```


```
// input field

import React, { Component, useState } from 'react';
import { render } from 'react-dom';

export default function InputField({name,label, value, onChange}) {
  return (
    <div>
     <label>{label}</label>
      <input type="text" 
      value={value} 
      name={name}
      onChange={(e) => onChange(e.target.value)} />

      {value}
    </div>
  );

}
```



------------
    
    
## Answer \#7

 Vote: 0

Created at 2019-08-01 06:57:37

------------

While keeping the majority of your structure the same, I think the simplest and most React solution is to use `forwardRef()` which in a nut-shell let's us communicate between then parent-component and child-components.

See working [sandbox](https://codesandbox.io/s/pensive-roentgen-uzrrn).


## App.js



```
import React, { useRef } from "react";
import InputField from "./InputField";
import ReactDOM from "react-dom";

function App() {
  const handleClickEvent = () => {
    if (firstName.current && lastName.current) {
      console.log(`firstName: ${firstName.current.value}`);
      console.log(`lastName: ${lastName.current.value}`);
    }
  };

  const firstName = useRef(null);
  const lastName = useRef(null);

  return (
    <div>
      <InputField ref={firstName} label={"first name"} name={"firstname"} />
      <InputField ref={lastName} label={"last name"} name={"lastname"} />
      <button onClick={handleClickEvent}>Get value</button>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```



## InputField.js



```
import React, { useState } from "react";

const InputField = React.forwardRef((props, ref) => {
  const [state, setState] = useState("");
  return (
    <div>
      <label>{props.label}</label>
      <input
        ref={ref}
        type="text"
        value={state}
        name={props.name}
        onChange={e => setState(e.target.value)}
      />

      {state}
    </div>
  );
});

export default InputField;
```


Notice that with this structure, you are not required to pass in any state updating function as `props` to the `InputField` component. The value that you enter into each input will be strictly maintained by the individual component. It is independent from the `Parent`, and therefore makes it much more reusable.

The refs we created allow us to tap into specific elements of the `InputField` so we extract the desired values. In this case, we can get first-name and last-name through the `handleClickEvent` function.


------------
    
    
## Answer \#8

 Vote: 0

Created at 2019-09-13 07:56:10

------------

you can achieve this doing the following:

```
import React, { Component, useState } from 'react';
import { render } from 'react-dom';

export default function InputField({name,label}) {
  const [state, setState] = useState('');
  const handleChange = e => {
    setState(e.target.value);
  };

  return (
    <div>
     <label>{label}</label>
      <input
        type="text" 
        value={state} 
        name={name}
        onChange={handleChange}
      />
      {state}
    </div>
  );
}
```


Hopes this helps.


------------
    
    
## Answer \#9

 Vote: -1

Created at 2019-08-01 06:25:26

------------

well one simple(but not necessarily recommended) way is to provide an id or a ref like this in index.js

```
<InputField label={'first name'} name={'firstname'} id={"ip1"}/>
<InputField label={'last name'} name={'lastname'} id={"ip2"}/>
```


and in your inputfield.js pass the id props to the input fields like this

```
<input type="text" 
      value={state} 
      name={name}
      onChange={(e) => setState(e.target.value)} 
      id= {id}/>
```


Now you can call them in the onClick of the button like this in index.js

```
const handleClickEvent = ()=>{
  alert(document.getElementById("ip1").value);
  }
```


The second, more preferable way is to set the state variable in index.js

```
function App() {
  const [stateIp1, setStateIp1] = useState('');
  const [stateIp2, setStateIp2] = useState('');
  const handleClickEvent = ()=>{
  alert(stateIp1);
  }
  return (
    <div>
      <InputField label={'first name'} state={stateIp1} setState={setStateIp1} name={'firstname'} id={"ip1"}/>
        <InputField label={'last name'}state={stateIp2} setState={setStateIp2} name={'lastname'} id={"ip2"}/>
        <button 
          onClick={handleClickEvent}

        >Get value</button>
    </div>
  );

}
```


Now your inputfield.js becomes

```
export default function InputField({name,label,id,setState,state}) {

  return (
    <div>
     <label>{label}</label>
      <input type="text" 
      value={state} 
      name={name}
      onChange={(e) => setState(e.target.value)} id= {id}/>
    </div>
  );
```



------------
    
    