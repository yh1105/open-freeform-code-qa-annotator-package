
# Post \#72414377 [Link](https://stackoverflow.com/questions/72414377/)

## How to test element's width in cypress when the calculated width isn't accurate?

**Vote**: 2 (631/702) **Views**: 887 (667/702) 

**Internal ID** \#0-1-146

Created at 2022-05-28 09:13:35

Tags: `css` `testing` `cypress` `e2e-testing` `rounding-error`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

Let's say I have a `div` in the DOM with `'200px'` of width and a `class` of `'target'`.
In order to test its width with cypress, I should write the code below:
```
cy.get('.target').should('have.css', 'width', '200px');
```

and there's a bunch of tests, testing widths and heights of elements...
...
Today something weird happened!
All these tests have failed because the value of `width` that cypress found was `200.0000000000032038879956012px` instead of `200px` !!!
The first solution that came into my mind was to test them on the actual numbers (ex, `200`), instead of the string (ex, `'200px'`) that the cypress found; which I think is an expensive idea!



----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2022-05-28 12:08:40

------------

You can match without any conversion,
```
cy.get('div')
  .invoke('css', 'width')
  .should('match', /200.*px/)   // matches 200.0000000000032038879956012px or 200px
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-05-28 11:35:13

------------

The test assertion `.should('have.css', ...)` is a wrapper for [Window.getComputedStyle()](https://developer.mozilla.org/en-US/docs/Web/API/Window/getComputedStyle) which reads the actual rendered value after all CSS has been applied.
It won't always correspond to the "input" setting applied in an inline style or via a style sheet, depending on other settings like justification, flex settings, media queries.
From a test perspective, you can switch to testing the "input" parameters,
- `.should('have.attr', 'style', 'width: 200px')`- `.should('have.class', 'target')`

---


Alternatively use the `chai-almost` plugin to check close numeric values.
```
const chaiAlmost = require('chai-almost');
chai.use(chaiAlmost(1))                           // 1 pixel variance

cy.get('.target')
  .invoke('css', 'width')                         // extract computed width 
  .then(widthStr => +widthStr.replace('px', ''))  // convert to number
  .should('almost.equal', 200);                   // approximate 200 +/- 1 px
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-05-28 11:43:00

------------

You can convert the 200px first into a number and then check that the number is in the range. Something like:
```
cy.get('.target')
  .invoke('css', 'width')
  .then((width) => {
    expect(+width.replace('px', '')).to.be.within(200, 201)
  })
```



------------
    
    