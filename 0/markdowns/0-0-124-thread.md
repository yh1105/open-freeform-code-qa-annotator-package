
# Post \#58976251 [Link](https://stackoverflow.com/questions/58976251/)

## Checking text appears inside an element using react testing library

**Vote**: 64 (104/702) **Views**: 100392 (85/702) 

**Internal ID** \#0-0-124

Created at 2019-11-21 13:32:00

Tags: `javascript` `reactjs` `react-testing-library`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm writing some tests for a React app using [Testing Library](https://testing-library.com/). I want to check that some text appears, but I need to check it appears in a particular place because I know it already appears somewhere else.
The [Testing Library documentation for queries](https://testing-library.com/docs/dom-testing-library/api-queries#bytext) says that the `getByText` query takes a `container` parameter, which I guessed lets you search within that container. I tried doing this, with the `container` and `text` parameters in the order specified in the docs:
```
const container = getByTestId('my-test-id');
expect(getByText(container, 'some text')).toBeTruthy();
```

and I get an error: `matcher.test is not a function`.
If I put the params the other way round:
```
const container = getByTestId('my-test-id');
expect(getByText('some text', container)).toBeTruthy();
```

I get a different error: `Found multiple elements with the text: some text`
Which means it's not searching inside the specified container.
I think I'm not understanding how `getByText` works. What am I doing wrong?


----------
        
## Answer \#0

**Accepted** Vote: 74

Created at 2019-11-25 08:43:28

------------

Better to use [within](https://testing-library.com/docs/dom-testing-library/api-within/) for this sort of things:
```
render(<MyComponent />)
const { getByText } = within(screen.getByTestId('my-test-id'))
expect(getByText('some text')).toBeInTheDocument()
```



------------
    
    
## Answer \#1

 Vote: 60

Created at 2020-10-13 14:39:21

------------

Another way to do this
```
import {render, screen} from '@testing-library/react';

...

  render(<MyComponent />);
  expect(screen.getByTestId('my-test-id')).toHaveTextContent('some text');
```

Note it is now recommended to use `screen` instead of the results of render.
(StackOverflow post the points to a KC Dobbs Article explaining why: [react-testing-library - Screen vs Render queries](https://stackoverflow.com/questions/61482418/react-testing-library-screen-vs-render-queries))


------------
    
    
## Answer \#2

 Vote: 5

Created at 2022-09-19 17:37:19

------------

This way you can be more precise, focusing in specific item:
```
expect(queryByTestId("helperText")?.textContent).toContain("Help me!");
```



------------
    
    