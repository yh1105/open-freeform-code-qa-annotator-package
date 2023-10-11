
# Post \#46786986 [Link](https://stackoverflow.com/questions/46786986/)

## How and where to use ::ng-deep?

**Vote**: 208 (19/702) **Views**: 380474 (11/702) 

**Internal ID** \#0-2-158

Created at 2017-10-17 09:34:24

Tags: `css` `angular` `angular-template`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

How and where can one use `::ng-deep` in Angular 4?
Actually I want to overwrite some of the CSS properties of the child components from the parent components. Moreover is it supported on IE11?


----------
        
## Answer \#0

**Accepted** Vote: 181

Created at 2017-10-17 09:42:28

------------

Usually  `/deep/ “shadow-piercing”` combinator  can be used to force a style down to `child components`. This selector had an alias >>> and now has another one called ::ng-deep.
since `/deep/ combinator` has been deprecated,  it is recommended to use `::ng-deep`

```
<div class="overview tab-pane" id="overview" role="tabpanel" [innerHTML]="project?.getContent( 'DETAILS')"></div>
```

and `css`
```
.overview {
    ::ng-deep {
        p {
            &:last-child {
                margin-bottom: 0;
            }
        }
    }
}
```

it will be applied to child components


------------
    
    
## Answer \#1

 Vote: 131

Created at 2019-03-26 11:56:28

------------

I would emphasize the importance of limiting the `::ng-deep` to only children of a component by requiring the parent to be an encapsulated css class.
For this to work it's important to use the `::ng-deep` after the parent, not before otherwise it would apply to all the classes with the same name the moment the component is loaded.
Using the `:host` keyword before `::ng-deep` will handle this automatically:
```
:host ::ng-deep .mat-checkbox-layout
```


---


Alternatively you can achieve the same behavior by adding a component scoped CSS class before the `::ng-deep` keyword:
```
.my-component ::ng-deep .mat-checkbox-layout {
    background-color: aqua;
}
```

Component template:
```
<h1 class="my-component">
    <mat-checkbox ....></mat-checkbox>
</h1>
```


---


Resulting (Angular generated) css will then include the uniquely generated name and apply only to its own component instance:
```
.my-component[_ngcontent-c1] .mat-checkbox-layout {
    background-color: aqua;
}
```



------------
    
    
## Answer \#2

 Vote: 129

Created at 2017-10-17 09:43:19

------------


## USAGE


`::ng-deep`, `>>>` and `/deep/` disable view encapsulation for specific CSS rules, in other words, it gives you access to DOM elements, which are not in your component's HTML. For example, if you're using Angular Material (or any other third-party library like this), some generated elements are outside of your component's area (such as [dialog](https://material.angular.io/components/dialog/overview)) and you can't access those elements directly or using a regular CSS way. If you want to change the styles of those elements, you can use one of those three things, for example:
```
::ng-deep .mat-dialog {
  /* styles here */
}
```

For now Angular team recommends making  manipulations only with  view encapsulation.

## DEPRECATION


 manipulations are actually [deprecated](https://angular.io/guide/component-styles#deprecated-deep--and-ng-deep) too,  it's still working for now, because Angular does pre-processing support (don't rush to refuse `::ng-deep` today, take a look at [deprecation practices](https://angular.io/guide/releases#deprecation-practices) first).
Anyway, before following this way, I recommend you to take a look at  approach (which is not ideal too, it allows your styles to leak into other components), but in some cases, it's a better way. If you decided to disable view encapsulation, it's strongly recommended to use specific classes to avoid CSS rules intersection, and finally, avoid a mess in your stylesheets. It's really easy to disable right in the component's `.ts` file:
```
@Component({
  selector: '',
  template: '',
  styles: [''],
  encapsulation: ViewEncapsulation.None  // Use to disable CSS Encapsulation for this component
})
```

You can find more info about the view encapsulation in [this](https://coryrylan.com/blog/css-encapsulation-with-angular-components) article.


------------
    
    
## Answer \#3

 Vote: 39

Created at 2018-06-14 05:08:04

------------

Make sure not to miss the explanation of `:host-context` which is directly above `::ng-deep` in the angular guide : [https://angular.io/guide/component-styles](https://angular.io/guide/component-styles). I missed it up until now and wish I'd seen it sooner.
`::ng-deep` is often necessary when you didn't write the component and don't have access to its source, but `:host-context` can be a very useful option when you do.
For example I have a black `<h1>` header inside a component I designed, and I want the ability to change it to white when it's displayed on a dark themed background.
If I didn't have access to the source I may have to do this in the css for the parent:
```
.theme-dark widget-box ::ng-deep h1 { color: white; }
```

But instead with `:host-context` you can do this  the component.
```
h1 
 {
     color: black;       // default color

     :host-context(.theme-dark) &
     {
         color: white;   // color for dark-theme
     }

     // OR set an attribute 'outside' with [attr.theme]="'dark'"

     :host-context([theme='dark']) &
     {
         color: white;   // color for dark-theme
     }
 }
```

This will look anywhere in the component chain for `.theme-dark` and apply the css to the h1 if found. This is a good alternative to relying too much on `::ng-deep` which while often necessary is somewhat of an anti-pattern.
In this case the `&` is replaced by the `h1` (that's how sass/scss works) so you can define your 'normal' and themed/alternative css right next to each other which is very handy.
Be careful to get the correct number of `:`. For `::ng-deep` there are two and for `:host-context` only one.


------------
    
    
## Answer \#4

 Vote: 2

Created at 2018-09-21 18:18:13

------------

Just an update: 

You should use `::ng-deep` instead of `/deep/` which seems to be deprecated.

Per documentation:

> The shadow-piercing descendant combinator is deprecated and support is
  being removed from major browsers and tools. As such we plan to drop
  support in Angular (for all 3 of /deep/, >>> and ::ng-deep). Until
  then ::ng-deep should be preferred for a broader compatibility with
  the tools.

You can find it [here](https://angular.io/guide/component-styles)


------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-09-09 18:22:18

------------

I looked through all those answers and found nobody mentioned a child component can pass a style CSS in from its parent.
In component ts file, you can use this:
```
@Input() styles: any = {};
```

In component html file, you use this:
```
[ngStyle]="styles"
```

in parent, you use this :
```
<yourComponent [styles]="{backgroundColor: 'blue', 'font-size': '16px'}">
```

Please see more details here: [Best way to pass styling to a component](https://stackoverflow.com/questions/35734073/best-way-to-pass-styling-to-a-component)
In this way, we didn't break encapsulation, which is one of those most important  Object orientation principles


------------
    
    
## Answer \#6

 Vote: -5

Created at 2019-10-24 17:14:59

------------

Use ::ng-deep with caution.  I used it throughout my app to set the material design toolbar color to different colors throughout my app only to find that when the app was in testing the toolbar colors step on each other.  Come to find out it is because these styles becomes global, see [this article](https://angular.io/guide/component-styles#deprecated-deep--and-ng-deep)  Here is a working code solution that doesn't bleed into other components.
```
<mat-toolbar #subbar>
...
</mat-toolbar>

export class BypartSubBarComponent implements AfterViewInit {
  @ViewChild('subbar', { static: false }) subbar: MatToolbar;
  constructor(
    private renderer: Renderer2) { }
  ngAfterViewInit() {
    this.renderer.setStyle(
      this.subbar._elementRef.nativeElement, 'backgroundColor', 'red');
  }

}
```



------------
    
    