
# Post \#52321539 [Link](https://stackoverflow.com/questions/52321539/)

## React — Passing props with styled-components

**Vote**: 58 (113/702) **Views**: 155478 (48/702) 

**Internal ID** \#0-0-20

Created at 2018-09-13 20:42:20

Tags: `javascript` `reactjs` `styled-components`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I just read in the `styled-components` [documentation](https://www.styled-components.com/docs/basics#passed-props) that the following is wrong and it will affect render times. If that is the case, how can I refactor the code and use the required props to create a dynamic style?

Thank you in advance.



```
import React from 'react'
import styled from 'styled-components'

const Tab = ({ onClick, isSelected, children }) => {
    const TabWrapper = styled.li`
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 100px;
    margin: 1px;
    font-size: 3em;
    color: ${props => (isSelected ? `white` : `black`)};
    background-color: ${props => (isSelected ? `black` : `#C4C4C4`)};
    cursor: ${props => (isSelected ? 'default' : `pointer`)};
`

    return <TabWrapper onClick={onClick}>{children}</TabWrapper>
}


export default Tab
```



----------
        
## Answer \#0

**Accepted** Vote: 61

Created at 2018-09-13 21:29:11

------------

I believe what the documentation is saying is that you should avoid including your styles inside of the rendering component:

DO THIS

```
const StyledWrapper = styled.div`
  /* ... */
`

const Wrapper = ({ message }) => {
  return <StyledWrapper>{message}</StyledWrapper>
}
```


INSTEAD OF THIS

```
const Wrapper = ({ message }) => {
  // WARNING: THIS IS VERY VERY BAD AND SLOW, DO NOT DO THIS!!!
  const StyledWrapper = styled.div`
    /* ... */
  `

  return <StyledWrapper>{message}</StyledWrapper>
}
```


Because what happens is when the component's Props changes, then the component will re-render and the style will regenerate. Therefore it makes sense to keep it separate.

So if you read further on to the  section, they explain this:

```
const Button = styled.button`
  /* Adapt the colours based on primary prop */
  background: ${props => props.primary ? "palevioletred" : "white"};
  color: ${props => props.primary ? "white" : "palevioletred"};

  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;
`;

// class X extends React.Component {
//  ...

render(
  <div>
    <Button>Normal</Button>
    <Button primary>Primary</Button>
  </div>
);

// }
```


this works because when you use the  component in class X, it will know the props of class X without you having to tell it anything.

For your scenario, I imagine the solution would be simply:

```
const TabWrapper = styled.li`
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 100px;
  margin: 1px;
  font-size: 3em;
  color: ${props => (props.isSelected ? `white` : `black`)};
  background-color: ${props => (props.isSelected ? `black` : `#C4C4C4`)};
  cursor: ${props => (props.isSelected ? 'default' : `pointer`)};
`;

const Tab = ({ onClick, isSelected, children }) => {
  return <TabWrapper onClick={onClick}>{children}</TabWrapper>
}

const X = <Tab onClick={() => console.log('clicked')} isSelected>Some Children</Tab>
```


I haven't tested this at all, so please feel free to try it out and let me know if it works for you or whatever worked for you!


------------
    
    
## Answer \#1

 Vote: 42

Created at 2020-11-17 09:22:22

------------

You can pass an argument with Typescript as follows:
```
<StyledPaper open={open} />    

...

const StyledPaper = styled(Paper)<{ open: boolean }>`
   top: ${p => (p.open ? 0 : 100)}%;
`;
```



------------
    
    
## Answer \#2

 Vote: 37

Created at 2021-07-23 19:03:06

------------

Another way to do it would be
```
const StyledDiv = styled.div.attrs((props: {color: string}) => props)`
    width: 100%;
    height: 100%;
    background-color: ${(props) => props.color};
`

//...

render() {
    return (
        <StyledDiv color="black">content...</StyledDiv>
    );
}
```

This way you are type-safe in terms of the props you want to send into the styled component. (Good when coding in Typescript)


------------
    
    
## Answer \#3

 Vote: 5

Created at 2021-01-14 01:04:35

------------

For a more simple example with functional components:
Suppose you have an arrow like polygon and you need 2 of them pointing in different directions. So you can pass the rotate value by props
```
<Arrow rotates='none'/>
          
               <Arrow rotates='180deg'/>
```


```
import React from 'react';
    import styled from "@emotion/styled";

    const ArrowStyled = styled.div`
      background-color: rgba(255,255,255,0.9);
      width: 24px;
      height: 30px;
      clip-path: polygon(56% 40%,40% 50%,55% 63%,55% 93%,0% 50%,56% 9%);
      transform: rotate(${props => props.rotates});
     `


const Arrow = ({rotates}) => {
    return (
       <ArrowStyled rotates={rotates}/>
    );
}

export default Arrow;
```



------------
    
    
## Answer \#4

 Vote: 4

Created at 2021-02-06 15:51:10

------------

If you're using Typescript create an interface inside your styles file!
Otherwise, you won't be able to access props in your CSS
```
import styled from 'styled-components'

interface StyledLiProps{
  selected: boolean
}

export const TabWrapper = styled.li`
    
// styles ...

color: ${props => (selected ? `white` : `black`)};
background-color: ${props => (selected ? `black` : `#C4C4C4`)};
`
```

And don`t forget to declare the props you want to use in your CSS inside your JSX
```
interface TabProps{
  text: string;
}

const Tab = ({ text }: TabProps) => {
 
//...
  
return <TabWrapper selected={isSelected} onClick={() => updateTab}>{text}</TabWrapper>


}
```



------------
    
    
## Answer \#5

 Vote: 3

Created at 2018-09-13 20:48:03

------------

Consider styled components documentation gives example of using reacts context api [2] for different themes. 

[1] [https://www.styled-components.com/docs/advanced](https://www.styled-components.com/docs/advanced)

[2] [https://reactjs.org/docs/context.html](https://reactjs.org/docs/context.html)


------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-06-01 12:49:25

------------

Exporting styled-component
> Button
and passing `scrollPosition` as a prop in 
> PassingPropsToSyledComponent
```
import styledComponents from "styled-components";
            
export const Button = styledComponents.div`
       position: ${ props => props.scrollPosition ? 'relative' : 'static' };
`;
export const PassingPropsToSyledComponent = ()=> {
   return(
   <Button scrollPosition={scrollPosition}>
       Your Text Here
   </Button>
   )
}
```



------------
    
    