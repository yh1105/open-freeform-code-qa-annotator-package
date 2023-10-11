
# Post \#64165138 [Link](https://stackoverflow.com/questions/64165138/)

## How to mock/spy useState hook in jest?

**Vote**: 22 (248/702) **Views**: 64696 (133/702) 

**Internal ID** \#0-0-68

Created at 2020-10-02 01:26:13

Tags: `javascript` `reactjs` `testing` `jestjs` `enzyme`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am trying to spy on useState React hook but i always get the test failed
This is my React component:
```
const Counter= () => {
    const[counter, setCounter] = useState(0);

    const handleClick=() => {
        setCounter(counter + 1);
    }

    return (
        <div>
            <h2>{counter}</h2>
            <button onClick={handleClick} id="button">increment</button>
        </div>
    )
}
```

:
```
it('increment counter correctlry', () => {
    let wrapper = shallow(<Counter/>);
    const setState = jest.fn();
    const useStateSpy = jest.spyOn(React, 'useState');

    useStateSpy.mockImplementation((init) => [init, setState]);
     const button = wrapper.find("button")
     button.simulate('click');
     expect(setState).toHaveBeenCalledWith(1);
})
```

Unfortunately this doesn't work and i get the test failed with that message:
```
expected 1
Number of calls: 0
```



----------
        
## Answer \#0

**Accepted** Vote: 23

Created at 2020-10-02 03:34:05

------------

You need to use `React.useState` instead of the single import `useState`.
I think is about how the code gets [transpiled](https://babeljs.io/repl#?browsers=ie%2010&build=&builtIns=usage&corejs=3.6&spec=false&loose=false&code_lz=JYWwDg9gTgLgBAJQKYEMDGMA0cDecCuAzkgMowoxJwC-cAZlBCHAORSoYsBQXRp5lLsnQwAdHzIUkQA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=false&presets=env%2Creact&prettier=false&targets=&version=7.16.3&externalPlugins=&assumptions=%7B%7D), as you can see in the babel repl the `useState` from the single import ends up being different from the one of the module import
```
_react.useState // useState
_react.default.useState // React.useState;
```

So you spy on `_react.default.useState` but your component uses `_react.useState`.
It seems impossible to spyOn a single import since you need the function to belong to an object, here is a very extensive guide that explains the ways of mocking/spying modules [https://github.com/HugoDF/mock-spy-module-import](https://github.com/HugoDF/mock-spy-module-import)
And as @Alex Mackay mentioned, you probably want to change your mindset about testing react components, moving to react-testing-library is recommended, but if you really need to stick to enzyme you don't need to go that far as to mock react library itself


------------
    
    
## Answer \#1

 Vote: 24

Created at 2021-07-14 08:50:06

------------

diedu's answer led me the right direction and I came up with this solution:

1. Mock use state from react to return a jest.fn() as useState: 1.1 Also import useState immediately after - that will now be e jest mock (returned from the jest.fn() call)


```
jest.mock('react', ()=>({
  ...jest.requireActual('react'),
  useState: jest.fn()
}))
import { useState } from 'react';
```



1. Later on in the beforeEach, set it to the original useState, for all the cases where you need it to not be mocked


```
describe("Test", ()=>{
  beforeEach(()=>{
    useState.mockImplementation(jest.requireActual('react').useState);
    //other preperations
  })
  //tests
})
```



1. In the test itself mock it as needed:


```
it("Actual test", ()=>{
  useState.mockImplementation(()=>["someMockedValue", someMockOrSpySetter])
})
```


Parting notes: While it might be conceptually somewhat wrong to get your hands dirty inside the "black box" one is unit testing, it is indeed super useful at times to do it.


------------
    
    
## Answer \#2

 Vote: 6

Created at 2020-10-02 03:21:07

------------

Annoyingly Codesandbox is currently having trouble with its testing module so I can't post a working example but I will try to explain why mocking `useState` is generally a bad thing to do.
The user doesn't care if `useState` has been called, they care about  therefore that is what you should be testing for.
```
// App
import React, { useState } from "react";
export default function App() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={() => setCount((prev) => prev + 1)}>Increment</button>
    </div>
  );
}
// Tests
import React from "react";
import App from "./App";
import { screen, render } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

describe("App should", () => {
  it('increment count value when "Increment" btn clicked', () => {
    // Render the App
    render(<App />);
    // Get the count in the same way the user would, by looking for 'Count'
    let count = screen.getByText(/count:/);
    // As long as the h1 element contains a '0' this test will pass
    expect(count).toContain(0);
    // Once again get the button in the same the user would, by the 'Increment'
    const button = screen.getByText(/increment/);
    // Simulate the click event
    userEvent.click(button);
    // Refetch the count
    count = screen.getByText(/count:/);
    // The 'Count' should no longer contain a '0'
    expect(count).not.toContain(0);
    // The 'Count' should contain a '1'
    expect(count).toContain(1);
  });
  // And so on...
  it('reset count value when "Reset" btn is clicked', () => {});
  it('decrement count value when "Decrement" btn is clicked', () => {});
});
```

Definitely check out `@testing-library` if you are interested in this style of testing. I switched from `enzyme` about 2 years ago and haven't touched it since.


------------
    
    
## Answer \#3

 Vote: 6

Created at 2021-04-28 13:52:25

------------

just you need to import React in your test file like:
```
import * as React from 'react';
```

after that you can use the mock function.
```
import * as React from 'react';

:
:
it('increment counter correctlry', () => {
    let wrapper = shallow(<Counter/>);
    const setState = jest.fn();
    const useStateSpy = jest.spyOn(React, 'useState');

    useStateSpy.mockImplementation((init) => [init, setState]);
     const button = wrapper.find("button")
     button.simulate('click');
     expect(setState).toHaveBeenCalledWith(1);
})
```



------------
    
    
## Answer \#4

 Vote: 3

Created at 2021-09-23 08:55:46

------------

you should use React.useState() instead useState(), But there are other ways...
in React you can set useState without React with this config
```
// setupTests.js
    const { configure } = require('enzyme')
    const Adapter = require('@wojtekmaj/enzyme-adapter-react-17')
    const { createSerializer } = require('enzyme-to-json')

    configure({ adapter: new Adapter() });
    expect.addSnapshotSerializer(createSerializer({
        ignoreDefaultProps: true,
        mode: 'deep',
        noKey: true,
    }));
```


```
import React, { useState } from "react";

    const Home = () => {

        const [count, setCount] = useState(0);

        return (
            <section>

                <h3>{count}</h3>
                <span>
                    <button id="count-up" type="button" onClick={() => setCount(count + 1)}>Count Up</button>
                    <button id="count-down" type="button" onClick={() => setCount(count - 1)}>Count Down</button>
                    <button id="zero-count" type="button" onClick={() => setCount(0)}>Zero</button>
                </span>
            </section>
        );

    }

    export default Home;
```


// index.test.js
```
import { mount } from 'enzyme';
    import Home from '../';
    import React, { useState as useStateMock } from 'react';


    jest.mock('react', () => ({
        ...jest.requireActual('react'),
        useState: jest.fn(),
    }));

    describe('<Home />', () => {
        let wrapper;

        const setState = jest.fn();

        beforeEach(() => {
            useStateMock.mockImplementation(init => [init, setState]);
            wrapper = mount(<Home />);
        });

        afterEach(() => {
            jest.clearAllMocks();
        });

        describe('Count Up', () => {
            it('calls setCount with count + 1', () => {
                wrapper.find('#count-up').simulate('click');
                expect(setState).toHaveBeenCalledWith(1);
            });
        });

        describe('Count Down', () => {
            it('calls setCount with count - 1', () => {
                wrapper.find('#count-down').props().onClick();
                expect(setState).toHaveBeenCalledWith(-1);
            });
        });

        describe('Zero', () => {
            it('calls setCount with 0', () => {
                wrapper.find('#zero-count').props().onClick();
                expect(setState).toHaveBeenCalledWith(0);
            });
        });
    });
```




------------
    
    