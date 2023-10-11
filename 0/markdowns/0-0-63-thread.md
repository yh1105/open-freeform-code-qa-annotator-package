
# Post \#71871748 [Link](https://stackoverflow.com/questions/71871748/)

## How to disable button according to input length by using React useState?

**Vote**: 1 (672/702) **Views**: 1767 (613/702) 

**Internal ID** \#0-0-63

Created at 2022-04-14 12:37:41

Tags: `javascript` `reactjs` `react-hooks` `use-state`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have a simple page with input fields and a button and I want to make button disabled when both field length is less than 3. It is simple thing but I am confused about the hook because it is going into infinite loop.
Here is code:
```
function TableFooterPanel(props) {

    const [firstName, setFirstName] = useState('');
    const [lastName, setLastName] = useState('');
    // const [isButtonDisabled, setIsButtonDisabled] = useState(true);

    const addNewCustomer = async (name, surname) => {
        await service.addCustomer(name, surname);
        props.funcParam();
    }

    var isButtonDisabled = false;

    if (firstName <= 3 || lastName <= 3) {
        isButtonDisabled = true;
    }
    else {
        isButtonDisabled = false;
    }

    return (

        <>
            <Card className='buttonFooter'>
                <Form className='buttonFooter'>
                    <input type="text" placeholder="First Name" defaultValue={firstName} onChange={e => setFirstName(e.target.value)}></input>
                    <input type="text" placeholder="Last Name" defaultValue={lastName} onChange={e => setLastName(e.target.value)}></input>
                    <Button disabled={isButtonDisabled} onClick={() => addNewCustomer(firstName, lastName)}>Add</Button>
                </Form>
            </Card>

        </>

    );

}
export default TableFooterPanel;
```

With this code, the boxes only check whether they are empty or filled.
If I uncomment useState hook and try to set it in if-else condition then this time  is happening and page is crashing.
How can I check the input length without falling into infinite-loop?


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2022-04-14 12:48:52

------------

I suggest you to not write code directly in component's body: you don't know how many times that code will be exected. Much better use a `useEffect` hook:
```
function TableFooterPanel(props) {

const [firstName, setFirstName] = useState('');
const [lastName, setLastName] = useState('');
const [isButtonDisabled, setIsButtonDisabled] = useState(true);

const addNewCustomer = async (name, surname) => {
    await service.addCustomer(name, surname);
    props.funcParam();
}

useEffect(() => {
   if (firstName.length <= 3 || lastName.length <= 3) {
       setIsButtonDisabled(true);
   }
   else {
       setIsButtonDisabled(false);
   }
}, [firstName, lastName]);


return (

    <>
        <Card className='buttonFooter'>
            <Form className='buttonFooter'>
                <input type="text" placeholder="First Name" defaultValue={firstName} onChange={e => setFirstName(e.target.value)}></input>
                <input type="text" placeholder="Last Name" defaultValue={lastName} onChange={e => setLastName(e.target.value)}></input>
                <Button disabled={isButtonDisabled} onClick={() => addNewCustomer(firstName, lastName)}>Add</Button>
            </Form>
        </Card>

    </>

);

}
export default TableFooterPanel;
```

As you can see, I have used an `useEffect` hook with `firstName` and `lastName` in deps list: this means that, every time `firstName` or `lastName` change, `useEffect` will be fired.
And don't forget that, to check string length you have to use `length` property =).


------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-04-14 12:39:29

------------

Try :
```
if (firstName.length <= 3 || lastName.length <= 3) {
        isButtonDisabled = true;
    }
    else {
        isButtonDisabled = false;
    }
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-04-14 12:48:11

------------

Here you go: [Codesandbox demo](https://codesandbox.io/s/relaxed-architecture-nu1req?file=/src/App.js)
You could simple add the disabled argument inline like this:
```
<button
              disabled={firstName.length < 3 || lastName.length < 3}
              onClick={() => addNewCustomer(firstName, lastName)}
            >
              Add
            </button>
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-04-14 12:49:15

------------

Less than 3 is `< 3`, not `<= 3`
There's no need for an if condition here, just set the value of isButtonDisabled directly:
```
const isButtonDisabled = firstName.lenght < 3 || lastName.lenght < 3;
```

Or just do it inline:
```
<Button disabled={firstName.lenght < 3 || lastName.lenght < 3} />
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-04-14 12:50:11

------------

```
function TableFooterPanel(props) {

    const [firstName, setFirstName] = useState('');
    const [lastName, setLastName] = useState('');

    const addNewCustomer = async (name, surname) => {
        await service.addCustomer(name, surname);
        props.funcParam();
    }

    return (

        <>
            <Card className='buttonFooter'>
                <Form className='buttonFooter'>
                    <input type="text" placeholder="First Name" defaultValue={firstName} onChange={e => setFirstName(e.target.value)}></input>
                    <input type="text" placeholder="Last Name" defaultValue={lastName} onChange={e => setLastName(e.target.value)}></input>
                    <Button disabled={!(firstName.length < 3 || lastName.length < 3)} onClick={() => addNewCustomer(firstName, lastName)}>Add</Button>
                </Form>
            </Card>

        </>

    );

}
export default TableFooterPanel;
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-04-14 12:49:37

------------

You need to put them inside a `useEffect` otherwise it will re-run when anything on this page changes and make the `isButtonDisabled` variable as a state.
```
const [isButtonDisabled, setIsButtonDisabled] = useState(true)

useEffect(()=>{
   if (firstName.length <= 3 || lastName.length <= 3) {
       setIsButtonDisabled(true);
   } else {
       setIsButtonDisabled(false);
   }    
},[firstName, lastName])
```



------------
    
    