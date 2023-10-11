
# Post \#64006345 [Link](https://stackoverflow.com/questions/64006345/)

## React Testing Library: When to use userEvent.click and when to use fireEvent

**Vote**: 56 (116/702) **Views**: 49577 (164/702) 

**Internal ID** \#0-0-113

Created at 2020-09-22 08:57:56

Tags: `javascript` `reactjs` `react-testing-library`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm presently learning React-Testing-Library.
I'd like to test mouse interaction with an element. Presently it's a bit unclear to me the difference between userEvent.click(element) and fireEvent.click(element). Are both recommended for use, and in the example below are they being correctly implemented?
```
const mockFunction = jest.fn(() => console.info('button clicked'));
const { getByTestId } = render(<MyAwesomeButton onClick={mockFunction} />);
const myAwesomeButton = getByTestId('my-awesome-button');

// Solution A
fireEvent(myAwesomeButton)
expect(mockFunction.toHaveBeenCalledTimes(1);

// Solution B
userEvent.click(myAwesomeButton);
expect(mockFunction).toHaveBeenCalledTimes(1);
```

Thanks in advance for any clarity.


----------
        
## Answer \#0

**Accepted** Vote: 73

Created at 2020-09-22 11:04:13

------------

Behind the scenes, `userEvent` uses the `fireEvent`. You can consider `fireEvent` being the low-level api, while `userEvent` sets a flow of actions.
[Here is the code for userEvent.click](https://github.com/testing-library/user-event/blob/5feaa942f46bb37d96c2f2fbeb4b33e8beff75ad/src/click.js#L87-L103)
You can see that depending of which element you are trying to click, `userEvent` will do a set of different actions (e.g. if it's a label or a checkbox).


------------
    
    
## Answer \#1

 Vote: 16

Created at 2022-02-08 09:57:40

------------

According to [Docs](https://testing-library.com/docs/user-event/intro#difference-to-fireevent), you should use `user-event` to test interaction with your components.
`fireEvent` dispatches exactly the events you tell it to and just those - even if those exact events never had been dispatched in a real interaction in a browser.
`User-event` on the other hand dispatches the events like they would happen if a user interacted with the document. That might lead to the same events you previously dispatched per `fireEvent` directly, but it also might catch bugs that make it impossible for a user to trigger said events.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-09-02 15:28:36

------------

another good to mention difference between `fireEvent` and `userEvent` is that
by default fireEvent is wrapped inside `act` function and this is useful when the user does some action and this action will cause component updates and re-render. on the controversy, if we used `userEvent` probably will notice `"not wrapped in act(...)" warning` error that appears in the console.
```
act(() => {
  userEvent.type(input, 'inputValue')
})
```

and here we don't need the act function, cause it's already wrapped over fireEvent
```
fireEvent.change(input, {target: {value: 'inputValue'}})
```

and this great article demonstrates this concept
[Common mistakes with React Testing Library](https://kentcdodds.com/blog/common-mistakes-with-react-testing-library)


------------
    
    