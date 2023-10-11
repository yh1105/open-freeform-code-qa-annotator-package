
# Post \#57087145 [Link](https://stackoverflow.com/questions/57087145/)

## Check if all required fields are filled in a specific div

**Vote**: 11 (370/702) **Views**: 50798 (160/702) 

**Internal ID** \#0-2-163

Created at 2019-07-18 04:29:51

Tags: `javascript` `jquery` `html` `validation` `required`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

I have some form fields that are dynamically generated form the database. There are inputs, checkboxes, radio buttons, textarea's, and select's. All the fields are in a parent div with ID `dynamic-form-fields`. Some fields have a `required` attribute.

This is a multi-step form so each "step/page" needs to be validated before going on to the next step. So it's not submitting the form.

How can I check if all the required fields in div `dynamic-form-fields` are filled?

Here's something what my HTML code looks like:

```
<input type="text" name="dff[]" placeholder="Name" required>
<input type="text" name="dff[]" placeholder="Date of Birth">
<input type="text" name="dff[]" placeholder="Email" required>
<input type="radio" name="gender" value="Male" required>
<input type="radio" name="gender" value="Female" required>
<select name="pet">
    <option value="dog">Dog</option>
    <option value="cat">Cat</option>
    <option value="bird">Bird</option>
</select>

<button id="check">Check</button>

<script>
    document.getElementById("check").onclick = function () {
        if() {
            alert('error please fill all fields!');
        }
    };
</script>
```


As you see, some fields are required and some are not. It is different every time so the solution has to be dynamic. There are also different types of input's such as select, radio, checkbox, text, etc... How can I make sure every "" field inside the div ID `dynamic-form-fields` is filled?


---





Maybe something like this but how do I get every type of input (text, radio, checkbox, select, etc...) under the div ID `dynamic-form-fields`?

```
$('#dynamic-form-fields input:required').each(function() {
  if ($(this).val() === '')
      alert('error please fill all fields!');
});
```



----------
        
## Answer \#0

**Accepted** Vote: 23

Created at 2019-07-18 04:38:01

------------

```
document.getElementById("check").onclick = function() {
  let allAreFilled = true;
  document.getElementById("myForm").querySelectorAll("[required]").forEach(function(i) {
    if (!allAreFilled) return;
    if (i.type === "radio") {
      let radioValueCheck = false;
document.getElementById("myForm").querySelectorAll(`[name=${i.name}]`).forEach(function(r) {
        if (r.checked) radioValueCheck = true;
      })
      allAreFilled = radioValueCheck;
      return;
    }
    if (!i.value) { allAreFilled = false;  return; }
  })
  if (!allAreFilled) {
    alert('Fill all the fields');
  }
};
```
```
<div id="myForm">
  <input type="text" name="dff[]" placeholder="Name" required>
  <input type="text" name="dff[]" placeholder="Date of Birth">
  <input type="text" name="dff[]" placeholder="Email" required>
  <input type="radio" name="gender" value="Male" required>
  <input type="radio" name="gender" value="Female" required>
  <select name="pet" required>
    <option value="">Select a pet</option>
    <option value="dog">Dog</option>
    <option value="cat">Cat</option>
    <option value="bird">Bird</option>
  </select>
</div>
<button id="check">Check</button>
```




------------
    
    
## Answer \#1

 Vote: 10

Created at 2019-07-18 04:48:39

------------

You can use `:invalid` to do this.

Demo:

```
$('#check').on("click", function(){
  let valid = true;
  $('[required]').each(function() {
    if ($(this).is(':invalid') || !$(this).val()) valid = false;
  })
  if (!valid) alert("error please fill all fields!");
  else alert('valid');
})
```
```
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<input type="text" name="dff[]" placeholder="Name" required>
<input type="text" name="dff[]" placeholder="Date of Birth">
<input type="text" name="dff[]" placeholder="Email" required>
<input type="radio" name="gender" value="Male" required>
<input type="radio" name="gender" value="Female" required>
<select name="pet" required>
    <option selected disabled>Select a pet</option>
    <option value="dog">Dog</option>
    <option value="cat">Cat</option>
    <option value="bird">Bird</option>
</select>

<button id="check">Check</button>
```




------------
    
    
## Answer \#2

 Vote: 2

Created at 2019-07-18 04:42:47

------------

You could keep separate checks based on input type, consider the following:

```
var isEmpty = false;
$("#check").on('click', function() {
  isEmpty = false
  $('input[type=text]:required').each(function() {
    if ($(this).val() === '')
      isEmpty = true;
  });
  var radioSelected = false;
  //$('input[name=gender]:required').each(function() {
  $('input[type=radio]:required').each(function() {
    if ($(this).is(':checked'))
      radioSelected = true;
  });

  if (!radioSelected)
    isEmpty = true;

  if ($("[name=pet]").val() == "")
    isEmpty = true;

  //if (!$(".cb").is(":checked"))
  //  isEmpty = true;

  if (isEmpty)
    alert('error please fill all fields!');
});
```
```
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<input type="text" name="dff[]" placeholder="Name" required>
<input type="text" name="dff[]" placeholder="Date of Birth">
<input type="text" name="dff[]" placeholder="Email" required>
<input type="radio" name="gender" value="Male" required>
<input type="radio" name="gender" value="Female" required>
<select name="pet" required>
  <option value="dog">Dog</option>
  <option value="cat">Cat</option>
  <option value="bird">Bird</option>
</select>
<!--<input type="checkbox" class="cb" required />-->
<button id="check">Check</button>
```




------------
    
    
## Answer \#3

 Vote: 1

Created at 2019-07-18 04:40:00

------------

You can check is `required` attribute available or not as,

```
$('#dynamic-form-fields').each(function() {
    var hasRequired = $(this).attr('required');
    if (typeof hasRequired !== "undefined" && hasRequired !== false) {
        // This is required inputs.
    }
}
```


Ref : [Link](https://stackoverflow.com/questions/1318076/jquery-hasattr-checking-to-see-if-there-is-an-attribute-on-an-element)


------------
    
    