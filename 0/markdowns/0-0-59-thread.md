
# Post \#61221735 [Link](https://stackoverflow.com/questions/61221735/)

## International Telephone Input for Angular 9

**Vote**: 4 (532/702) **Views**: 9145 (428/702) 

**Internal ID** \#0-0-59

Created at 2020-04-15 05:10:05

Tags: `angular` `angular9`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am need to implement an input for intl mobile number in my web application, but I can hardly find any that supports Angular 9 version.
Do you have any recommendations?
Thanks


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2020-04-17 13:40:18

------------

I found a better npm package(ngx-intl-tel-input) that fullfil your requirement. Please have look on it.

Here is the link to the package 
[https://www.npmjs.com/package/ngx-intl-tel-input](https://www.npmjs.com/package/ngx-intl-tel-input)

stackblitz demo link:
[https://stackblitz.com/edit/ngx-intl-tel-input-demo](https://stackblitz.com/edit/ngx-intl-tel-input-demo)


------------
    
    
## Answer \#1

 Vote: 5

Created at 2021-08-19 00:39:07

------------

If you are using Angular material,you can use 
url : [https://www.npmjs.com/package/ngx-mat-intl-tel-input](https://www.npmjs.com/package/ngx-mat-intl-tel-input)
:
Angular Material,
ReactiveFormsModule,
FormsModule,
Validation with libphonenumber-js


------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-04-15 05:31:02

------------

There is a good [validator](https://www.npmjs.com/package/validator) on npm. 

Use the `isMobilePhone(str [, locale [, options]])` method to validate all international phone numbers. 


------------
    
    
## Answer \#3

 Vote: 0

Created at 2023-02-20 14:18:32

------------

As of 2023 february, the checked answer recommand a package that seem's to be discontinued. No update for 10 months, no update for Angular13+.
As a replacement i recommand this package wich is maintained and ok for Angular 14.x:
[https://www.npmjs.com/package/ngx-mat-intl-tel-input](https://www.npmjs.com/package/ngx-mat-intl-tel-input)


------------
    
    