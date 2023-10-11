
# Post \#65641648 [Link](https://stackoverflow.com/questions/65641648/)

## How to get data from nested objects in the form of array

**Vote**: 5 (499/702) **Views**: 3966 (545/702) 

**Internal ID** \#0-0-0

Created at 2021-01-09 10:37:02

Tags: `javascript` `arrays` `object`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

As you can see, the keys are now the name of the items and the corresponding value is another object consisting of two keys - quantity and price.
```
var itemsToBuy = {
    milk: {
        quantity : 5,
        price: 20
    },
    bread: {
        quantity : 2,
        price: 15
    },
    potato: {
        quantity : 3,
        price: 10
    }
}
```

I tried but I am getting only undefined output.
Want to get data such like :
```
['milk', 'bread', 'potato']

[20, 15, 10]
```



----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2021-01-09 10:39:43

------------


## Solution


- With `Object.keys()` you can get the keys of your Object as an array.  `['milk', 'bread', 'potato']`- With `Object.entries()` in combination with `map()` you can get the values of the price property as an array.  `[20, 15, 10]`

```
var itemsToBuy = {
        milk: {
            quantity : 5,
            price: 20
        },
        bread: {
            quantity : 2,
            price: 15
        },
        potato: {
            quantity : 3,
            price: 10
        }
    }
    const keys = Object.keys(itemsToBuy);
    const val = Object.entries(itemsToBuy).map((element)=> element[1].price)

    console.log(keys);
    console.log(val);
```




------------
    
    
## Answer \#1

 Vote: 4

Created at 2021-01-09 10:42:25

------------

```
const object1  = {
  milk: {
      quantity : 5,
      price: 20
  },
  bread: {
      quantity : 2,
      price: 15
  },
  potato: {
      quantity : 3,
      price: 10
  }
}
console.log(Object.entries(object1).map(([key,value])=> value.price))
```




------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-01-09 10:43:07

------------

for names:
```
Object.keys(itemsToBuy) //["milk", "bread", "potato"]
```

and for prices:
```
Object.entries(itemsToBuy).map((el)=> el[1].price)//[20, 15, 10]
```



------------
    
    
## Answer \#3

 Vote: 2

Created at 2021-01-09 10:45:44

------------

To get object keys you can use: Object.keys()
```
let keys = Object.keys(itemsToBuy) // ["milk", "bread", "potato"]
```

And for the nested values you can get 1st all values
```
let values = Object.values(itemsToBuy) // [{quantity : 5, price: 20}, {quantity : 2, price: 15}, {quantity : 3, price: 10}]
```

And then map to return the new array with elements you want to extract ex.price:
```
let price = values.map(value => value.price) // [20, 15, 10]
```



------------
    
    