
# Post \#52491253 [Link](https://stackoverflow.com/questions/52491253/)

## How to get div 'text' value in Cypress test using jquery

**Vote**: 31 (190/702) **Views**: 135327 (59/702) 

**Internal ID** \#0-0-10

Created at 2018-09-25 05:39:24

Tags: `javascript` `jquery` `cypress`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

Using Jquery in Cypress.io test, how to get the div 'text' value called 'Wildness' from the below html tag. I have tried below in my Cypress test, but it is throwing undefined in console. 

```
const $divText = Cypress.$('.ibxudA .WildnessText-kRKTej').text()
         cy.wrap($divText)
           .should("eq", "Wildness")
```


```
<div class="sc-bMvGRv_onetwo">
<div>
<div class="abLeftSection">
<div class="abNewBtn-fTLJBK">
<button class="ifAKCX ohpWT" type="button">New</button>
</div>
<div class="kpLvEV" style="">
<div class="cWzXYZ">
<div class="OgGUG">
<div class="jsnnAD">
<svg class="dFvKsA"></svg>
</div>
</div>
<div class="ibxudA">First</div>
</div>
<div class="kpLvEV" style="">
<div class="bGADLM"><div class="OgGUG">
<div class="jsnnAD">
<svg class="dFvKsA"></svg>
</div>
</div>
<div class="ibxudA">
<div class="WildnessText-kRKTej" title="Wildness">Wildness</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
```




----------
        
## Answer \#0

**Accepted** Vote: 70

Created at 2018-09-27 12:34:19

------------

I might try this:
```
cy.get(".ibxudA").find('.WildnessText-kRKTej').should('have.text',"Wildness")
```


```
cy.get(".ibxudA").find('.WildnessText-kRKTej').invoke('text').then((text) => {
    expect(text.trim()).equal('Wildness')
});
```

This might be a similar question: [How do you check the equality of the inner text of a element using cypress?](https://stackoverflow.com/questions/52430983/how-do-you-check-the-equality-of-the-inner-text-of-a-element-using-cypress)


------------
    
    
## Answer \#1

 Vote: 20

Created at 2020-04-15 09:00:44

------------

```
cy.get("WildnessText-kRKTej").then(function($elem) {
     cy.log($elem.text())
})
```



------------
    
    
## Answer \#2

 Vote: 10

Created at 2020-01-21 07:49:56

------------

One line validation: 

Syntax:

```
cy.get(<selector>).invoke("text").should("eq", <your string>);
```


Example:

```
cy.get("div.myclass").invoke("text").should("eq", "My Text");
```


Here, following method is used for retrieving text. `should()` is used for validation.

```
cy.get('element').invoke('text')
```



------------
    
    
## Answer \#3

 Vote: 6

Created at 2020-02-06 08:10:18

------------

```
it('get text', async () => {
      const text = await new Cypress.Promise<string>((resolve) => {
        cy.get('[data-testid="target"')
          .invoke('text')
          .then((txt) => resolve(txt.toString()))
      })

      cy.log(text)
    })
```



------------
    
    
## Answer \#4

 Vote: 4

Created at 2019-10-17 06:22:32

------------

I would suggest you use inbuilt cypress API 'contains' like this: 

```
cy.contains('Wildness')
```


It will select the whole HTML tag for you. Hope it helps...


------------
    
    
## Answer \#5

 Vote: 3

Created at 2020-07-18 20:51:58

------------

As of July 2020, from Cypress docs ([https://docs.cypress.io/faq/questions/using-cypress-faq.html#How-do-I-get-an-element%E2%80%99s-text-contents](https://docs.cypress.io/faq/questions/using-cypress-faq.html#How-do-I-get-an-element%E2%80%99s-text-contents)):
```
cy.get('div').should(($div) => {
  const text = $div.text()

  expect(text).to.match(/foo/)
  expect(text).to.include('foo')
  expect(text).not.to.include('bar')
})
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2018-09-25 05:47:02

------------

```
cy.get('.ibxudA .WildnessText-kRKTej').invoke('text').then((yourDivText) => {
   expect(yourDivText.toString().toLowerCase()).to.contain('wildness');
});
```



------------
    
    