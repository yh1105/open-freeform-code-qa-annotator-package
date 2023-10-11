
# Post \#56134476 [Link](https://stackoverflow.com/questions/56134476/)

## How to make select dropdown readonly or disabled conditionally in angular?

**Vote**: 10 (387/702) **Views**: 37561 (199/702) 

**Internal ID** \#0-0-80

Created at 2019-05-14 16:02:42

Tags: `angular` `typescript` `angular7`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am using Angular7 and I want to make select element readonly or disabled conditionnaly.



```
editable: boolean = true;
```


And the template:

```
<select  #listOfOptions="ngModel" [(ngModel)]="myList" [readonly]="!editable" class="form-control">
    </select>
```


This causes:

> Error: Template parse errors:
      Can't bind to 'readOnly' since it isn't a known property of 'select'.

There is an [issue](https://github.com/ng-select/ng-select/issues/786) about this, no solutions yet!



```
<select [attr.disabled]="!editable">
...
</select>
```


It is always disabled, no matter how I set the editable variable.

Thank you for help.


----------
        
## Answer \#0

**Accepted** Vote: 13

Created at 2019-05-14 16:05:28

------------

You should use `disabled` instead of `attr.disabled`

```
<select [disabled]="!editable">
...
</select>
```



------------
    
    
## Answer \#1

 Vote: 13

Created at 2020-09-21 02:53:01

------------

I faced same issue with owner post.
I tried accepted answer but it not worked in my side.
I use `[attr.disabled]="!editable ? '' : null"` following this link it worked.
[Angular 4 select disabled is not working](https://stackoverflow.com/questions/49087887/angular-4-select-disabled-is-not-working/49088204)
I shared for whom concern.
```
<select [attr.disabled]="!editable ? '' : null">
  <option></option>
</select>
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2019-05-14 16:18:38

------------

This may not be entirely of use to you, as I can see you're using Template Driven Forms, however I'll suggest this as an alternative route you may want to look into!

If you were to switch over to using Reactive Forms for this, you can set the disabled property when you initialise the form. There's a bit more boiler involved, which I think puts some people off the Reactive Forms, but I find I quite prefer them personally.

Set up your HTML like this:

```
<form [formGroup]="selectForm">
  <select formControlName="mySelect">
    <!-- Put your options in here with an *ngFor over an array in your component -->
  </select>
</form>
```


And then set up your component like this:

```
import { Component, OnInit } from '@angular/core';
import { FormBuilder, FormGroup } from '@angular/forms';
// Note: You will need to import the ReactiveFormsModule in your nearest local ngModule

@Component({...})
export class MyComponent implements OnInit {

  public selectForm: FormGroup;
  public editable: boolean;
  public options: any[];

  constructor(private formBuilder: FormBuilder) {}

  ngOnInit(): void {
    this.selectForm = this.formBuilder.group({
      mySelect: [
        { value: '', disabled: !editable }
      ]
    });
  }

}
```


So the form control will now have the 'disabled' property bound directly to your local variable 'editable', and this should work as you desire.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-06-13 20:32:17

------------

In any non working case, here's what I do.  I encapsulate the mat-form-field in a div for which I set the pointer-events to none and change the opacity to .5 to simulate a readonly or disabled.
```
<div
  [style.pointer-events] = "DisableDocumentType(documentIndex) ? 'none' : 'auto'"
  [style.opacity] = "DisableDocumentType(documentIndex) ? .5 : 1"
>
  <mat-form-field appearance="outline" style="width:10vw; align-content: end;">
    <mat-label> {{'app.documentType' | translate}}*</mat-label>
    <mat-select
      formControlName="documentType"
      (selectionChange)="changeDocumentType($event.value, documentIndex)"
    >
      <mat-option [value]="documentType.code" *ngFor="let documentType of documentTypes">{{documentType.name | translate}}
      </mat-option>
    </mat-select>
  </mat-form-field>
</div>
```



------------
    
    