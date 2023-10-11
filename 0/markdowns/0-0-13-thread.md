
# Post \#59813926 [Link](https://stackoverflow.com/questions/59813926/)

## How can I store and update multiple values in React useState?

**Vote**: 33 (185/702) **Views**: 69232 (124/702) 

**Internal ID** \#0-0-13

Created at 2020-01-19 20:03:47

Tags: `javascript` `reactjs` `react-hooks` `use-state`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I've a series of user data elements which I'm collecting inside a React component using hooks.

```
const [mobile, setMobile] = useState('');
const [username, setUsername] = useState('');
const [email, setEmail] = useState('');
const [password, setPassword] = useState('');
const [confirmPassword, setConfirmPassword] = useState('');
```


Each of these are updated as follows.

```
<input type="text"
       className="form-control"
       id="mobile"
       placeholder="Enter a valid mobile number"
       onChange={event => {setMobile(event.target.value)}}/>
```


Is there a more succint way to do this using an object as the variable?


----------
        
## Answer \#0

**Accepted** Vote: 78

Created at 2020-01-19 20:30:45

------------

You should add `name` attributes to input tags. Each name must refer to key in `AllValues` object.

```
const [allValues, setAllValues] = useState({
   mobile: '',
   username: '',
   email: '',
   password: '',
   confirmPassword: ''
});
const changeHandler = e => {
   setAllValues({...allValues, [e.target.name]: e.target.value})
}
return (
   <input type="text"
       className="form-control"
       id="mobile"
       name="mobile"
       placeholder="Enter a valid mobile number"
       onChange={changeHandler}
   />
   // ...
)
```



------------
    
    
## Answer \#1

 Vote: 20

Created at 2021-01-07 08:56:59

------------

The above answer could create issues in some cases, the following should be the right approach.
```
const [allValues, setAllValues] = useState({
   mobile: '',
   username: '',
   email: '',
   password: '',
   confirmPassword: ''
});
const changeHandler = e => {
   setAllValues( prevValues => {
   return { ...prevValues,[e.target.name]: e.target.value}
}
}
```



------------
    
    
## Answer \#2

 Vote: 6

Created at 2021-06-26 08:00:59

------------

> Set initial values
```
const initialValues = {                   // type all the fields you need
name: '',
email: '',
password: ''
};
```

> UseState
```
const [values, setValues] = useState(initialValues);       // set initial state
```

> Handling the changes
```
const handleChange = (e) => {                
  setValues({
    ...values,                                // spreading the unchanged values
    [e.target.name]: e.target.value,          // changing the state of *changed value*
  });
};
```

> Making the Form
```
<form>
  <input type="email"
   id="xyz"
   name="email"                                     // using name, value, onChange for applying changes
   value={email}
   onChange={changeHandler}
   placeholder="Enter your email"
   />
   // other inputs
</form>
```



------------
    
    