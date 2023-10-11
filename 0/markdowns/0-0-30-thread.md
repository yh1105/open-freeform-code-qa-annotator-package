
# Post \#64658031 [Link](https://stackoverflow.com/questions/64658031/)

## Which Enzyme adapter works with React 17?

**Vote**: 73 (86/702) **Views**: 58756 (143/702) 

**Internal ID** \#0-0-30

Created at 2020-11-03 06:55:03

Tags: `reactjs` `unit-testing` `enzyme`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have a React app, and I want to start writing unit tests with Enzyme. Enzyme's [documentation](https://enzymejs.github.io/enzyme/) discusses versions of React up to 16.
In `src/setupTests.js` I currently have
```
import Enzyme from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

Enzyme.configure({ adapter: new Adapter() });
```

But my app uses React version 17.0.1. 


----------
        
## Answer \#0

**Accepted** Vote: 110

Created at 2020-11-03 06:55:03

------------

> 

If you have , you can use this [unofficial adapter for React 17 for enzyme](https://www.npmjs.com/package/@wojtekmaj/enzyme-adapter-react-17).
```
// src/setupTests.js
import { configure } from 'enzyme';
import Adapter from '@wojtekmaj/enzyme-adapter-react-17';

configure({ adapter: new Adapter() });
```

See this [GitHub issue](https://github.com/enzymejs/enzyme/issues/2429) for more information on the unofficial adapter.


------------
    
    
## Answer \#1

 Vote: 6

Created at 2021-03-05 10:30:42

------------

I tried with unofficial adapter '@wojtekmaj/enzyme-adapter-react-17' and it works fine. 

1. clear the npm cache (clearing this may make npm slower, but give it a try).


- `npm cache verify``npm cache clean --force`

1. remove your


- `node_modules``package-lock.json`

1. update npm dependencies in your package.json with "@testing-library/react": "^11.2.5","@wojtekmaj/enzyme-adapter-react-17": "^0.4.1","react": "^17.0.1","react-dom": "^17.0.1",
2. npm i --legacy-peer-deps
3. as @wojtekmaj/enzyme-adapter-react-17 mentioned making a change in the test file.


- `'@wojtekmaj/enzyme-adapter-react-17';`- `({ adapter: new Adapter() });`


------------
    
    
## Answer \#2

 Vote: 4

Created at 2021-04-25 09:54:02

------------

add this code in setupTests.js file
```
// jest-dom adds custom jest matchers for asserting on DOM nodes.
// allows you to do things like:
// expect(element).toHaveTextContent(/react/i)
// learn more: https://github.com/testing-library/jest-dom
import '@testing-library/jest-dom';
import Enzyme from 'enzyme';
import Adapter from '@wojtekmaj/enzyme-adapter-react-17';

Enzyme.configure({ adapter: new Adapter() });
```

[](https://i.stack.imgur.com/zugig.png)


------------
    
    
## Answer \#3

 Vote: 3

Created at 2022-04-08 04:41:17

------------

I've tried [@wojtekmaj/enzyme-adapter-react-17](https://www.npmjs.com/package/@wojtekmaj/enzyme-adapter-react-17) for React 17, and it does work just fine.
After using `npm install --save-dev @wojtekmaj/enzyme-adapter-react-17` you also need to set up enzyme to use the adapter you want it to use, you do so by adding the top level configure API:
```
/* src/setupTests.js */
import Enzyme from 'enzyme';
import Adapter from '@wojtekmaj/enzyme-adapter-react-17';

Enzyme.configure({ adapter: new Adapter() });
```



------------
    
    