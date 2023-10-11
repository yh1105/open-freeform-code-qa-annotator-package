
# Post \#56541342 [Link](https://stackoverflow.com/questions/56541342/)

## React Hooks: Why is .current null for useRef Hook?

**Vote**: 101 (60/702) **Views**: 126891 (68/702) 

**Internal ID** \#0-0-65

Created at 2019-06-11 10:07:47

Tags: `reactjs` `react-hooks`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have a simple example of a component: 

```
function App() {
  const observed = useRef(null);
  console.log(observed.current);

  return (
    <div ref={observed} className="App">
      <h1>Hello CodeSandbox</h1>
      <h2>Start editing to see some magic happen!</h2>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```


I would expect that `observed.current` would be of type element and current would not be empty but the div element with all its properties. My understanding would be:


1. The ref is initialised with a value of null
2. Null is overwritten by the ref



But as it turns out, `.current` remains empty. This is bad since I want to pass observed to a function that expects an argument of type Element.

[https://codesandbox.io/embed/purple-forest-0460k](https://codesandbox.io/embed/purple-forest-0460k)


----------
        
## Answer \#0

**Accepted** Vote: 91

Created at 2019-06-11 10:13:28

------------

`Ref.current` is null because the ref is not set till after the function returns and the content is rendered. The [useEffect](https://reactjs.org/docs/hooks-effect.html) hook fires every time the value of contents of the array passed to it changes. By passing `observed` in the array and by passing a callback that logs `observed` to the console, the useEffect hook can be leveraged to accomplish your task.

```
useEffect(() => {
    console.log(observed.current);
  }, [observed]);
```


Edit: This only works on the first render as changes to a ref do not trigger re-renders. But the general statement about useEffect still holds. If you want to observe changes to a ref of a dom element, you can use a [callback as ref.](https://reactjs.org/docs/refs-and-the-dom.html#callback-refs)

```
<div 
    ref={el => { console.log(el); observed.current = el; }} // or setState(el)
    className="App"
  >
```


[Code Sandbox](https://codesandbox.io/s/tender-payne-eyk5f)


------------
    
    
## Answer \#1

 Vote: 20

Created at 2020-05-18 15:59:53

------------

This is what I ended up doing since calling `useEffect` on `rlvRef` did not capture all events.
```
const rlvRef = useRef()
const [refVisible, setRefVisible] = useState(false)

useEffect(() => {
  if (!refVisible) { 
    return
  }
  // detected rendering
}, refVisible)

return (
  <RecyclerListView
    ref={el => { rlvRef.current = el; setRefVisible(!!el); }}
    ... 
  />
)
```



------------
    
    
## Answer \#2

 Vote: 16

Created at 2019-06-11 10:18:56

------------

At the time the `console.log` happens, the `ref` isn't yet set. Try putting your `console.log` into a `useEffect` hook and it works as you want it to. 

```
import React, { useRef, useEffect } from "react";
import ReactDOM from "react-dom";

import "./styles.css";

function App() {
  const observed = useRef(null);

  useEffect(() => {
    console.log(observed.current);
  }, [observed]);

  return (
    <div ref={observed} className="App">
      <h1>Hello CodeSandbox</h1>
      <h2>Start editing to see some magic happen!</h2>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```



------------
    
    
## Answer \#3

 Vote: -1

Created at 2022-08-24 17:03:27

------------

I needed to set a value into the useRef()-variable which was retrieved by an async http-request and was saved in a useState()-variable. So i needed to provide it as an dependency as well in the useEffect()-function:
```
const [object, setObject] = useState('');

    const fetchObject = useCallback(async () => {
        const res = await fetch(apiUrl + "/object/", {});
        setObject({ ...await res.json() });
    }, [apiUrl]);

    const jsonEditor = React.createRef();

    useEffect(() => {
        fetchObject();
        if (jsonEditor.current && object.json) {
            jsonEditor.current.jsonEditor.set(JSON.parse(object.json));
        }
    }, [fetchObject, object.json]);

return (
    <div>
        <JsonEditor
            ref={jsonEditor}
            onChangeText={onChangeTextHandler}
        />
    </div>);
```



------------
    
    