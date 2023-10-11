
# Post \#50508334 [Link](https://stackoverflow.com/questions/50508334/)

## When upgrading Angular 5 to 6, I get incompatible peer dependency (using ng update @angular/core)

**Vote**: 59 (107/702) **Views**: 102333 (82/702) 

**Internal ID** \#0-0-64

Created at 2018-05-24 11:34:08

Tags: `angular` `npm` `angular5` `angular6`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am trying to update my Angular app from v5 to v6 following [this guide](https://update.angular.io/).

I have ran all these commands successfully:

```
npm install -g @angular/cli
npm install @angular/cli
ng update @angular/cli
```


The problem is that I get an error when running this command:

```
ng update @angular/core
                  Package "@angular/flex-layout" has an incompatible peer dependency to "rxjs" (requires "^5.5.0", would install "6.2.0").
                  Package "@angular/compiler-cli" has an incompatible peer dependency to "typescript" (requires ">=2.7.2 <2.8", would install "2.6.2")
Incompatible peer dependencies found. See above.
```


I am not sure how to handle this & I don't want to try things on my own to avoid breaking the app.

Can someone please advise what to do?

My current dependencies are as follows:

```
{
 ....
  },
  "private": true,
  "dependencies": {
    "@angular/animations": "^5.2.10",
    "@angular/cdk": "^5.2.5",
    "@angular/common": "^5.2.10",
    "@angular/compiler": "^5.2.10",
    "@angular/core": "^5.2.10",
    "@angular/flex-layout": "^5.0.0-beta.14",
    "@angular/forms": "^5.2.10",
    "@angular/http": "^5.2.10",
    "@angular/material": "^5.2.5",
    "@angular/platform-browser": "^5.2.10",
    "@angular/platform-browser-dynamic": "^5.2.10",
    "@angular/router": "^5.2.10",
    "@ngx-translate/core": "^9.1.1",
    "@ngx-translate/http-loader": "^3.0.1",
    "core-js": "^2.5.5",
    "hammerjs": "^2.0.8",
    "primeng": "^5.2.4",
    "rxjs": "^5.5.11",
    "zone.js": "^0.8.26"
  },
  "devDependencies": {
    "@angular-devkit/build-angular": "~0.6.3",
    "@angular/cli": "^6.0.3",
    "@angular/compiler-cli": "^5.2.11",
    "@angular/language-service": "^5.2.10",
    "@types/jasmine": "~2.8.3",
    "@types/jasminewd2": "~2.0.2",
    "@types/node": "^6.0.106",
    "codelyzer": "^4.3.0",
    "jasmine-core": "~2.8.0",
    "jasmine-spec-reporter": "~4.2.1",
    "karma": "^2.0.2",
    "karma-chrome-launcher": "~2.2.0",
    "karma-coverage-istanbul-reporter": "^1.2.1",
    "karma-jasmine": "~1.1.0",
    "karma-jasmine-html-reporter": "^0.2.2",
    "protractor": "^5.3.2",
    "ts-node": "~4.1.0",
    "tslint": "~5.9.1",
    "typescript": "~2.6.2"
  }
}
```



----------
        
## Answer \#0

**Accepted** Vote: 32

Created at 2018-05-24 12:17:49

------------

Hope this will help someone else, I finally could solve the issue by manually updating some of the angular packages (using npm)

```
npm i @angular/cdk@6 @angular/compiler-cli@6 @angular/material@6 rxjs@6 --save
```


After that the ng update command could work (with some warnings, but at least we passed the ng update @angular/core)

```
ng update @angular/core
    Updating package.json with dependency @angular/language-service @ "6.0.3" (was "5.2.10")...
    Updating package.json with dependency @angular/animations @ "6.0.3" (was "5.2.10")...
    Updating package.json with dependency @angular/forms @ "6.0.3" (was "5.2.10")...
    Updating package.json with dependency @angular/common @ "6.0.3" (was "5.2.10")...
    Updating package.json with dependency @angular/compiler @ "6.0.3" (was "5.2.10")...
    Updating package.json with dependency @angular/core @ "6.0.3" (was "5.2.10")...
    Updating package.json with dependency @angular/router @ "6.0.3" (was "5.2.10")...
    Updating package.json with dependency @angular/platform-browser-dynamic @ "6.0.3" (was "5.2.10")...
    Updating package.json with dependency @angular/platform-browser @ "6.0.3" (was "5.2.10")...
    Updating package.json with dependency @angular/http @ "6.0.3" (was "5.2.10")...
UPDATE package.json (1587 bytes)
npm WARN @angular/compiler-cli@6.0.3 requires a peer of typescript@>=2.7.2 <2.8 but none is installed. You must install peer dependencies yourself.
npm WARN @angular/flex-layout@5.0.0-beta.14 requires a peer of @angular/cdk@^5.0.0 but none is installed. You must install peer dependencies yourself.
npm WARN @angular/flex-layout@5.0.0-beta.14 requires a peer of @angular/core@^5.0.0 but none is installed. You must install peer dependencies yourself.
npm WARN @angular/flex-layout@5.0.0-beta.14 requires a peer of @angular/common@^5.0.0 but none is installed. You must install peer dependencies yourself.
npm WARN @angular/flex-layout@5.0.0-beta.14 requires a peer of rxjs@^5.5.0 but none is installed. You must install peer dependencies yourself.
npm WARN @ngtools/webpack@6.0.3 requires a peer of typescript@~2.4.0 || ~2.5.0 || ~2.6.0 || ~2.7.0 but none is installed. You must install peer dependencies yourself.
npm WARN @ngx-translate/http-loader@3.0.1 requires a peer of @ngx-translate/core@>=10.0.0 but none is installed. You must install peer dependencies yourself.
npm WARN tsickle@0.27.5 requires a peer of typescript@>=2.4.2 <2.8 but none is installed. You must install peer dependencies yourself.

updated 10 packages in 57.253s
```



------------
    
    
## Answer \#1

 Vote: 55

Created at 2018-06-07 10:09:56

------------

A work around for this would be to add a `--force` flag.

```
ng update @angular/core --force
```



------------
    
    
## Answer \#2

 Vote: 16

Created at 2018-10-11 20:53:45

------------

The issue was solved for me after making a forced update like the following:-
```
ng update --all --force
```

It basically avoids/ignores all the dependency-checks and updates all the packages wherever applicable. , I had to do the update this way as other suggestions seemed to be not working for me.


------------
    
    
## Answer \#3

 Vote: 4

Created at 2018-07-25 19:57:58

------------

I was able to get around messages about incompatible peers by using:

ng update --all

It updated all the packages that needed to be updated.


------------
    
    
## Answer \#4

 Vote: 2

Created at 2021-01-18 02:29:51

------------

Running --force is not the right option. since it is complaining about rxjs and typescript, you need to upgrade that first. Once that's complete, you can run the angular update command


------------
    
    
## Answer \#5

 Vote: 0

Created at 2020-04-08 09:07:19

------------

Don't force update, that will later present unexpected results. Use the following npm install instead and try again. 

```
npm install -g @angular-devkit/schematics-cli
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2020-07-23 15:39:15

------------

You can try:
```
npm i --save-dev @angular/cli@version @angular/core@version
```

Then you can run `ng update --all`
This worked for me.
I got `Incompatible peer dependencies found. See above.` while updating `@angular/cli` and `@angular/core` using `ng update --all`.


------------
    
    
## Answer \#7

 Vote: 0

Created at 2020-09-15 12:05:53

------------

this command worked in my case :
```
npm install --force @angular/cli@version @angular/core@version
```

here version could be 6,7,8,9,10 on which you to upgrade


------------
    
    