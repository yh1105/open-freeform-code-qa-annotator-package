
# Post \#57888846 [Link](https://stackoverflow.com/questions/57888846/)

## Set horizontal scroll in angular material tabs

**Vote**: 17 (290/702) **Views**: 22164 (282/702) 

**Internal ID** \#0-0-97

Created at 2019-09-11 12:10:52

Tags: `angular` `angular-material`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

If we see the following tabs in the mobile's browser, there are two buttons [] (the first one is on the right side and the other is on the left side)

```
<mat-tab-group mat-stretch-tabs class="example-stretched-tabs mat-elevation-z4">
    <mat-tab label="Item-1"> Content 1 </mat-tab>
    <mat-tab label="Item-2"> Content 2 </mat-tab>
    <mat-tab label="Item-3"> Content 3 </mat-tab>
    <mat-tab label="Item-4"> Content 4 </mat-tab>
    <mat-tab label="Item-5"> Content 5 </mat-tab>
    <mat-tab label="Item-6"> Content 6 </mat-tab>
    <mat-tab label="Item-7"> Content 7 </mat-tab>
    <mat-tab label="Item-8"> Content 8 </mat-tab>
    <mat-tab label="Item-9"> Content 9 </mat-tab>
    <mat-tab label="Item-10"> Content 10 </mat-tab>
</mat-tab-group>
```


[](https://i.stack.imgur.com/TwYZ5.png)

I'm going to remove the two buttons and use scroll instead.



 Remove the two buttons with the following code :

```
ngAfterViewInit(): void {
  document.getElementsByClassName('mat-tab-header-pagination-before')[0].remove();
  document.getElementsByClassName('mat-tab-header-pagination-after')[0].remove();
}
```


 put the following style in style.css:

```
.mat-tab-label-container {
    display: flex;
    flex-grow: 1;
    overflow: hidden;
    z-index: 1;
    overflow-x: scroll !important;
}
```




The problem of the above method is when we select item-10 we can not scroll left

[StackBlitz Here.](https://stackblitz.com/edit/angular-6fgzsv)


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2020-05-27 08:12:14

------------

I had solved these problems with scrolling by making my own auto-scroll effect on already scrollable mat-tabs - when you click on the partially visible tab - it will be autoscrolled to nearly center position, also left button will be scrolled to right, right partially visible button to left. 

Please check this comment for more details: 
[https://stackoverflow.com/a/62031767/9026103](https://stackoverflow.com/a/62031767/9026103)


------------
    
    
## Answer \#1

 Vote: 14

Created at 2020-01-03 15:17:51

------------

Instead of applying overflow style to .mat-tab-label-container, we can apply it to .mat-tab-header class.

```
::ng-deep .mat-tab-header {
  overflow-x: scroll !important;
}

::ng-deep .mat-tab-label-container { 
  overflow: visible !important;
}
```



Also, If you want to remove the scrollbar which comes below the element where overflow-x: scroll is used, we can do the following: 

```
::ng-deep .mat-tab-header::-webkit-scrollbar {
  display: none;
}
```



: The problem of the item-10 that cannot be scrolled left is solved by the above styles. I have forked @AbolfazlR repository and made the above changes. Below is the working example

[StackBlitz](https://stackblitz.com/edit/angular-5ww4ot)


------------
    
    
## Answer \#2

 Vote: 6

Created at 2019-09-11 14:25:10

------------

It seems it is a [known issue](https://github.com/angular/components/issues/2236), and is still in open status. 

A possible solution is using this scss rule(copied from the discussion of the issue) instead of the workaround you mentioned:

```
::ng-deep .mat-tab-header-pagination {
    display: none !important;
}
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2020-02-08 14:01:14

------------

The reason for not being able to scroll when the last tab is active is because Angular mat-tab control is using a transform to move the last tab into view and forcing it to the right side of container.  

[](https://i.stack.imgur.com/LMleB.png)

A possible solution is to squash the transform by adding this CSS class:

```
::ng-deep {

   // SOLUITON:
  .mat-tab-list {
    transform: inherit !important;
  }


  // FORCES SCROLLABILIY:
  .mat-tab-label-container {
    overflow-x: scroll !important;
  }

  // HIDES MAT-TAB's NATIVE PAGINATION:
  .mat-tab-header-pagination {
    display: none !important;
  }

}
```


  the problem with this solution is it kills the transform effect and could be a poor user experience in so much as the user has to scroll for themselves.  Arguably, it's better then not being able to scroll left at all.


------------
    
    