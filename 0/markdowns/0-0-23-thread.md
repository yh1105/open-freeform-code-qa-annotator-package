
# Post \#61751728 [Link](https://stackoverflow.com/questions/61751728/)

## Asynchronous calls with React.useMemo

**Vote**: 64 (104/702) **Views**: 67300 (130/702) 

**Internal ID** \#0-0-23

Created at 2020-05-12 12:27:42

Tags: `reactjs` `async-await` `react-usememo`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

Scenario is relatively simple: we have a long-running, on-demand calculation that occurs on a remote server. We want to memoize the result. Even though we are fetching asychronously from a remote resource, this isn't a side effect because we just want the result of this calculation to display to the user and we definitely don't want to do this on every render.

Problem: it seems that React.useMemo does not directly support Typescript's async/await and will return a promise:

```
//returns a promise: 
let myMemoizedResult = React.useMemo(() => myLongAsyncFunction(args), [args])
//also returns a promise:
let myMemoizedResult = React.useMemo(() => (async () => await myLongAsyncFunction(args)), [args])
```


What is the correct way to wait on the result from an asynchronous function and memoize the result using React.useMemo? I've used regular promises with plain JS but still struggle with them in these types of situations.

I've tried other approaches such as memoize-one, but the issue seems to be that the `this` context changes due to the way that React function components work [break the memoization](https://github.com/alexreardon/memoize-one#changes-to-this-is-considered-an-argument-change), which is why I'm trying to use React.useMemo.

Maybe I'm trying to fit a square peg in a round hole here - if that's the case it would be good to know that too. For now I'm probably just going to roll my own memoizing function.

Edit: I think part of it was that I was making a different silly mistake with memoize-one, but I'm still interested to know the answer here wrt React.memo.

Here's a snippet - the idea is not to use the memoized result directly in the render method, but rather as something to reference in an event-driven way i.e. on a Calculate button click.

```
export const MyComponent: React.FC = () => {
    let [arg, setArg] = React.useState('100');
    let [result, setResult] = React.useState('Not yet calculated');

    //My hang up at the moment is that myExpensiveResultObject is 
    //Promise<T> rather than T
    let myExpensiveResultObject = React.useMemo(
        async () => await SomeLongRunningApi(arg),
        [arg]
    );

    const getResult = () => {
        setResult(myExpensiveResultObject.interestingProperty);
    }

    return (
        <div>
            <p>Get your result:</p>
            <input value={arg} onChange={e => setArg(e.target.value)}></input>
            <button onClick={getResult}>Calculate</button>
            <p>{`Result is ${result}`}</p>
        </div>);
}
```



----------
        
## Answer \#0

**Accepted** Vote: 72

Created at 2021-02-05 22:02:48

------------

What you really want is to re-render your component once the asynchronous call is over. Memoisation alone won't help you achieve that. Instead you should use React's state - it will keep the value your async call returned and it will allow you to trigger a re-render.
Furthermore, triggering an async call is a side effect, so it should not be performed during the render phase - neither inside the main body of the component function, nor inside `useMemo(...)` which also happens during the render phase. Instead all side effects should be triggered inside `useEffect`.
Here's the complete solution:
```
const [result, setResult] = useState()

useEffect(() => {
  let active = true
  load()
  return () => { active = false }

  async function load() {
    setResult(undefined) // this is optional
    const res = await someLongRunningApi(arg1, arg2)
    if (!active) { return }
    setResult(res)
  }
}, [arg1, arg2])
```

Here we call the async function inside `useEffect`. Note that you cannot make the whole callback inside `useEffect` async - that's why instead we declare an async function `load` inside and call it without awaiting.
The effect will re-run once one of the `arg`s changes - this is what you want in most cases. So make sure to memoise `arg`s if you re-calculate them on render. Doing `setResult(undefined)` is optional - you might instead want to keep the previous result on the screen until you get the next result. Or you might do something like `setLoading(true)` so the user knows what's going on.
Using `active` flag is important. Without it you are exposing yourself to a race condition waiting to happen: the second async function call may finish before the first one finishes:

1. start first call
2. start second call
3. second call finishes, setResult() happens
4. first call finishes, setResult() happens again, overwriting the correct result with a stale one


and your component ends up in an inconsistent state. We avoid that by using `useEffect`'s cleanup function to reset the `active` flag:

1. set active#1 = true, start first call
2. arg changes, cleanup function is called, set active#1 = false
3. set active#2 = true, start second call
4. second call finishes, setResult() happens
5. first call finishes, setResult() doesn't happen since active#1 is false




------------
    
    
## Answer \#1

 Vote: 5

Created at 2020-05-12 16:38:20

------------

 My original answer below seems to have some unintended side effects due to the asynchronous nature of the call. I would instead try and either think about memoizing the actual computation on the server, or using a self-written closure to check if the `arg` hasn't changed. Otherwise you can still utilize something like `useEffect` as I described below.

I believe the problem is that `async` functions always implicitly return a promise. Since this is the case, you can directly `await` the result to unwrap the promise:

```
const getResult = async () => {
  const result = await myExpensiveResultObject;
  setResult(result.interestingProperty);
};
```


See an [example codesandbox here](https://codesandbox.io/s/upbeat-kirch-2sb01?file=/src/App.tsx:550-716).

I do think though that a better pattern may be to utilize a `useEffect` that has a dependency on some state object that only gets set on the button click in this case, but it seems like the `useMemo` should work as well.


------------
    
    
## Answer \#2

 Vote: 3

Created at 2021-05-18 01:55:34

------------

I think React specifically mentions that [useMemo](https://reactjs.org/docs/hooks-reference.html#usememo) should not be used to manage side effects like asynchronous API calls. They should be managed in `useEffect` hooks where there are proper dependencies set to determine whether they should be re-run or not.


------------
    
    