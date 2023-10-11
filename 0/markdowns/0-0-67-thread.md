
# Post \#40676343 [Link](https://stackoverflow.com/questions/40676343/)

## Typescript input onchange event.target.value

**Vote**: 432 (2/702) **Views**: 690568 (3/702) 

**Internal ID** \#0-0-67

Created at 2016-11-18 11:46:02

Tags: `reactjs` `typescript` `types` `typescript-typings`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

In my react and typescript app, I use:
```
onChange={(e) => data.motto = (e.target as any).value}
```

How do I correctly define the typings for the class, so I wouldn't have to hack my way around the type system with `any`?
```
export interface InputProps extends React.HTMLProps<Input> {
...

}

export class Input extends React.Component<InputProps, {}> {
}
```

If I put `target: { value: string };` I get :
```
ERROR in [default] /react-onsenui.d.ts:87:18
Interface 'InputProps' incorrectly extends interface 'HTMLProps<Input>'.
  Types of property 'target' are incompatible.
    Type '{ value: string; }' is not assignable to type 'string'.
```



----------
        
## Answer \#0

**Accepted** Vote: 802

Created at 2017-03-07 10:24:56

------------

Generally event handlers should use `e.currentTarget.value`, e.g.:
```
const onChange = (e: React.FormEvent<HTMLInputElement>) => {
  const newValue = e.currentTarget.value;
}
```

You can read why it so here ([Revert "Make SyntheticEvent.target generic, not SyntheticEvent.currentTarget."](https://github.com/DefinitelyTyped/DefinitelyTyped/pull/12239)).
UPD: As mentioned by @roger-gusmao `ChangeEvent` more suitable for typing form events.
```
const onChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  const newValue = e.target.value;
}
```



------------
    
    
## Answer \#1

 Vote: 223

Created at 2018-04-13 15:08:06

------------

the correct way to use in TypeScript is

```
handleChange(e: React.ChangeEvent<HTMLInputElement>) {
    // No longer need to cast to any - hooray for react!
    this.setState({temperature: e.target.value});
  }

  render() {
        ...
        <input value={temperature} onChange={this.handleChange} />
        ...
    );
  }
```


Follow the complete class, it's better to understand:

```
import * as React from "react";

const scaleNames = {
  c: 'Celsius',
  f: 'Fahrenheit'
};


interface TemperatureState {
   temperature: string;
}

interface TemperatureProps {
   scale: string;

}

class TemperatureInput extends React.Component<TemperatureProps, TemperatureState> {
  constructor(props: TemperatureProps) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = {temperature: ''};
  }

  //  handleChange(e: { target: { value: string; }; }) {
  //    this.setState({temperature: e.target.value});  
  //  }


  handleChange(e: React.ChangeEvent<HTMLInputElement>) {
    // No longer need to cast to any - hooray for react!
    this.setState({temperature: e.target.value});
  }

  render() {
    const temperature = this.state.temperature;
    const scale = this.props.scale;
    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames[scale]}:</legend>
        <input value={temperature} onChange={this.handleChange} />
      </fieldset>
    );
  }
}

export default TemperatureInput;
```



------------
    
    
## Answer \#2

 Vote: 87

Created at 2018-02-09 06:44:43

------------

You can do the following:
```
import { ChangeEvent } from 'react';

const onChange = (e: ChangeEvent<HTMLInputElement>)=> {
   const newValue = e.target.value;
}
```



------------
    
    
## Answer \#3

 Vote: 23

Created at 2022-06-23 05:43:12

------------

`ChangeEvent<HTMLInputElement>` is the type for change event in typescript. This is how it is done-
```
import { ChangeEvent } from 'react';

const handleInputChange = (event: ChangeEvent<HTMLInputElement>) => {
    setValue(event.target.value);
};
```



------------
    
    
## Answer \#4

 Vote: 22

Created at 2021-11-24 11:23:41

------------

we can also use the onChange event fire-up with defined types(in functional component) like as follows:
```
const handleChange = (
    e: React.ChangeEvent<HTMLTextAreaElement | HTMLInputElement>
  ) => {
        const name = e.target.name;
        const value = e.target.value;
};
```



------------
    
    
## Answer \#5

 Vote: 18

Created at 2017-02-08 04:16:12

------------

`as HTMLInputElement` works for me


------------
    
    
## Answer \#6

 Vote: 12

Created at 2016-11-29 14:24:48

------------

The `target` you tried to add in `InputProps` is not the same `target` you wanted which is in `React.FormEvent`

So, the solution I could come up with was, extending the event related types to add your target type, as:

```
interface MyEventTarget extends EventTarget {
    value: string
}

interface MyFormEvent<T> extends React.FormEvent<T> {
    target: MyEventTarget
}

interface InputProps extends React.HTMLProps<Input> {
    onChange?: React.EventHandler<MyFormEvent<Input>>;
}
```


Once you have those classes, you can use your input component as

```
<Input onChange={e => alert(e.target.value)} />
```


without compile errors. In fact, you can also use the first two interfaces above for your other components.


------------
    
    
## Answer \#7

 Vote: 12

Created at 2020-12-31 16:50:41

------------

I use something like this:
```
import { ChangeEvent, useState } from 'react';


export const InputChange = () => {
  const [state, setState] = useState({ value: '' });

  const handleChange = (event: ChangeEvent<{ value: string }>) => {
    setState({ value: event?.currentTarget?.value });
  }
  return (
    <div>
      <input onChange={handleChange} />
      <p>{state?.value}</p>
    </div>
  );
}
```



------------
    
    
## Answer \#8

 Vote: 9

Created at 2020-07-19 03:27:36

------------

When using Child Component We check type like this.
Parent Component:
```
export default () => {

  const onChangeHandler = ((e: React.ChangeEvent<HTMLInputElement>): void => {
    console.log(e.currentTarget.value)
  }

  return (
    <div>
      <Input onChange={onChangeHandler} />
    </div>
  );
}
```

Child Component:
```
type Props = {
  onChange: (e: React.ChangeEvent<HTMLInputElement>) => void
}

export Input:React.FC<Props> ({onChange}) => (
  <input type="tex" onChange={onChange} />
)
```



------------
    
    
## Answer \#9

 Vote: 9

Created at 2020-07-24 10:31:01

------------

An alternative that has not been mentioned yet is to type the onChange function instead of the props that it receives. Using React.ChangeEventHandler:
```
const stateChange: React.ChangeEventHandler<HTMLInputElement> = (event) => {
    console.log(event.target.value);
};
```



------------
    
    
## Answer \#10

 Vote: 6

Created at 2019-02-17 15:30:46

------------

Here is a way with ES6 object destructuring, tested with TS 3.3.
This example is for a text input.

```
name: string = '';

private updateName({ target }: { target: HTMLInputElement }) {
    this.name = target.value;
}
```



------------
    
    
## Answer \#11

 Vote: 5

Created at 2022-04-26 03:32:42

------------

This works for me also it is framework agnostic.
```
const handler = (evt: Event) => {
  console.log((evt.target as HTMLInputElement).value))
}
```



------------
    
    
## Answer \#12

 Vote: 3

Created at 2019-09-27 10:17:49

------------

This is when you're working with a `FileList` Object:

```
onChange={(event: React.ChangeEvent<HTMLInputElement>): void => {
  const fileListObj: FileList | null = event.target.files;
  if (Object.keys(fileListObj as Object).length > 3) {
    alert('Only three images pleaseeeee :)');
  } else {
    // Do something
  }

  return;
}}
```



------------
    
    
## Answer \#13

 Vote: 3

Created at 2020-07-26 16:10:45

------------

Thanks [@haind](https://stackoverflow.com/users/2284020/haind)
Yes `HTMLInputElement` worked for input field
```
//Example
var elem = e.currentTarget as HTMLInputElement;
elem.setAttribute('my-attribute','my value');
elem.value='5';
```

This `HTMLInputElement` is interface is inherit from `HTMLElement` which is inherited from `EventTarget` at root level. Therefore we can assert using `as` operator to use specific interfaces according to the context like in this case we are using `HTMLInputElement` for input field other interfaces can be `HTMLButtonElement`, `HTMLImageElement` etc.
[](https://i.stack.imgur.com/cp3xo.png)
For more reference you can check other available interface here
- [Web API interfaces](https://developer.mozilla.org/en-US/docs/Web/API)- [Interfaces in External Node Modules](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html)


------------
    
    
## Answer \#14

 Vote: 3

Created at 2021-07-20 14:20:38

------------

You no need to type if you do this:
```
<input onChange={(event) => { setValue(e.target.value) }} />
```

Because if you set a new value with the arrow function directly in the html tag, typescript will understand by default the type of event.


------------
    
    
## Answer \#15

 Vote: 3

Created at 2022-11-24 19:04:30

------------

```
const handleChange = (
    e: ChangeEvent<HTMLInputElement>
  ) => {
    const { name, value } = e.target;
    this.setState({ ...currentState, [name]: value });
  };
```

you can apply this on every `input` element in the form component


------------
    
    
## Answer \#16

 Vote: 1

Created at 2019-10-03 12:08:21

------------

```
function handle_change(
    evt: React.ChangeEvent<HTMLInputElement>
  ): string {
    evt.persist(); // This is needed so you can actually get the currentTarget
    const inputValue = evt.currentTarget.value;

    return inputValue
  }
```


And make sure you have `"lib": ["dom"]` in your `tsconfig`.


------------
    
    
## Answer \#17

 Vote: 1

Created at 2022-09-20 16:41:06

------------


```
<input
    type="text"
    value={incrementAmount}
    onChange={(e) => {
      setIncrementAmmount(+e.target.value);
    }}
/>
```



------------
    
    
## Answer \#18

 Vote: 0

Created at 2021-09-20 13:33:37

------------

```
import { NativeSyntheticEvent, TextInputChangeEventData,} from 'react-native';



  // Todo in java script
 const onChangeTextPassword = (text : any) => {
    setPassword(text);
  }
```


```
const onChangeTextEmail = ({ nativeEvent: { text },}: NativeSyntheticEvent<TextInputChangeEventData>) => {
    console.log("________ onChangeTextEmail _________ "+ text);
    setEmailId(text);
  };


 <TextInput
          style={{ width: '100%', borderBottomWidth: 1, borderBottomColor: 'grey', height: 40, }}
          autoCapitalize="none"
          returnKeyType="next"
          maxLength={50}
          secureTextEntry={false}
          onChange={onChangeTextEmail}
          value={emailId}
          defaultValue={emailId}
          
        />
```



------------
    
    
## Answer \#19

 Vote: 0

Created at 2022-09-19 02:55:15

------------

```
const event = { target: { value: 'testing' } as HTMLInputElement };
handleChangeFunc(event as ChangeEvent<HTMLInputElement>);
```

this work for me.


------------
    
    