
# Post \#71450038 [Link](https://stackoverflow.com/questions/71450038/)

## How to patch the value of multiselect dropdown (primeng component) in a reactive form?

**Vote**: 1 (672/702) **Views**: 1414 (629/702) 

**Internal ID** \#0-0-93

Created at 2022-03-12 13:19:16

Tags: `javascript` `angular` `angular-reactive-forms` `primeng` `primeng-dropdowns`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am using primeng components in the template.
I have the option to edit the form, which displays the existing field values. I am not able to patch the value of multiselect dropdown.
```
excludeDays = [      
    {label: 'Mon', value: 1},
    {label: 'Tue', value: 2},
    {label: 'Wed', value: 3},
    {label: 'Thu', value: 4},
    {label: 'Fri', value: 5},
    {label: 'Sat', value: 6},
    {label: 'Sun', value: 7},
  ]
```

I have my backend data coming in as a string and then I am converting it into array of number like
```
[1,2] or[2,3,4]
```

Now after I press the edit button I want the values of the dropdown to be selected as above excludeDays values are there.
```
<div class="col-12" style="width: auto;" >
      <p-multiSelect [options]="excludeDays" optionLabel="label" defaultLabel="Select day" formControlName="excludeDays"></p-multiSelect> 
</div>
```

I am using
```
this.form.patchValue({
... : ....
excludeDays: converted array of (excludeDays) to number array
})
```

This is the console output of the excludeDays array after converting it into number array
[](https://i.stack.imgur.com/HKdjr.png)


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2022-03-12 13:54:30

------------

After getting data from the backend , create an array of objects using the original array, then set those values to the patchValue method.
```
const excludeDays = [1,2].map(id=>this.excludeDays.find(day=>day.value === id));

this.form.patchValue({
excludeDays: excludeDays
})
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-03-12 14:31:42

------------

Prime NG multiselect () expects the same data type for  property as that of  property.
That means, if your  are an array of  (string[]) then you can have ngModel to be an  as well. But if your options are an array of , then options will have to be array of  (object must be of same type).
So for example, you have this array to bind to p-multiselect
```
excludeDays = [      
{label: 'Mon', value: 1},
{label: 'Tue', value: 2},
{label: 'Wed', value: 3},
{label: 'Thu', value: 4},
{label: 'Fri', value: 5},
{label: 'Sat', value: 6},
{label: 'Sun', value: 7}
]
```

and want to pre-select three values out of it, then you'll have to provide an array of same type for example
```
selectedDays = [
{label: 'Mon', value: 1},
{label: 'Tue', value: 2}
];
```

and your template would look like this
```
<p-multiSelect  [options]="excludeDays" [(ngModel)]="selectedDays"  defaultLabel="Select a day" optionLabel="label"></p-multiSelect>
```

Having explained that, you'll need to have the exact same array of objects in the  method instead of having array of numbers.
```
this.form.patchValue({
... : ....
excludeDays: excludeDays //instead of array of numbers you should have same array of objects.
})
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-03-12 14:42:55

------------

```
let datafromBackend = [1, 2];

let UpdateSelection = [];
this.excludeDays.forEach((item) => {
  if (datafromBackend.findIndex((x) => x === item.value) > -1) {
    UpdateSelection.push(item);
  }
});

this.profileForm.get('excludeDays').setValue(UpdateSelection);
```



------------
    
    