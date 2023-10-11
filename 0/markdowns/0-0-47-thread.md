
# Post \#53964694 [Link](https://stackoverflow.com/questions/53964694/)

## how do you declare an array of objects inside typescript?

**Vote**: 25 (228/702) **Views**: 69129 (126/702) 

**Internal ID** \#0-0-47

Created at 2018-12-28 21:57:13

Tags: `javascript` `reactjs` `typescript`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I would like to specify a function that takes an array of objects as a parameter, but I don't have a particular type defined for the object (a sort of "anonymous type")
```
bagTotal = (products) => {
 // function does stuff
}
```

I understand I can do this:
```
bagTotal = (products: any[]) => {
 // function does stuff
}
```

but this is a bit more relaxed then what I want: to be strict with typescript.
`products` is an array of the same-looking objects; all objects have a name, a price and a description.
how can I declare that?
I want to do something like
```
bagTotal = (products: [{name: string, price: number, description: string}]) => {
 // function does stuff
}
```

but that's not right. How can I declare this?


----------
        
## Answer \#0

**Accepted** Vote: 63

Created at 2018-12-28 22:01:27

------------

You're almost there, the placement of the brackets is just wrong:

```
{name: string, price: number, description: string}[]
```


The way you had it isn't entirely wrong, but it means something else: it means an array with exactly one item of this type. 


---



I'd also recommend extracting it to an interface, it'd make the type reusable and the thing easier to read:

```
interface Product {
    name: string;
    price: number;
    description: string;
}

const products: Product[];
```



------------
    
    
## Answer \#1

 Vote: 6

Created at 2021-02-23 11:38:12

------------

The [generic array type](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#arrays) `Array<elemType>` provides a straightforward way to declare an array of objects:
```
bagTotal = (products: Array<{name: string, price: number}>) => {
   ...
}
```



------------
    
    
## Answer \#2

 Vote: 5

Created at 2018-12-28 22:04:39

------------

I think you must declare the class "Product" so you can declare a Product array like this:

```
products: Product[];
```


and pass it as a parameter like this:

```
bagTotal = (products: Product[]) => {
 // function does stuff
}
```


To have the class you can do a new .ts file with this code:

```
export class Product {
  name: String;
  price: Number;
  description: String;
}
```


I wish that helped!

Thank you.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2018-12-28 22:05:28

------------

If you are declaring an array of a specific object and want to specify type for variables in the objects, I would create a class for the object like this:

```
class Item(){
    name: string;
    description: string;
    etc: any

  constructor() {
    this.name;
    this.description;
    this.etc;
}}
```


Then you can specify the array as an array of item objects:

```
itemArray: Array<Item>;
```



------------
    
    