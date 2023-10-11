
# Post \#72656551 [Link](https://stackoverflow.com/questions/72656551/)

## React check if element is visible on viewport

**Vote**: 0 (696/702) **Views**: 4978 (521/702) 

**Internal ID** \#0-0-32

Created at 2022-06-17 08:39:15

Tags: `reactjs`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am trying to identify on scroll if the div is visible on viewport. I am shring the code below:
```
<div id="parent">
     data.map(item => {
             <div id={item.id}>data.title</div>
         }
 <div>
```

Now I want to get the list of divs inside of #parent which are visible on viewport on scroll.


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2022-06-28 11:50:20

------------

This helped me:
```
<div id="parent">
 data.map(item => {
         <div id={`id_${item.id}`}>data.title</div>
     }
 <div>
```

and
```
const displayIds = (target) => {
       console.log(target.replace("id_", ""));
  }

 const myScrollHandler = debounce(() => {
    data.map(
        (item: any) => {
            const target = 
            document.querySelector(`#id_${item.id}`);
            const top = target?.getBoundingClientRect().top || 0;
            top >= 0 && top <= window.innerHeight ? displayIds(target);
        }
    );
}, 500);

 useEffect(() => {
    document.addEventListener("scroll", myScrollHandler);
    return () => document.removeEventListener("scroll", myScrollHandler);
}, [data]);
```

Now for every scroll I have list of id's associated with div's which is visible on viewport.


------------
    
    
## Answer \#1

 Vote: 4

Created at 2022-06-17 08:57:03

------------

You can install the 'react-intersection-observer' module from the npm to do the trick. [Read more](https://www.npmjs.com/package/react-intersection-observer) from here. They have the 'inView' hook that could solve your problem.
First import the hook.
```
import { useInView } from "react-intersection-observer";

  const [ref, inView] = useInView({
    /* Optional options */
    triggerOnce: true,
    rootMargin: '0px 0px',
  })
```

here, we can ref our element by
```
<div ref={ref}></div>
```

and the inView returns true when it is visible on the viewport and false when it is not.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-08-15 15:56:06

------------

`React-Intersection-Observer` using with 
- Install package `npm i react-intersection-observer`- Create new file `MultipleObserver.js`
```
// MultipleObserver.js
import { useInView } from 'react-intersection-observer';

const MultipleObserver = ( {children} ) => {

    const { ref, inView } = useInView({ triggerOnce: true });

    return (
             <div ref={ref}>    
               { inView ? <span>{children}</span> : 'Loading...' }
             </div>
          )
}

export default MultipleObserver;
```

Now you can use multiple divs in the same components see example `MyComponent.js`
```
// MyComponent.js
import React from 'react';
import MultipleObserver from "MultipleObserver.js";

const MyComponent = ( props ) => {
    return (
        <div className="main">

           <h2>Page Content</h2>

           <MultipleObserver>
              <img src="large-image.png" />
           </MultipleObserver>

           <MultipleObserver>
              <iframe src="domain.com/large-video.mp4"></iframe>
           </MultipleObserver>
          
           <p>Another content</p>

        </div>
}

export default MyComponent
```



------------
    
    