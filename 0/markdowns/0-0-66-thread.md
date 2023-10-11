
# Post \#55054337 [Link](https://stackoverflow.com/questions/55054337/)

## Cypress: run only one test

**Vote**: 231 (13/702) **Views**: 154182 (49/702) 

**Internal ID** \#0-0-66

Created at 2019-03-07 23:14:51

Tags: `javascript` `automated-tests` `cypress` `e2e-testing`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I want to toggle only running one test, so I don't have to wait for my other tests to see the result of one test.

Currently, I comment out my other tests, but this is really annoying.

Is there a way to toggle only running one test in `Cypress`?


----------
        
## Answer \#0

**Accepted** Vote: 352

Created at 2019-03-07 23:17:17

------------


### to run only one file


```
cypress run --spec path/to/file.spec.js
```

or using glob patterns:
```
cypress run --spec 'path/to/files/*.spec.js'
```

> Note: you need to  to avoid shell expansion!

### to run only one test in a file


You can use a `.only` as described in [the Cypress docs](https://docs.cypress.io/guides/core-concepts/writing-and-organizing-tests.html#Excluding-and-Including-Tests)
```
it.only('only run this one', () => {
  // similarly use it.skip(...) to skip a test
})

it('not this one', () => {
})
```

Also, you can do the same with `describe` and `context` blocks

### edit:


there's also a nice `VSCode` extension to make adding/removing `.only`'s easier with keyboard shortcuts. It's called Test Utils (install with `ext install chrisbreiding.test-utils`). It works with js, coffee, and typescript:
[](https://i.stack.imgur.com/2cKuQ.png)


------------
    
    
## Answer \#1

 Vote: 54

Created at 2019-06-09 03:56:26

------------

There are multiple ways of achieving this. 


1. You can add .onlyto it or describe see @bkucera answer
2. You can do it from the terminal as explained in the doc here  npx cypress run --record --spec "cypress/integration/my-spec.js"

 npm run cypress -- --record --spec "cypress/integration/my-spec.js"




------------
    
    
## Answer \#2

 Vote: 9

Created at 2020-03-16 14:16:57

------------

You can mute not needed test suites and particular cases by prepending `x` to testrunner methods call (`describe`, `it`, etc.)

So it would look like:

```
// this whole testsuite will be muted
xdescribe('Visit google', () => { 
  it('should visit google', () => { cy.visit('https://google.com/'); });
});

// this testsuite will run
describe('Visit youtube', () => {
  it('should visit youtube', () => { cy.visit('https://youtube.com/'); });

  // this testcase will be muted
  xit('is not necessary', () => { ... });
});
```



------------
    
    
## Answer \#3

 Vote: 8

Created at 2020-01-30 23:02:28

------------

You can run the test like this.

cypress run --spec **/file.js


------------
    
    
## Answer \#4

 Vote: 7

Created at 2022-01-10 12:14:46

------------

The best way to do such kind runs are by using the  keyword that cypress provide.
To run all the test cases in one describe function from many describe functions add the .only in the required describe.
```
describe("1st describe", () => {
  it("Should check xx", async function(){
  });
  it("Should check yy", async function(){
  });
});   
describe.only("2nd describe", () => {
  it("Should check xx", async function(){
  });
  it("Should check yy", async function(){
  });
}); 
describe("3rd describe", () => {
  it("Should check xx", async function(){
  });
  it("Should check yy", async function(){
  });
});
```

So here .
Similarly if you want to run some test cases in 1 describe add the .only in front of all the test cases that you want to run.
```
describe("describe statement", () => {
  it("Should check xx", async function(){
  });
  it.only("Should check yy", async function(){
  });
  it.only("Should check zz", async function(){
  });
});
```

So here the 
This is similar to the  that you might be familiar with.
You can skip the test in cypress with  or 


------------
    
    
## Answer \#5

 Vote: 5

Created at 2019-09-02 15:38:57

------------

There is one way I have found to skip tests which I don't need to run (in the current test), and that is to use: `this.skip();`

```
it('test page', function () {
    // skip this test for now
    this.skip();
    cy.visit('http://example.com/')
    cy.contains('test page').click()
    cy.url()
        .should('include', '/test-page/')
})
```






------------
    
    
## Answer \#6

 Vote: 1

Created at 2021-04-13 20:02:22

------------

My test files have a structure like this `path/something.test.jsx` and commands `npx cypress run --spec path/something.test.jsx` gives the following exception in the terminal:
```
Can't run because no spec files were found.
We searched for any files matching this glob pattern:
...
```

Surprisingly enough the following works and run the test exactly for one file (providing you have jest installed):
```
jest path/something.test.jsx
```



------------
    
    
## Answer \#7

 Vote: 1

Created at 2021-06-09 15:53:11

------------


1. A very easy solution is to prefix your tests in with numbers, as testing frameworks will typically will run tests in alpha/numeric order by default - so if I have to check one spec file - I will copy the contents into a file 0-[file-name].spec and re-run the test command. Once the test completes - you terminate the test run - as you will have the results you were looking for. This answer is targeted at projects where your testing framework is abstracted and as a developer, you do not have all available options for your testing framework. Not the best answer, but it works and is intuitive and super easy to do. I have found this to be a way to avoid adding a bunch of conditional skips() or only() calls that will not make it to production, will have to be removed and you can easily add the file pattern to .gitignore file so these local files do not get checked in.




------------
    
    
## Answer \#8

 Vote: 1

Created at 2022-10-31 16:33:02

------------

The best-known solution for that already exists and requires adding just one simple argument in the console.
[https://github.com/cypress-io/cypress/tree/develop/npm/grep](https://github.com/cypress-io/cypress/tree/develop/npm/grep)
Simply run:
npx cypress run --env grep="TestName" --spec "filename"
Cypress .only() function is used only for development.


------------
    
    
## Answer \#9

 Vote: 1

Created at 2023-01-12 11:44:45

------------

put .only for the test you want to execute and then run the spec as npx cypress run --spec path/to/your-file.spec.js


------------
    
    
## Answer \#10

 Vote: -1

Created at 2021-03-26 01:09:33

------------

You can use this
```
cypress run -- --spec 'path/to/files/*.spec.js'
```

or
```
npm run --spec 'path/to/files/*.spec.js'
```

It worked for me.
Many thanks


------------
    
    
## Answer \#11

 Vote: -1

Created at 2021-09-02 18:49:30

------------

use the @focus keyword in the test scripts when execute using cypress open


------------
    
    
## Answer \#12

 Vote: -2

Created at 2020-12-07 13:29:30

------------

To run a specific file through Terminal:
```
npx cypress run --record --spec "cypress/integration/my-spec.js"

 npm run cypress -- --record --spec "cypress/integration/my-spec.js"
```



------------
    
    