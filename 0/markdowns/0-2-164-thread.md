
# Post \#56378786 [Link](https://stackoverflow.com/questions/56378786/)

## What is the use case of Styled-Components' .attrs() function?

**Vote**: 19 (269/702) **Views**: 27117 (245/702) 

**Internal ID** \#0-2-164

Created at 2019-05-30 12:57:15

Tags: `javascript` `html` `reactjs` `styled-components`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

I came across the [.attrs() function in styled components](https://www.styled-components.com/docs/basics#attaching-additional-props), but I do not understand what it does, or what it does ?
I tried to understand the example in their docs, but as far as I am concerned, I can do the exact same thing without the `attrs()` function.
Could somebody please explain this to me or give a simple example?


----------
        
## Answer \#0

**Accepted** Vote: 27

Created at 2019-05-30 13:48:39

------------


Styled components as the name suggests are for styling the native DOM elements or custom components.
`attrs` is for mentioning the attributes of that very same DOM element in the `attrs` constructor without the need to even mention it in actual component call.
What above line means is you can do
```
<Input placeholder="A small text input" />
```

with
```
const Input = styled.input.attrs(({ type }) => ({
  type:  type || "password"
}))`
  align-items: center;
  display: flex;
  margin: 1.5vh 0;
`
```

See that component usage `<Input .../>` doesn't have `type` prop anywhere. It came from your attribute constructor function(static)
You couldn't have done this otherwise in your style rules because these are just the string literal of your `CSS` properties.
It saved you from writing `type='password'` in every usage of
`<Input type='password' ... />`
:
Now, that's a specific `input` component with `type` attribute of password. What if you wish to have a general `input` (styled component) with any value of `type` attribute?
Tada!
```
const Input = styled.input.attrs(({ type }) => ({
  
  type:  type || "password",
  ...
```

Your `type` is now dynamic i.e. it will take whatever input `type`  you specify from your usage of component and render the input as that type (text, password, file etc. ) or if you skip the type prop it will pick up default of `password`. You can use as much conditional logic as you want up there.
Example:
```
<Input .../> // renders type="password"
<Input type="text" .../>
<Input type="email" .../>
```

Hope that answers your question.


------------
    
    
## Answer \#1

 Vote: 5

Created at 2019-05-30 13:31:12

------------

The goal of .attrs is so it can be passed down from your props.
So you can use props inside your styled, and you can create placeholders, or change colors depending of the props etc...
For example :
```
const InputText = styled.input.attrs({
  type: 'text',
  placeholder: props => props.placeholder || 'Please fill',
})`
  padding: 6px 12px; 
`;
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2020-05-23 03:22:52

------------

Another interesting use is code organization.
Styled-components only work with the `style` prop but many custom components don't expose this prop. Instead they provide a `*Style` prop which is passed to child component `style` props.

As an example [react-native-material-textfield](https://github.com/n4kz/react-native-material-textfield) has 5 style props.
[](https://i.stack.imgur.com/020CK.png)

We use the `attrs` function to keep the organization of styles in one file with the rest of the styled components.

[](https://i.stack.imgur.com/cXGoX.png)

This doesn't allow the use of traditional css syntax for the pseudo component but it's the best we could think of to keep all styles organized.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2019-05-30 13:23:06

------------

The goal is to add new props/modify input props (as an HoC) only to a specific styled component.

Please notice:

> As of styled-components v4 the withComponent API is now a candidate
  for deprecation. In all likelihood, you probably want to use the new
  "as" prop to simply switch what element/component being rendered since
  the withComponent API is destructive toward styles if the
  lowest-wrapped component is a StyledComponent.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2020-10-06 13:46:04

------------

From the [documentation](https://styled-components.com/docs/faqs#how-can-i-override-inline-styles):
> 
# When to use attrs?

You can pass in attributes to styled components using attrs, but it is
not always sensible to do so.The rule of thumb is to use attrs when you want every instance of a
styled component to have that prop, and pass props directly when every
instance needs a different one:
```
const PasswordInput = styled.input.attrs(props => ({
  // Every <PasswordInput /> should be type="password"
  type: "password"
}))``

// This specific one is hidden, so let's set aria-hidden
<PasswordInput aria-hidden="true" />
const PasswordInput = styled.input.attrs(props => ({
  // Every <PasswordInput /> should be type="password"
  type: "password"
}))``
// This specific one is hidden, so let's set aria-hidden
<PasswordInput aria-hidden="true" />
```

> The same goes for props that can be inferred based on the "mode" of
another prop. In this case you can set a property on attrs to a
function that computes that prop based on other props.


------------
    
    