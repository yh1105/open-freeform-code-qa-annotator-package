
# Post \#68559392 [Link](https://stackoverflow.com/questions/68559392/)

## Find object from an array of nested objects by key in JavaScript

**Vote**: 3 (575/702) **Views**: 5524 (510/702) 

**Internal ID** \#0-0-8

Created at 2021-07-28 11:16:27

Tags: `javascript` `object` `recursion`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have a JSON nested object tree like this.
How can I get the specified object by passing an ID value to a function. (ID is a key in all nested objects)
I tried with recursion & JSON.stringify().findObject() but somehow they are not working.
```
{
    "id": "A",
    "name": "Item A",
    "child": [
        {
            "id": "B",
            "name": "Item B",
            "child": [
                {
                    "id": "C",
                    "name": "Item C"
                    "child": []
                }
            ]
        },
        {
            "id": "D",
            "name": "Item D",
            "child": []
        }
    ]
}


//Calling like this
var result = findObject("C");
```

Then `console.log(result);` Should be
```
{
  "id": "C",
  "name": "Item C"
  "child": []
}
```



----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2021-07-28 11:32:21

------------

You can wrap your parent `obj` in an array using `findObject`, which then invokes an auxiliary function `findObjectAux` that is responsible for performing the iteration over your array to find your match (note that for the parent object, you'll only have one item in your array, but subsequent calls could contain more). For each object in the array, you can check if its id matches the one you passed through into your function. If it does, you can return it to the calling function, if it doesn't match, then you can recurse down its `child` array. If looking through the objects child array happens to return a value, then you've found a match that you can return, otherwise, your `for` loop can continue looking at any remaining objects in your array:
```
const obj = { "id": "A", "name": "Item A", "child": [{ "id": "B", "name": "Item B", "child": [{ "id": "C", "name": "Item C", "child": [] }] }, { "id": "D", "name": "Item D", "child": [] }] };

const findObject = (obj, id) => findObjectAux([obj], id);
const findObjectAux = (arr, id) => {
  for (const obj of arr) {
    if (obj.id === id) return obj;
    const nestedObj = findObjectAux(obj.child, id);
    if (nestedObj) return nestedObj;
  }
}

const result = findObject(obj, "C");
console.log(result);
```


Or, you can go for an approach that looks at the objects themselves, which calls `.reduce()` on the child objects and then executes `findObject()` on the child objects to recursively check the if the child object / its children match the id:
```
const obj = { "id": "A", "name": "Item A", "child": [{ "id": "B", "name": "Item B", "child": [{ "id": "C", "name": "Item C", "child": [] }] }, { "id": "D", "name": "Item D", "child": [] }] };

const findObject = (obj, id) => {
  return obj.id === id 
    ? obj 
    : obj.child.reduce((acc, obj) => acc ?? findObject(obj, id), undefined);
}

const result = findObject(obj, "X");
console.log(result);
```


The above can be improved though, as `.reduce()` will keep iterating through your elements returning the same accumulator once `findObject()` returns a non-nullish value. What we need is a method like [.find()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find) that can stop its iteration early once a value is found. The issue with `.find()` is that it can only return those elements from the array your iterating, and not the child elements. To help optimize this, you can create a helper function (I've called it `flatFind()`), which behaves like `.find()`, but allows you to return a truthy value which will then be the resulting value returned by the `flatFind()` function:
```
const obj = { "id": "A", "name": "Item A", "child": [{ "id": "B", "name": "Item B", "child": [{ "id": "C", "name": "Item C", "child": [] }] }, { "id": "D", "name": "Item D", "child": [] }, { "id": "E", "name": "Item D", "child": [] }] };

const findObject = id => obj => obj.id === id 
  ? obj 
  : flatFind(obj.child, findObject(id));

const result = findObject("D")(obj);
console.log(result);
```
```
<script>
// Helper function:
const flatFind = (arr, fn) => {
  for(let i = 0; i < arr.length; i++) {
    const retVal = fn(arr[i], i, arr);
    if(retVal) return retVal; // early termination
  }
}
</script>
```




------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-07-30 13:01:15

------------

The answer from Nick Parsons is great, both the well-written code and the excellent explanation.
But I think there is a possibility to write this both more generically and more simply.
While the requirement here is to search by `id`, we can easily imagine all sorts of other ways we want to match.  Rather than trying to anticipate them all, we can write a generic version that takes an arbitrary predicate, and then configure it with a simple function.
Here is one approach:
```
const deepFind = (pred) => ([x, ...xs] = []) => 
  x && (pred (x) ? x : deepFind (pred) (x.child) || deepFind (pred) (xs))

const findById = (id) => (obj) =>  
  deepFind ((o) => o.id == id) ([obj])

const input = {id: "A", name: "Item A", child: [{id: "B", name: "Item B", child: [{id: "C", name: "Item C", child: []}]}, {id: "D", name: "Item D", child: []}]};

console .log ('C:', findById ('C') (input)) //~> {id: "C", name: "Item C", child: []}
console .log ('X:', findById ('X') (input)) //~> undefined
```


`deepFind` accepts a predicate function and returns a function that accepts an array and returns the first match when searching depth-first (on a `node -> node.child` tree) for a match to the predicate.
We wrap this with `findById`, which takes a target id, and returns a function which takes an input object, wraps that object in an array, and calls `deepFind` with a predicate to test that the id matches the target and with that array.
We could easily make this more generic by adding another initial parameter to deepFind to tell us how our tree is structured.  (`node -> node.children` is probably more common.)  But that's a task for another day.
`deepFind` has perhaps a slightly dense implementation.  It can be written in various ways, if one of these makes more sense to you:
```
const deepFind = (pred) => (xs) => {
  for (let x of xs) {
    if (pred (x)) {return x}
    return deepFind (pred) (x .child || [])
  }
}
```

or
```
const deepFind = (pred) => ([x, ...xs]) => 
  x == undefined
    ? undefined
    : pred (x) 
      ? x
      : deepFind (pred) (x.child || []) || deepFind (pred) (xs)
```

or atop a `traverse` generator function:
```
function * traverse (xs = []) {
  for (let x of xs) {
    yield x;
    yield * traverse (x.child || [])
  }
}

const deepFind = (pred) => (obj) => {
  for (let node of traverse ([obj])) {
    if (pred (node)) {return node} 
  }
}
```

and we could find many more.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-07-28 12:00:33

------------

```
var object = {
    "id": "A",
    "name": "Item A",
    "child": [
        {
            "id": "B",
            "name": "Item B",
            "child": [
                {
                    "id": "C",
                    "name": "Item C",
                    "child": []
                }
            ]
        },
        {
            "id": "D",
            "name": "Item D",
            "child": []
        }
    ]
}

const find_by_id = (target, object) => {
  if (object.id === target) {
    return object
  }
  for (let c of object.child) {
    let x = find_by_id(target, c) 
    if (x) {
      return x
    }
  }
}

const target = "C"
let result = find_by_id(target, object)
console.log(result)
```

Let me know if this fails in any case.


------------
    
    