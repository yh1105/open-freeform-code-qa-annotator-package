
# Post \#59932119 [Link](https://stackoverflow.com/questions/59932119/)

## React hooks equivalent of componentDidCatch?

**Vote**: 55 (117/702) **Views**: 51454 (159/702) 

**Internal ID** \#0-0-110

Created at 2020-01-27 13:27:30

Tags: `reactjs` `react-hooks`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have a simple component that acts as an error boundary in my React app, and which passes off any errors to a logging service.

It looks something like this:

```
export class CatchError extends React.PureComponent {
  state = {
    hasError: false
  }

  componentDidCatch(error, info) {
    this.props.log({ error, info })
    this.setState({ hasError: true })
  }

  render() {
    const child = typeof this.props.children === "function"
      ? this.props.children({ error: hasError })
      : children

    return React.cloneElement(child, { error: this.state.hasError })
  }
}
```


Is there a React hook equivalent to `componentDidCatch` so I can make this component a function and not a class?

So it might look something like this:

```
export function CatchError({ children, log }) {
  const [hasError, setHasError] = useState(false)
  const caught = useDidCatch()

  useEffect(() => {
    const [error, info] = caught
    log({ error, info })
    setHasError(true)
  }, [caught])

  const child = typeof children === "function"
    ? children({ error: hasError })
    : children

  return React.cloneElement(child, { error: hasError })
}
```



----------
        
## Answer \#0

**Accepted** Vote: 54

Created at 2020-01-27 13:27:30

------------

There is not a React hook equivalent of `componentDidCatch`.
However, the React team plans to add one soon.
The React docs state:
> There are no Hook equivalents to the uncommon getSnapshotBeforeUpdate and componentDidCatch lifecycles yet, but we plan to add them soon.
Read more: [https://reactjs.org/docs/hooks-faq.html#do-hooks-cover-all-use-cases-for-classes](https://reactjs.org/docs/hooks-faq.html#do-hooks-cover-all-use-cases-for-classes)


------------
    
    
## Answer \#1

 Vote: 21

Created at 2020-03-27 12:44:06

------------

There is not any official hook for [Error Boundaries](https://reactjs.org/docs/error-boundaries.html)(componentDidCatch) in react.
but you can simply use [try...catch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch) to catch component errors like this:

```
function myComponent(...) {
  // Initialize state variables.
  // Initialize context variables.
  // Initialize custom hooks.
  // ...

  try {
    // Define internal functions.
    // Define internal variables.

    return (
      <SomeThing />
    )
  } catch (error) {
    // Catch internal functions, variables and return (jsx) errors.
    // You can also create a lib to log the error to an error reporting service
    // and use it here.
  }
}
```


The `try...catch` block is the  here.

:
when you use a hook like `useEffect` and you use some `internal functions` in it, you cannot put that internal function into try...catch(Error Boundary) block because you should define that function on top of useEffect hook ([why?](https://stackoverflow.com/questions/56850196/where-should-i-declare-functions-that-are-called-inside-a-useeffect-hook)) and you shouldn't use useEffect conditionally ([why?](https://reactjs.org/docs/hooks-rules.html#only-call-hooks-at-the-top-level))!


------------
    
    
## Answer \#2

 Vote: 4

Created at 2021-04-19 21:28:27

------------

As mentioned already, the React team has [not yet implemented a hook equivalent](https://reactjs.org/docs/hooks-faq.html#do-hooks-cover-all-use-cases-for-classes), and there are [no published timelines](https://github.com/facebook/react/issues/19630#issuecomment-675390931) for a hook implementation.
A few third party packages on npm implement error boundary hooks. This is the [most popular npm package](https://www.npmjs.com/package/use-error-boundary). Alternatively, I published [react-use-error-boundary](https://www.npmjs.com/package/react-use-error-boundary), attempting to recreate an API similar to [useErrorBoundary from Preact](https://preactjs.com/guide/v10/hooks/#useerrorboundary).


------------
    
    