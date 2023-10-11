
# Post \#70099656 [Link](https://stackoverflow.com/questions/70099656/)

## Is it possible to style a checkbox like this?

**Vote**: 2 (631/702) **Views**: 1224 (645/702) 

**Internal ID** \#0-1-132

Created at 2021-11-24 16:30:30

Tags: `css` `sass`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

I have a design with a very specific way to add checkboxes. It is for a filter list of products. When the filter is selected, the checkbox should have a black box in the middle. However, I can't seem to find how to do it.
I have logic running from the checkbox on whether it is checked or not for some JavaScript so it definitely needs to remain a checkbox.
Anybody have any ideas?
[](https://i.stack.imgur.com/ki1PZ.png)


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2021-11-24 16:49:42

------------

The best way to do this is to remove the default appearance of the checkbox using `appearance: none;`. From there, you can change anything about its style you want to. Here is an example that gets close to what you are wanting.
```
/* The checkbox holder */
.checkbox {
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 10px;
}

/* The checkbox */
.checkbox > input {
  /*
    Remove the default appearance.
  */
  -webkit-appearance: none;
  -moz-appearance: none;
  -o-appearance: none;
  appearance: none;
  
  
  /*
    Set the size of the checkbox.
  */
  width: 20px;
  height: 20px;
  
  box-shadow: 0 0 0 2px black; /* Outer border */
  border: 3px solid white; /* Inner border */
}

/* The checkbox - when checked */
.checkbox > input:checked {
  background-color: black; /* The "check" */
}
```
```
<h1>Item List</h1>
<div class="checkbox">
  <input id="item1" type="checkbox">
  <label for="item1">Item 1</label>
</div>
<div class="checkbox">
  <input id="item2" type="checkbox">
  <label for="item2">Item 2</label>
</div>
<div class="checkbox">
  <input id="item3" type="checkbox">
  <label for="item3">Item 3</label>
</div>
```




------------
    
    
## Answer \#1

 Vote: 1

Created at 2021-11-24 16:44:34

------------

This is certainly not the  way, but you could set the `appearance: none` on the input and then replace it with custom content and `:after` content.  This seems to work OK in the snippet on Chrome-- not sure how well it would translate to other browsers.
```
input[type="checkbox"] {
  appearance: none;
  border: 1px solid black;
  height: 1rem;
  width: 1rem;
  vertical-align: bottom;
  display: inline-flex;
  align-items: center;
  justify-content: center;
}

input[type="checkbox"]:checked:after {
  content: "";
  background-color: black;
  height: 0.8rem;
  width: 0.75rem;
}
```
```
<label for="myCheckbox">My Checkbox</label>
<input type="checkbox" id="myCheckbox" name="myCheckbox" />
```




------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-11-24 18:08:12

------------

I will do that this way...
```
const myForm = document.forms['my-form']

myForm.onsubmit = e =>
  {
  e.preventDefault() // disable submit 

  let inputJSON =
    Array
    .from(new FormData(myForm))
    .reduce( (r, [name,value]) =>
      {
      if (name.endsWith('[]'))
        {
        name    = name.slice(0,-2)
        r[name] = r[name] ?? []
        r[name].push(value)
        }
      else r[name] = value; 
      return r
      } ,{})
  console.clear()
  console.log( inputJSON  )
  }
```
```
label { 
  display : block; 
  margin  : 10px; 
  }
input[type="checkbox"] {
  appearance     : none;
  --size         : 20px;
  position       : relative;
  width          : var(--size);
  height         : var(--size);
  vertical-align : text-bottom;
  }
input[type="checkbox"]::before {
  position   : absolute;
  display    : block;
  top        : 0;
  left       : 0;
  width      : 100%;
  height     : 100%;
  content    : '';
  box-sizing : border-box;
  border     : 2px solid darkblue;
  }
input[type="checkbox"]:checked::after {
  position   : absolute;
  display    : block;
  top        : 4px;
  left       : 4px;
  width      : calc(100% - 8px);
  height     : calc(100% - 8px);
  background : #3a5079;
  content    : '';
  } 

/* --- other thinks ---*/
fieldset {
  width : 240px;
  }
legend {
  text-transForm : uppercase;
  font-size      : 20px;
  }
  
/*-- just SO snippet --*/  
.as-console-wrapper { max-height:100% !important; top:0; left:50% !important; width:50%; }
.as-console-row         { background-color: yellow; }
.as-console-row::after  { display:none !important; }
```
```
<form name="my-form" >
  <fieldset>
    <legend>Ingredient</legend>
    <label><input type="checkbox" name="Ingredient[]" value="Beef"> Beef</label>
    <label><input type="checkbox" name="Ingredient[]" value="pork"> Pork</label>
    <label><input type="checkbox" name="Ingredient[]" value="Chicken"> Chicken</label>
    <label><input type="checkbox" name="Ingredient[]" value="Vegetables"> Vegetables</label>
  </fieldset>

  <button type="submit">submit</button>
</form>
```




------------
    
    