
# Post \#66052793 [Link](https://stackoverflow.com/questions/66052793/)

## Angular typescript convert string to number

**Vote**: 3 (575/702) **Views**: 12950 (384/702) 

**Internal ID** \#0-0-7

Created at 2021-02-04 19:47:05

Tags: `angular` `typescript`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I want to convert a `string number` (an integer represented as a string. ex "12" ) to a `number`

```
export class Person{
  FirstName :  string | undefined ;
  Telephone:  number | undefined ;

}
```


```
console.log("Person" + JSON.stringify(this.person));
```

The JSON appears as
```
{
    "FirstName ": "hhhhh",
    "Telephone": "1478525",
}
```

The Telephone property is a number and I want it to appear as follows (without the ""_ :
```
{
    "FirstName ": "hhhhh",
    "Telephone": 1478525,
}
```

Approaches I took.
```
this.person.Telephone = parseInt(telephone);
```

Above didn't work, it shows the same number enclosed as a `string`. I also tried the following:
```
this.person.Telephone as number
```

both approaches didn't work. Can someone help me solve this ?
Error I get if I don't convert to a number:
> "The JSON value could not be converted to System.Int32. Path:
$.Telephone | LineNumber: 2 | BytePositionInLine: 18."


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2021-02-04 19:58:15

------------

I think you can try this =>
```
let stringToNumberData = "123";
let numberValue = Number(stringToNumberData);
console.log(numberValue);
//Returns 123
```

OR
```
if(!isNaN(Number(stringToNumberData ))){
  let numberValue = Number(stringToNumberData );
  console.log(numberValue);
} else{
    console.log('Not a Number');
}
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-02-04 20:00:04

------------

Just use the standard javascript [Number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)
```
this.person.Telephone = Number(telephone); 
console.log("Person" + JSON.stringify(this.person));
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-02-04 20:01:19

------------

You can use unary operator +
```
export class Person{
  FirstName :  string | undefined;
  Telephone:  number | undefined;
}
let person = new Person();
person.FirstName = "Name";
person.Telephone = 12345;
console.log(person);
person.Telephone = +"12345";
console.log(person);
```

Output:
```
Person { FirstName: 'Name', Telephone: 12345 }
Person { FirstName: 'Name', Telephone: 12345 }
```

PS: Use string as the data type for phone numbers.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-02-05 01:12:47

------------

If you want to get a number from a string, you can use this approach.
```
let person = {
name: "Some Name",
age: "45"
};

console.log('Person obj before modification ==>', person);

person.age = parseInt(person.age, 10);

console.log('Person object after modifying ==>', person);
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-06-21 02:11:54

------------

One scenario where the string to number conversion might be needed is while fetching the params from the activated route in Angular.
The easiest way is to append a + operator at the beginning.
```
constructor(private serversService: ServersService, private actRoute: ActivatedRoute) { }



ngOnInit() {
    const id = +this.actRoute.snapshot.params['id'];//convert param string to number if needed
    this.server = this.serversService.getServer(id);
    this.actRoute.params.subscribe((params: Params) => {
      this.server = this.serversService.getServer(+params['id']);
    })
  }
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-10-27 15:04:04

------------

```
ngOnInit(): void {
    const stringValue: string = '341';
    const numberValue: number = +stringValue;
    // numberValue === 341
  }
```



------------
    
    