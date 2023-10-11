
# Post \#67223337 [Link](https://stackoverflow.com/questions/67223337/)

## How to update values of every object in an array of objects in Javascript?

**Vote**: 5 (499/702) **Views**: 10798 (407/702) 

**Internal ID** \#0-0-87

Created at 2021-04-23 02:51:55

Tags: `javascript` `arrays` `reactjs` `replace`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm trying to update a value of  in all objects in an array(data) with the values from , my code is:
```
import "./styles.css";

const data = [
  { id: 1, car: "Toyota 2020", owner: "BM" },
  { id: 2, car: "Nissan", owner: "DK" },
  { id: 3, car: "Mazda", owner: "JA" },
  { id: 4, car: "Ford", owner: "DS" }
];
const newData = ["Audi", "Bentley", "BMW", "Buick"];


export default function App() {
  return (
    <div className="App">
      {data?.map((item) => {
        return <li key={item.id}>{item.car}</li>;
      })}
    </div>
  );
}
```

I don't know how to change car values in data with values from newData, so data would be like this:
```
const data = [
  { id: 1, car: "Audi", owner: "BM" },
  { id: 2, car: "Bentley", owner: "DK" },
  { id: 3, car: "BMW", owner: "JA" },
  { id: 4, car: "Buick", owner: "DS" }
];
```

[codesandbox](https://codesandbox.io/embed/agitated-silence-5zbk0?fontsize=14&hidenavigation=1&theme=dark)
Later in code I'm mapping through data and I need to display updated car values, I'm not sure which method to use. Any help greatly appreciated.


----------
        
## Answer \#0

**Accepted** Vote: 11

Created at 2021-04-23 02:53:57

------------

Just take the index of the object being iterated over, and look it up in the `newData` array?
```
const data = [
  { id: 1, car: "Toyota 2020", owner: "BM" },
  { id: 2, car: "Nissan", owner: "DK" },
  { id: 3, car: "Mazda", owner: "JA" },
  { id: 4, car: "Ford", owner: "DS" }
];
const newData = ["Audi", "Bentley", "BMW", "Buick"];

const newCars = data.map((obj, i) => ({ ...obj, car: newData[i] }));
console.log(newCars);
```




------------
    
    
## Answer \#1

 Vote: 3

Created at 2021-04-23 02:54:50

------------

You can achieve result with using [map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) over `data` or `newData`
```
const data = [
  { id: 1, car: "Toyota 2020", owner: "BM" },
  { id: 2, car: "Nissan", owner: "DK" },
  { id: 3, car: "Mazda", owner: "JA" },
  { id: 4, car: "Ford", owner: "DS" },
];
const newData = ["Audi", "Bentley", "BMW", "Buick"];
const result = newData.map((c, i) => {
  return { ...data[i], car: c };
});

console.log(result);
```


with map over newData
```
const data = [
  { id: 1, car: "Toyota 2020", owner: "BM" },
  { id: 2, car: "Nissan", owner: "DK" },
  { id: 3, car: "Mazda", owner: "JA" },
  { id: 4, car: "Ford", owner: "DS" },
];
const newData = ["Audi", "Bentley", "BMW", "Buick"];
const result = data.map((obj, i) => {
  return { ...obj, car: newData[i] };
});

console.log(result);
```




------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-04-23 03:11:32

------------

you can with a button when you click it all the new car values gonna update.
but the UI will not update.
let's pretend that you have an initial array and you want to override all the car values you have to create a new variable with useState() hook and put data array as the initial value.
then you have to do a useeffect() hook to update the state after you get the new car data from the server, or put a settimeout() and update the state with it
```
[data,setdata]=useState(data)

   useEffect(() => {
   /// do whatever with the original array (data)

  setTimeout(() => {
  /// update the state 
  setdata(thenewarry)
  }, 3000);
  }, []);
```



------------
    
    