
# Post \#73165823 [Link](https://stackoverflow.com/questions/73165823/)

## How can select 1 element from the 8 elements matching my selectors. [Cypress test]

**Vote**: 0 (696/702) **Views**: 691 (684/702) 

**Internal ID** \#0-1-142

Created at 2022-07-29 11:15:02

Tags: `css` `css-selectors` `cypress` `ui-automation`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

I am trying to verify the heading of a section when running a test, using this locater `[data-cy="offerBreakUpTableHeaderCellRenderer"] .flex span:first-child`
I am getting 4 elements I want to select the first one**(Joined)** of the 4 elements
[](https://i.stack.imgur.com/fH3a9.png)


----------
        
## Answer \#0

**Accepted** Vote: 0

Created at 2022-07-29 11:19:22

------------

You can do something like this. `eq(0)` will get the first `[data-cy="offerBreakUpTableHeaderCellRenderer"]` and then `within` will make sure that the `Joined` element only within the `[data-cy="offerBreakUpTableHeaderCellRenderer"]` is selected.
```
cy.get('[data-cy="offerBreakUpTableHeaderCellRenderer"]')
  .eq(0)
  .within(() => {
    cy.contains('span', 'Joined').click()

    //Checking number as a string
    cy.contains('span', 'Joined').next().should('have.text', '1')

    //checking number as a number
    cy.contains('span', 'Joined')
      .next()
      .invoke('text')
      .then((num) => +num)
      .should('eq', 0 + 1)
  })
```

If you want to check them independently, you can do this:
```
cy.get('[data-cy="offerBreakUpTableHeaderCellRenderer"]')
  .eq(0)
  .within(() => {
    cy.contains('span', 'Joined').click()

    //Checking number as a string
    cy.get('span[class*="flex"] span').eq(1).should('have.text', '1')

    //checking number as a number
    cy.get('span[class*="flex"] span')
      .eq(1)
      .invoke('text')
      .then((num) => +num)
      .should('eq', 0 + 1)
  })
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-07-29 11:58:02

------------

```
cy.get("[data-cy=offerBreakUpTableHeaderCellRenderer]>span")
    .eq(0)
    .should('contain', 'Joined')
    .click()
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-07-29 23:07:54

------------

Using a `.eq()` in this instance can be not as readable when debugging if it were to fail.
[.contains()](https://docs.cypress.io/api/commands/contains) is a powerful command that allows you search for the first element containing text with an optional selector.
For your use, it would be better to use the `data-cy` attribute with the text of the first header `Joined`.
```
// returns the for 'Joined' header
// you can drill down to it's child elements
cy.contains("[data-cy=offerBreakUpTableHeaderCellRenderer]", 'Joined')
  .should('be.visible')
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-07-29 23:20:38

------------

In your case, you have the same locator for 4 elements so you can use text within these locators to get the unique element. Apart from already provided solutions, you can also use the below snippet
```
cy.get('[data-cy="offerBreakUpTableHeaderCellRenderer"]').contains('Joined')
```

and if you want to check if the next no is 1
```
cy.get('[data-cy="offerBreakUpTableHeaderCellRenderer"]').contains('Joined').next().should('have.text', '1')
```

Here you have to replace the text that you are looking for and don't have to worry about the order of element


------------
    
    