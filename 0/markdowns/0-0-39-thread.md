
# Post \#52577141 [Link](https://stackoverflow.com/questions/52577141/)

## How to submit form from a button outside that component in React?

**Vote**: 43 (145/702) **Views**: 66774 (131/702) 

**Internal ID** \#0-0-39

Created at 2018-09-30 10:41:47

Tags: `reactjs`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have a form in one of my React components, and and in the outside component that calls it I want to pass a reference to a button there, so that I can also submit that using that button.

To make it more clear I have the following:

```
import React, { Component } from "react";
import ReactDOM from "react-dom";

class CustomForm extends Component {
  render() {
    return (
      <form onSubmit={alert('Form submitted!')}>
        <button type='submit'>Inside Custom</button>
      </form>
    );
  }
}

function App() {
  return (
    <div>
      <CustomForm />
      <button>In Root</button>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```


Now, I can submit the form using the button titled 'Inside Custom', but I also want to be able to submit the form using the button titled 'In Root' that is located in the root component. Is there a way to somehow pass reference from that button to that custom component, and actually submit the form when `In Root` button is clicked?


----------
        
## Answer \#0

**Accepted** Vote: 11

Created at 2018-09-30 11:20:11

------------

: Simple and correct answer: [https://stackoverflow.com/a/53573760/5271656](https://stackoverflow.com/a/53573760/5271656)  

In React, data flows down and actions flow up. So notify child component about button click in the parent.
This is how you can do this.  

```
import React, { Component } from "react";
import ReactDOM from "react-dom";

class CustomForm extends Component {
  handleOnSubmit = e => {
    e.preventDefault();
    // pass form data
    // get it from state
    const formData = {};
    this.finallySubmit(formData);
  };

  finallySubmit = formData => {
    alert("Form submitted!");
  };

  componentDidUpdate(prevProps, prevState) {
    if (this.props.submitFromOutside) {
      // pass form data
      // get it from state
      const formData = {};
      this.finallySubmit();
    }
  }

  render() {
    return (
      <form onSubmit={this.handleOnSubmit}>
        <button type="submit">Inside Custom</button>
      </form>
    );
  }
}

class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      submitFromOutside: false
    };
  }
  submitCustomForm = () => {
    this.setState({
      submitFromOutside: true
    });
  };

  componentDidMount() {
    console.log(this.form);
  }

  render() {
    return (
      <div>
        <CustomForm submitFromOutside={this.state.submitFromOutside} />
        <button onClick={this.submitCustomForm}>In Root</button>
      </div>
    );
  }
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```


To me, this solution is hacky and not in a react way but serves your use-case.
Find working solution here:[https://codesandbox.io/s/r52xll420m](https://codesandbox.io/s/r52xll420m)


------------
    
    
## Answer \#1

 Vote: 132

Created at 2018-12-01 18:22:50

------------

You can achieve this by using regular HTML capabilities ([HTML form Attribute](https://www.w3schools.com/tags/att_form.asp)), no need to use the React hacks:

Add "id" attribute to your form: id='my-form'

```
class CustomForm extends Component {
    render() {
        return (
             <form id='my-form' onSubmit={alert('Form submitted!')}>
                // Form Inputs go here    
             </form>
        );
    }
}
```


Then add the same Id to the "form" attribute of the target button outside of the form:

```
<button form='my-form' type="submit">Outside Button</button>
```


Now, the 'Outside Button' button will be absolutely equivalent as if it is inside the form. 

Note: This is not supported by IE11.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2018-09-30 11:25:58

------------

You can set in the state a flag propery for example `rootBtnClicked: false` and a method that handles the click in the `App` component. Also in the `<CustomForm />`, add a prop e.g. `rootBtnClicked={this.state.rootBtnClicked}`. When "In Root" button is clicked, trigger that method; then the method changes the state with `setState()`. Then inside the `CustomForm` component's `render()` method, check if the prop is `true`. If it is, manually trigger [.submit()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormElement/submit) using React's [ref](https://reactjs.org/docs/refs-and-the-dom.html).


------------
    
    
## Answer \#3

 Vote: 2

Created at 2021-06-06 21:03:06

------------

Well, set up a button with type of `submit` and attribute `form` which has value equal to `id` of the form you want to submit


------------
    
    
## Answer \#4

 Vote: 0

Created at 2018-09-30 11:26:48

------------

You can pass the submit function as a prop and use this method whenever you want. 
for more complex situations you can use redux save your form data on redux and whenever the button is clicked you read data from redux and submit them 


------------
    
    
## Answer \#5

 Vote: 0

Created at 2020-05-22 15:04:57

------------

None of these solutions worked for me, but I finally figured it out by doing

```
document.forms[0].submit()
```


Since there was only one form on the page, I selected it with the 0 index, but if there are multiple forms then you'd need to use the applicable index for the forms array.


------------
    
    
## Answer \#6

 Vote: -2

Created at 2018-09-30 11:11:46

------------

You can pass the onSubmit click handler as a props as follows:

```
import React, { Component } from "react";
import ReactDOM from "react-dom";

class CustomForm extends Component {

  render() {
    return (
      <form onSubmit={this.props.handleSubmit}>
        <button type='submit'>Inside Custom</button>
      </form>
    );
  }
}

function App() {
 handleSubmit = () => {
   alert('Form submitted!')
}
  return (
    <div>
      <CustomForm onSubmit={this.handleSubmit} />
      <button>In Root</button>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```


Passing function as a props to handle form submit makes the CustomForm element more reusable since the business logic for the submit is not part of CustomForm component.


------------
    
    