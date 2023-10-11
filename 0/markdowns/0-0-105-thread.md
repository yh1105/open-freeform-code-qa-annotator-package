
# Post \#66217682 [Link](https://stackoverflow.com/questions/66217682/)

## Should I use both Cypress and Jest together?

**Vote**: 58 (113/702) **Views**: 35984 (205/702) 

**Internal ID** \#0-0-105

Created at 2021-02-16 01:45:47

Tags: `vue.js` `jestjs` `cypress`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm learning both Jest and Cypress at same time. I know that they are not direct competitors because Cypress is focused on E2E and Jest on unit testing. For now I have implemented in my project both Jest and Cypress with few tests.
But actually most things I can test in both Cypress and Jest, and often I have hard time to decide with what write my test. Also it is harder to maintain compared to single test library.
I'm wondering - how often is Cypress (or alternative) and Jest (or alternative) used together? Is it really standard and good practice to use both? Or most developers/teams sticks with single one solution and it is fine?
: It was a long time since this question was asked. I got compromise that was also suggested in comment. Instead using only Cypress or Cypress + Jest, I'm using Cypress + new Cypress Component Testing (so no Jest). Thanks to that I'm having same library and assertions (easier to manage) but can test both e2e and components.


----------
        
## Answer \#0

**Accepted** Vote: 77

Created at 2021-04-16 14:29:50

------------

short answers: It's very common to use Jest and Cypress in the same codebase.

With component Libraries like Vue and React, the line between integration and unit tests can get a bit fuzzy. We can even use the same tools(Jest & Cypress)for both cases, which makes things even more confusing. I recommend that you aim to test "user stories" or, in other words, make sure that users can always perform key actions. For example:

1. Can the user fill and submit a form?
2. Can the user add products to the cart?
3. Does the hamburger menu responds to clicks?


Some of these tests will involve one component, others will involve two and some will require the entire application. I prefer writing smaller tests(unit and integration) using Jest and [testing library](https://testing-library.com/) because of the quick feedback loop. I get to develop and run my tests at, almost, the same time.
Eventually, you'll run into cases that involve so many moving parts(components) that using Jest is not an option. This is where Cypress shines, it's great for testing your end-to-end workflows.


------------
    
    
## Answer \#1

 Vote: 7

Created at 2022-06-29 15:16:30

------------

This is an old question, but wanted to provide additional clarity for why you would use both Cypress and Jest. I have used both on multiple projects. They each address separate testing concerns:

### Jest


For running unit tests. Unit tests uphold  or just that ensure your methods return the expected value for lots of different cases. Generally these tests are for methods in services, but not limited to them.

### Cypress


For running end-to-end, or functional tests. Cypress is for  by interacting with it and making assertions on it. Calls to the backend are generally stubbed, meaning you control the response in the test, because Cypress is not for testing the backend.
Together, Cypress and Jest provide a complete testing solution for the frontend.


------------
    
    
## Answer \#2

 Vote: 3

Created at 2022-06-29 15:03:39

------------

I use cypress for e2e tests and component testing. I test the behaiviors of those types with cypress. With jest i do unit testing. With jest i test logic/business-logic in the frontend.


------------
    
    