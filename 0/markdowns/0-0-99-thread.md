
# Post \#48996054 [Link](https://stackoverflow.com/questions/48996054/)

## Angular 5 Validators.pattern regex for only numbers

**Vote**: 20 (261/702) **Views**: 135192 (60/702) 

**Internal ID** \#0-0-99

Created at 2018-02-26 19:51:09

Tags: `regex` `angular` `angular5`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm having trouble getting the Angular 5 Validators.pattern to work. I've tried every regex that SHOULD normally work, but it's not working. I read that Validators.pattern requires a different format, but I can't seem to find it...

Any ideas what the Validators.pattern should be to

(1) allow only numbers, positive and negative

and 

(2) allow only numbers, positive and negative, optionally with up to 2 decimal places

Valid examples would be: 
1
1.2
1.22
-21
-21.48

Anything with any letter in it would be invalid.

Thanks for your help!


----------
        
## Answer \#0

**Accepted** Vote: 38

Created at 2018-02-26 21:21:41

------------

After trying so many regex expressions, none was working in Safari. So here's what I did...

First, I used the regex when creating the form control:

```
new FormControl({value: field.value}, [Validators.required, Validators.pattern(/^-?(0|[1-9]\d*)?$/)])
```


Then, in the input itself I added an (input) event:

```
<input matInput (input)="validateInput(field)" [placeholder]="field.label" type="number" [formControlName]="field.id" [id]="field.id">
```


And that function looks like this:

```
validateInput(field) {

    this.detailForm.patchValue({ [field.id]: this.detailForm.controls[field.id].value }); }

}
```


If you enter an invalid character (e.g. a-z) in Safari, it doesn't store it in the field control's value. It only stores valid values (numbers) there. So I'm just patching the value with the stored value -- if it's valid, with numbers, it's fine; if it's invalid, with other characters, then it will replace the input with an empty string.

Works perfectly on all browsers. Hope this helps someone :)


------------
    
    
## Answer \#1

 Vote: 12

Created at 2019-12-20 10:27:31

------------

Allow only integer numbers without symbols. 

```
Validators.pattern(/^[0-9]\d*$/)
```


Numbers start from one

```
Validators.min(1)
```



------------
    
    
## Answer \#2

 Vote: 6

Created at 2019-01-25 14:40:56

------------

Don't forget to use with quotes and double backslash in order to make your regex work like it's supposed `Validators.pattern('\\-?\\d*\\.?\\d{1,2}')` or you can make variable for that

```
const regexPattern = /\-?\d*\.?\d{1,2}/;
Validators.pattern(regexPattern);
```



------------
    
    
## Answer \#3

 Vote: 6

Created at 2019-05-02 07:12:04

------------

This one work for me in Aungular 7
```
const numericNumberReg= '^-?[0-9]\\d*(\\.\\d{1,2})?$';

this.$form = this.$builder.group({
      cost: ['', [Validators.required,Validators.pattern(numericNumberReg)]],
    });
```



------------
    
    
## Answer \#4

 Vote: 2

Created at 2019-08-25 10:47:39

------------

You can try with this regex '^-?[0-9]\d*(\.\d{1,2})?$'. And your form builder will be like below:
```
this.yourForm= this.formBuilder.group({
    amount: [this.amount, [Validators.required, Validators.pattern('^-?[0-9]\\d*(\\.\\d{1,2})?$')]]
});
```



------------
    
    