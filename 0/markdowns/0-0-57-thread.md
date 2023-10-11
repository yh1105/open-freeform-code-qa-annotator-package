
# Post \#49669654 [Link](https://stackoverflow.com/questions/49669654/)

## Angular 5 ngHide ngShow [hidden] not working

**Vote**: 44 (143/702) **Views**: 192627 (41/702) 

**Internal ID** \#0-0-57

Created at 2018-04-05 10:07:19

Tags: `javascript` `angular`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

Good day, guys!

I am trying to make my Angular 5 app hide elements (or show hidden). However, this seems to not work.

I've tried ngHide, ng-hide, ngShow, ng-show, [hidden] methods - none of them works.

My login.component.ts looks like this:

```
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.css']
})

export class LoginComponent implements OnInit {
  isHidden: boolean = true;
  input_pw: string = 'password';

  constructor() { }

  ngOnInit() {
    console.log(this.isHidden); //console shows true
  }

}
```


And login.component.html:

```
<div class="container">

  <div class="cont-login">
    <div class="login-pane">

      <div>
        <p class="inner log-labels">Your password</p>
        <input class="txt" type="password" [(ngModel)]="input_pw" ngHide="isHidden">
      </div>

      <div>
        <input class="btn" type="submit" value="Login">
      </div>

    </div>


  </div>

</div>
```


Am I doing something wrong here?

NOTE: I did not change or add anything related to css.


----------
        
## Answer \#0

**Accepted** Vote: 28

Created at 2018-04-05 10:14:46

------------

 I realize that this is somewhat bad advice. As the first comment states, this heavily depends on the situation, and it is  a bad practice to use the [hidden] attribute: see the comments for some of the cases where you need to use it and not `*ngIf` 



You should always try to use `*ngIf` instead of `[hidden]`.

```
<input *ngIf="!isHidden" class="txt" type="password" [(ngModel)]="input_pw" >
```


There are several [blog posts about](http://www.talkingdotnet.com/dont-use-hidden-attribute-angularjs-2/) that topics, but the bottom line is, that Hidden usually means you do not want the browser to render the object - using angular you still waste resource on rendering it, and it will end up in the DOM anyway (and tricky users can see it with basic browser manipulation). 


------------
    
    
## Answer \#1

 Vote: 48

Created at 2018-04-05 10:13:22

------------

If you want to just toggle visibility and still keep the input in DOM:

```
<input class="txt" type="password" [(ngModel)]="input_pw" 
 [style.visibility]="isHidden? 'hidden': 'visible'">
```


The other way around is as per answer by rrd, which is to use HTML hidden attribute. In an HTML element if `hidden` attribute is set to `true` browsers are supposed to hide the element from display, but the problem is that this behavior is overridden if the element has an explicit `display` style mentioned.

```
.hasDisplay {
  display: block;
}
```
```
<input class="hasDisplay" hidden value="shown" />
<input hidden value="not shown">
```



To overcome this you can opt to use an explicit css for `[hidden]` that overrides the display;

```
[hidden] {
  display: none !important;
}
```


Yet another way is to have a `is-hidden` class and do:

```
<input [class.is-hidden]="isHidden"/>

.is-hidden {
      display: none;
 }
```


`display: none``visibility: hidden`

The important thing is to use one way across an application rather than mixing different ways thereby making the code less maintainable.

If you want to remove it from DOM

```
<input class="txt" type="password" [(ngModel)]="input_pw" *ngIf="!isHidden">
```



------------
    
    
## Answer \#2

 Vote: 37

Created at 2018-04-05 10:11:12

------------

Your [hidden] will work but you need to check the css:

```
<input class="txt" type="password" [(ngModel)]="input_pw" [hidden]="isHidden" />
```


And the css:

```
[hidden] {
  display: none !important;
}
```


That should work as you want.


------------
    
    
## Answer \#3

 Vote: 4

Created at 2019-06-21 03:59:51

------------

If you can not use *ngif,  [class.hide]  works in angular 7.
example:

```
<mat-select (selectionChange)="changeFilter($event.value)" multiple [(ngModel)]="selected">
          <mat-option *ngFor="let filter of gridOptions.columnDefs"
                      [class.hide]="filter.headerName=='Action'"  [value]="filter.field">{{filter.headerName}}</mat-option>
        </mat-select>
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2021-01-26 07:20:57

------------

If you add  to div, the actual thing that happens is adding a class [hidden] to this element conditionally with 
Please check the style of the element in the browser to ensure no other style affect the  property of an element like this:
[](https://i.stack.imgur.com/nXPMe.png)
If you found  of  class is overridden, you need to add this css code to your style:
```
[hidden] {
    display: none !important;
}
```



------------
    
    
## Answer \#5

 Vote: 1

Created at 2021-05-17 20:35:39

------------


1. You can use [style.visibility]="isHidden ? 'hidden': 'visible'". This will hide your element, but it will still take the same space in DOM and affect a page layout.
2. Another option is using [hidden]="condition" in your templates. This will add [hidden] attribute to the element. A browser will apply display: hidden to this element only if there's no display property set explicitly in your css (for this element by id, element's class or element's tag etc). Otherwise, you should set [hidden] { display: none !important; }. This will completely hide element which will no longer take space in your layout.
3. Also you can conditionally set class on the element [class.is-hidden]="condition", then in your css in class .is-hidden you can use any css rules you want: .is-hidden { display: none; } or .is-hidden { visibility: hidden; } or accessible and layout-friendly alternative to display: none (not readable by screen readers) and visibility: hidden (takes up physical space in the DOM) (source https://github.com/30-seconds/30-seconds-of-css/blob/master/snippets/offscreen.md):


```
.is-hidden {
      border: 0;
      clip: rect(0 0 0 0);
      height: 1px;
      margin: -1px;
      overflow: hidden;
      padding: 0;
      position: absolute;
      width: 1px;
    }
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2018-04-05 10:09:33

------------

Try this

```
<input class="txt" type="password" [(ngModel)]="input_pw" [hidden]="isHidden">
```



------------
    
    
## Answer \#7

 Vote: 0

Created at 2018-04-05 10:13:04

------------

Try this:

```
<button (click)="click()">Click me</button>

<input class="txt" type="password" [(ngModel)]="input_pw" [ngClass]="{'hidden': isHidden}" />
```




```
isHidden: boolean = false;
click(){
    this.isHidden = !this.isHidden;
}
```



------------
    
    
## Answer \#8

 Vote: 0

Created at 2018-04-05 10:44:38

------------

There is two way for hide a element 


1. Use the "hidden" html attribute But in angular you can bind it with one or more fields like this : <input class="txt" type="password" [(ngModel)]="input_pw" [hidden]="isHidden">



2.Better way of doing this is to use " *ngIf " directive like this :

```
<input class="txt" type="password" [(ngModel)]="input_pw" *ngIf="!isHidden">
```


Now why this is a better way because it doesn't just hide the element, it will removes it from the html code so this will help your page to render.


------------
    
    
## Answer \#9

 Vote: 0

Created at 2022-09-27 02:38:24

------------

[hidden]="true" was being overridden by a d-flex on my div element


------------
    
    
## Answer \#10

 Vote: 0

Created at 2022-11-03 16:51:20

------------

`[class.cdk-visually-hidden]=true` is working for angular 14


------------
    
    