
# Post \#43309712 [Link](https://stackoverflow.com/questions/43309712/)

## How to check if a value is not null and not empty string in JS

**Vote**: 118 (43/702) **Views**: 492343 (7/702) 

**Internal ID** \#0-0-131

Created at 2017-04-09 17:26:24

Tags: `javascript`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

Is there any check if a value is not null and not empty string in Javascript? I'm using the following one:

```
var data; //get its value from db 
if(data != null && data != '') {
   // do something
}
```


But I'm wondering if there is another better solution. Thanks.


----------
        
## Answer \#0

**Accepted** Vote: 221

Created at 2017-04-09 17:36:26

------------

If you truly want to confirm that a variable is not null and not an empty string specifically, you would write:

```
if(data !== null && data !== '') {
   // do something
}
```


Notice that I changed your code to check for type equality (`!==`|`===`).

If, however you just want to make sure, that a code will run only for "reasonable" values, then you can, as others have stated already, write:

```
if (data) {
  // do something
}
```


Since, in javascript, both null values, and empty strings, equals to false (i.e. `null == false`).

The difference between those 2 parts of code is that, for the first one, every value that is not specifically null or an empty string, will enter the `if`. But, on the second one, every true-ish value will enter the `if`: `false`, `0`, `null`, `undefined` and empty strings, would not.


------------
    
    
## Answer \#1

 Vote: 26

Created at 2019-07-23 11:33:38

------------

Instead of using

```
if(data !== null && data !== ''  && data!==undefined) {

// do something
}
```


You can use below simple code

```
if(Boolean(value)){ 
// do something 
}
```


- - 


------------
    
    
## Answer \#2

 Vote: 22

Created at 2017-04-09 17:39:04

------------

Both `null` and an empty string are falsy values in JS. Therefore, 

```
if (data) { ... }
```


is completely sufficient.

A note on the side though: I would avoid having a variable in my code that could manifest in different types. If the data will eventually be a string, then I would initially define my variable with an empty string, so you can do this:

```
if (data !== '') { ... }
```


without the null (or any weird stuff like `data = "0"`) getting in the way.


------------
    
    
## Answer \#3

 Vote: 10

Created at 2020-03-12 10:29:07

------------

```
if (data?.trim().length > 0) {
   //use data
}
```


the `?.` [optional chaining operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining) will short-circuit and return `undefined` if data is `nullish` (`null` or `undefined`) which will evaluate to false in the `if` expression.


------------
    
    
## Answer \#4

 Vote: 7

Created at 2019-08-22 14:53:46

------------

I often test for truthy value and also for empty spaces in the string:

```
if(!(!data || data.trim().length === 0)) {
  // do something here
}
```


If you have a string consisting of one or more empty spaces it will evaluate to true.


------------
    
    
## Answer \#5

 Vote: 4

Created at 2021-05-08 13:00:02

------------

Simple solution to check if string is `undefined` or `null` or `""`:-
```
const value = null;
if(!value) {
  console.log('value is either null, undefined or empty string');
}
```



------------
    
    
## Answer \#6

 Vote: 2

Created at 2018-03-23 07:47:41

------------

Both null and empty could be validated as follows:

```
<script>
function getName(){
    var myname = document.getElementById("Name").value;
    if(myname != '' && myname != null){
        alert("My name is "+myname);
    }else{
        alert("Please Enter Your Name");
    }       
}
```





------------
    
    
## Answer \#7

 Vote: 1

Created at 2018-10-16 12:09:15

------------

----------

```
function myFun(){
var inputVal=document.getElementById("inputId").value;
if(inputVal){
document.getElementById("result").innerHTML="<span style='color:green'>The value is "+inputVal+'</span>';
}
else{
document.getElementById("result").innerHTML="<span style='color:red'>Something error happen! the input May be empty.</span>";
}
}
```
```
<input type="text" id="inputId">
<input type="button" onclick="myFun()" value="View Result">
<h1 id="result"></h1>
```




------------
    
    
## Answer \#8

 Vote: 1

Created at 2020-03-12 10:10:14

------------

I got so fed up with checking for null and empty strings specifically, that I now usually just write and call a small function to do it for me.

```
/**
 * Test if the given value equals null or the empty string.
 * 
 * @param {string} value
**/
const isEmpty = (value) => value === null || value === '';

// Test:
isEmpty('');        // true
isEmpty(null);      // true
isEmpty(1);         // false
isEmpty(0);         // false
isEmpty(undefined); // false
```



------------
    
    
## Answer \#9

 Vote: 1

Created at 2020-10-03 16:54:16

------------

When we code empty in essence could mean any one of the following given the circumstances;
- - - - - - - - - 
In real life situation as OP stated we may wish to test them all or at times we may only wish to test for limited set of conditions.
Generally `if(!a){return true;}` serves its purpose most of the time however it will not cover wider set of conditions.
Another hack that has made its round is `return (!value || value == undefined || value == "" || value.length == 0);`
But what if we need control on whole process?
There is no simple whiplash solution in native core JavaScript it has to be adopted. Considering we drop support for legacy IE11 (to be honest even windows has so should we) below solution born out of frustration works in all modern browsers;
```
function empty (a,b=[])
 {if(!Array.isArray(b)) return; 
 var conditions=[null,'0','0.0',false,undefined,''].filter(x => !b.includes(x));
 if(conditions.includes(a)|| (typeof a === 'string' && conditions.includes(a.toString().trim())))
 {return true;};
 return false;};`
```

Logic behind the solution is function has two parameters  and , a is value we need to check, b is a array with set conditions we need to exclude from predefined conditions as listed above. Default value of b is set to an empty array [].
First run of function is to check if b is an array or not, if not then early exit the function.
next step is to compute array difference from `[null,'0','0.0',false,undefined,'']` and from array b. if b is an empty array predefined conditions will stand else it will remove matching values.
conditions = [predefined set] - [to be excluded set]
 function does exactly that make use of it.
Now that we have conditions in array set all we need to do is check if value is in conditions array.
 function does exactly that no need to write nasty loops of your own let JS engine do the heavy lifting.

if we are to convert a into string for comparison then 0 and 0.0 would run fine however Null and Undefined would through error blocking whole script. We need edge case solution. Below simple  covers the edge case if first condition is not satisfied. Running another early check through include makes early exit if not met.
```
if(conditions.includes(a)||  (['string', 'number'].includes(typeof a) && conditions.includes(a.toString().trim())))
```

trim() function will cover for wider white spaces and tabs only value and will only come into play in edge case scenario.

```
function empty (a,b=[]){
if(!Array.isArray(b)) return;
conditions=[null,'0','0.0',false,undefined,''].filter(x => !b.includes(x));
if(conditions.includes(a)|| 
(['string', 'number'].includes(typeof a) && conditions.includes(a.toString().trim()))){
 return true;
} 
return false;
}

console.log('1 '+empty());
console.log('2 '+empty(''));
console.log('3 '+empty('      '));
console.log('4 '+empty(0));
console.log('5 '+empty('0'));
console.log('6 '+empty(0.0));
console.log('7 '+empty('0.0'));
console.log('8 '+empty(false));
console.log('9 '+empty(null));
console.log('10 '+empty(null,[null]));
console.log('11 dont check 0 as number '+empty(0,['0']));
console.log('12 dont check 0 as string '+empty('0',['0']));
console.log('13 as number for false as value'+empty(false,[false]));
```


Lets make it complex - what if our value to compare is array its self and can be as deeply nested it can be. what if we are to check if any value in array is empty, it can be an edge business case.
```
function empty (a,b=[]){
    if(!Array.isArray(b)) return;

    conditions=[null,'0','0.0',false,undefined,''].filter(x => !b.includes(x));
    if(Array.isArray(a) && a.length > 0){
    for (i = 0; i < a.length; i++) { if (empty(a[i],b))return true;} 
    } 
    
    if(conditions.includes(a)|| 
    (['string', 'number'].includes(typeof a) && conditions.includes(a.toString().trim()))){
     return true;
    } 
    return false;
    }

console.log('checking for all values '+empty([1,[0]]));
console.log('excluding for 0 from condition '+empty([1,[0]], ['0']));
```


it simple and wider use case function that I have adopted in my framework;
- - - - 


------------
    
    
## Answer \#10

 Vote: 0

Created at 2020-10-13 18:31:17

------------

```
function validateAttrs(arg1, arg2, arg3,arg4){
    var args = Object.values(arguments);
    return (args.filter(x=> x===null || !x)).length<=0
}
console.log(validateAttrs('1',2, 3, 4));
console.log(validateAttrs('1',2, 3, null));
console.log(validateAttrs('1',undefined, 3, 4));
console.log(validateAttrs('1',2, '', 4));
console.log(validateAttrs('1',2, 3, null));
```




------------
    
    