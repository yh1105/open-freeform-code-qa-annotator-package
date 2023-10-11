
# Post \#62911060 [Link](https://stackoverflow.com/questions/62911060/)

## Finding min and max in array Javascript

**Vote**: 3 (575/702) **Views**: 12959 (383/702) 

**Internal ID** \#0-0-94

Created at 2020-07-15 08:50:27

Tags: `javascript` `arrays`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

So I try to find the minimum and maximum if an array and want to call the function. I made this code, but it doesn´t work. I just don´t get why.
```
function getMinMax(arr){
  let maximum = Math.max(arr);
  let minimum = Math.min(arr);
 let result =  ([maximum, minimum]); 
  return result;
};

getMinMax([10,3,8,1,33])
```



----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2020-07-15 09:24:20

------------

You can find the maximum and minimum in 2 ways.
First way You go on with maximum and minimum form Math function in javascript, if you need to be used when you have to set numbers from max and min, like this :
```
let maximum = Math.max(10, 3, 8, 1, 33);
let minimum = Math.min(10, 3, 8, 1, 33);
```

Now you have an array so you have to convert to As above, for example :
let array = [1,2,3,4,5]
when you use ... before array you convert array to this :
1,2,3,4,5
for example :
```
function getMinMaxWithMath(arr){
  // Math.max(10,3,8,1,33)
  let maximum = Math.max(...arr);
  // Math.min(10,3,8,1,33)
  let minimum = Math.min(...arr);
 let result =  ([maximum, minimum]); 
  return result;
};

console.log('getMinMaxWithMath ', getMinMaxWithMath([10,3,8,1,33]));
```


The second way you can use for loop for example :
```
function getMinMaxForLoop(arr){
  let maximum = arr[0];
  let minimum = arr[0];
  for (let i = 0 ; i < arr.length; i++) {
    if (maximum < arr[i]) {
      maximum = arr[i];
    } else {
      minimum = arr[i];
    }
    
  }
 let result =  ([maximum, minimum]); 
  return result;
};
console.log('getMinMaxForLoop : ',getMinMaxForLoop([10,3,8,1,33]))
```


You can Look output from this [link](https://stackblitz.com/edit/js-uhvgak);


------------
    
    
## Answer \#1

 Vote: 3

Created at 2020-07-15 08:53:07

------------

You try to pass array, but functions (min, max) accept only number arguments.
You need to unpack array to array of arguments with the [spread operator (...)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax):
```
const arr = [10, 3, 8, 1, 33];
const min = Math.min(...arr);
const max = Math.max(...arr);
```

So, your code should be like this:
```
function getMinMax(arr){
  let maximum = Math.max(...arr);
  let minimum = Math.min(...arr);
 let result =  ([maximum, minimum]); 
  return result;
};

getMinMax([10,3,8,1,33])
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-07-15 12:13:15

------------

If you want to use this method, you need to structure the array
```
function getMinMax(arr) {
    let maximum = Math.max(...arr);
    let minimum = Math.min(...arr);
    let result = [maximum, minimum];
    return result;
};

getMinMax([10, 3, 8, 1, 33]);
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2020-07-15 09:28:45

------------

```
const getMinMax = (arr) => {
  const sortedArr = arr.sort((a,b) => a - b);
  const min = sortedArr[0];
  const max = sortedArr[sortedArr.length - 1];
  return [min, max];
};

console.log(getMinMax([10,3,8,1,33]))
```


You also can use `Array.sort()` to achieve your desired result.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2020-07-15 09:32:37

------------

You can also try this simple and understandable math

## Example


```
function max(arr) {
  let i;
  let max = arr[0]
  let min = arr[0]
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] > max) {
      max = arr[i]
    } else if (arr[i] < min) {
      min = arr[i]
    }
  }
  let res = (["min: " + min, "max: " + max])
  return res
}

console.log(max([10, 3, 8, 1, 33]))
```




------------
    
    
## Answer \#5

 Vote: 0

Created at 2020-07-15 11:19:52

------------

Or, as a one-liner (nothing new, only more compact):
```
const getMinMax=arr=>[Math.min(...arr),Math.max(...arr)];

console.log(getMinMax([10,3,8,1,33]))
```




------------
    
    