
# Post \#71938830 [Link](https://stackoverflow.com/questions/71938830/)

## clearInterval not working in React Application using functional component

**Vote**: 1 (672/702) **Views**: 2715 (582/702) 

**Internal ID** \#0-0-55

Created at 2022-04-20 11:27:49

Tags: `javascript` `reactjs` `setinterval` `react-functional-component` `clearinterval`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I wanted to build a timer application in `React` using `functional component` and below are the requirements.
The component will display a number initialized to `0` know as `counter`.
The component will display a `Start` button below the `counter` number.
On clicking the `Start` button the counter will start running. This means the `counter` number will start incrementing by 1 for every one second.
When the counter is running(incrementing), the `Start` button will become the `Pause` button.
On clicking the `Pause` button, the `counter` will preserve its value (number) but stops running(incrementing).
The component will also display a `Reset` button.
On clicking the `Reset` button, the `counter` will go to its initial value(which is `0` in our case) and stops running(incrementing).
Below is the code that I have implemented, but `clearInterval` doesn't seems to be working, Also how do i implement Reset Button?
Code:
```
import React, { useState, useEffect } from "react";

export default function Counter() {
  const [counter, setCounter] = useState(0);
  const [flag, setFlag] = useState(false);
  const [isClicked, setClicked] = useState(false);
  var myInterval;

  function incrementCounter() {
    setClicked(!isClicked);
    if (flag) {
      myInterval = setInterval(
        () => setCounter((counter) => counter + 1),
        1000
      );
      setFlag(false);
    } else {
      console.log("sasdsad");
      clearInterval(myInterval);
    }
  }

  function resetCounter() {
    clearInterval(myInterval);
    setCounter(0);
  }

  useEffect(() => {
    setFlag(true);
  }, []);

  return (
    <div>
      <p>{counter}</p>
      <button onClick={incrementCounter}>
        {isClicked ? "Pause" : "Start"}
      </button>
      <button onClick={resetCounter}>Reset</button>
    </div>
  );
}
```

Codesandbox link:
[CodeSandbox](https://codesandbox.io/s/romantic-varahamihira-5xso6y?file=/src/Counter.js)


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2022-04-20 11:58:18

------------

I did a slightly different version that use an extra `useEffect` that runs on `isRunning` (changed name from `flag`) change:
```
import React, { useState, useEffect, useRef } from "react";

export default function Counter() {
  const [counter, setCounter] = useState(0);
  // Change initial value to `false` if you don't want
  // to have timer running on load
  // Changed `flag` name to more significant name
  const [isRunning, setIsRunning] = useState(false);
  // You don't need 2 variable for this
  //const [isClicked, setClicked] = useState(false);

  // Using `useRef` to store a reference to the interval
  const myInterval = useRef();

  useEffect(() => {
    // You had this line to start timer on load
    // but you can just set the initial state to `true`
    //setFlag(true);
    // Clear time on component dismount
    return () => clearInterval(myInterval.current);
  }, []);

  // useEffect that start/stop interval on flag change
  useEffect(() => {
    if (isRunning) {
      myInterval.current = setInterval(
        () => setCounter((counter) => counter + 1),
        1000
      );
    } else {
      clearInterval(myInterval.current);
      myInterval.current = null;
    }
  }, [isRunning]);

  // Now on click you only change the flag
  function toggleTimer() {
    setIsRunning((isRunning) => !isRunning);
  }

  function resetCounter() {
    clearInterval(myInterval.current);
    myInterval.current = null;
    setCounter(0);
    setIsRunning(false);
  }

  return (
    <div>
      <p>{counter}</p>
      <button onClick={toggleTimer}>{isRunning ? "Pause" : "Start"}</button>
      <button onClick={resetCounter}>Reset</button>
    </div>
  );
}
```

Demo: [https://codesandbox.io/s/dank-night-wwxqz3?file=/src/Counter.js](https://codesandbox.io/s/dank-night-wwxqz3?file=/src/Counter.js)
As a little extra i've made a version that uses a custom hook `useTimer`. In this way the component code is way cleaner:
[https://codesandbox.io/s/agitated-curie-nkjf62?file=/src/Counter.js](https://codesandbox.io/s/agitated-curie-nkjf62?file=/src/Counter.js)


------------
    
    
## Answer \#1

 Vote: 2

Created at 2022-04-20 11:35:08

------------

Use `useRef` to make the interval as a ref. Then use `resetCounter()` to clean the interval ref.
```
const intervalRef = useRef(null)

const incrementCounter = () => {
  intervalRef.current = setInterval(() => {
    setCounter(prevState => prevState + 1)
  }, 1000);
};

const resetCounter = () => {
  clearInterval(intervalRef.current);
  intervalRef.current = null;
};
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-04-20 12:31:29

------------

Between each rendering your variable `myInterval` value doesn't survive. That's why you need to use the [`useRef`][1] hook that save the reference of this variable across each rendering.
Besides, you don't need an flag function, as you have all information with the `myClicked` variable
Here is a modification of your code with those modifications. Don't hesitate if you have any question.
```
import React, { useState, useEffect, useRef } from "react";

export default function Counter() {
  const [counter, setCounter] = useState(0);
  const [isStarted, setIsStarted] = useState(false);
  const myInterval = useRef();

  function start() {
    setIsStarted(true);
      myInterval.current = setInterval(() => setCounter((counter) => counter + 1), 100);
      100;
    } 

  function pause() {
    setIsStarted(false);
    clearInterval(myInterval.current);
  }

  function resetCounter() {
    clearInterval(myInterval.current);
    setCounter(0);
  }

  return (
    <div>
      <p>{counter}</p>
      {!isStarted ? 
      <button onClick={start}>
        Start
      </button> 
      :
      <button onClick={pause}>
        Pause
      </button> 
    }
      
      <button onClick={resetCounter}>Reset</button>
    </div>
  );
}
\\\


  [1]: https://reactjs.org/docs/hooks-reference.html#useref
```



------------
    
    
## Answer \#3

 Vote: 2

Created at 2022-09-23 00:43:31

------------

I'll just leave this here for anyone having the same problem.
in my case, the issue was node setInterval was used instead of window.setInterval.
this is a problem since this returns a type of Node.Timer which is an object instead of number (setInterval ID) for the clearInterval() to work as it needs an argument type of number. so to fix this,
```
React.useEffect(() => {
 let timeoutId;
 timeoutId = window.setInterval(callback, 100);

 return = () => {
  if(timeoutId) clearInterval(timeoutId)
 }
}, [])
```

or in class components use componentWillMount()


------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-04-20 11:33:54

------------

You have to store `myInterval` in state. After that when button is clicked and  `flag` is `false`, you can clear interval (myInterval in state).


------------
    
    