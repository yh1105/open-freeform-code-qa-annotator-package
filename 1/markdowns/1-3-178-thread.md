
# Post \#64306147 [Link](https://stackoverflow.com/questions/64306147/)

## Using Playwright for Python, how do I select an option from a drop down list?

**Vote**: 8 (428/702) **Views**: 11436 (401/702) 

**Internal ID** \#1-3-178

Created at 2020-10-11 16:01:34

Tags: `python` `playwright` `playwright-python`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

This is a followup to this [question](https://stackoverflow.com/questions/64303326/using-playwright-for-python-how-do-i-select-or-find-an-element) on the basic functionality of Playwright for Python.
How do I  an  from a ?
This example remote controls a vuejs-webseite that has a drop down list of fruits like "Apple", "Banana", "Carrot", "Orange"
Here I want to select the option "Banana"
```
from playwright import sync_playwright
import time

URL = '<my url>'

with sync_playwright() as p:
    browser = p.chromium.launch(headless=False)
    page = browser.newPage()
    page.goto(URL)

    # identify this element by ID. Wait for it first
    new_selector = 'id=name-fruit'
    page.waitForSelector(new_selector)
    handle = page.querySelector(new_selector)

    # at this point I have the element and can print the content
    print(handle.innerHTML())
```

The drop down list HTML like this
```
<select data-v-c2cef47a="" id="name-fruit" autocomplete="true" class="form-select__select">
    <option data-v-c2cef47a="" disabled="disabled" value=""><!----></option>
    <option data-v-c2cef47a="" value="[object Object]"> Apple </option>
    <option data-v-c2cef47a="" value="[object Object]"> Banana </option>
    <option data-v-c2cef47a="" value="[object Object]"> Carrot </option>
    <option data-v-c2cef47a="" value="[object Object]"> Orange </option> 
</select>
```

in Selenium, I would select an option like this
```
from selenium.webdriver.support.ui import Select
Select(handle).select_by_visible_text('Banana')  # Note: no spaces needed!
```

The Javascript docs for Playwright have this, which is not exactly the same, because it seems to identify the object at the same time.
```
// Single selection matching the label
await page.selectOption('select#colors', { label: 'Blue' });
```

How do I do the selection in Playwright for Python?
I tried both of these and nothing happens.
```
handle.selectOption("text=Banana") 
handle.selectOption("text= Banana ")
```



----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2020-10-11 17:04:00

------------

After trying many different variants,
I guessed a working syntax
```
handle.selectOption({"label": "Banana"})
```



------------
    
    
## Answer \#1

 Vote: 4

Created at 2021-06-23 07:49:39

------------

A working example for Playwright for Python:
```
page.select_option('select#colors', label='Banana')
```

or for JavaScript:
```
await page.selectOption('select#colors', { label: 'Banana' });
```

See [here](https://playwright.dev/python/docs/input#select-options) for further handling of how to interact with selectors and how to use it in different languages and scenarios like JavaScript.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-12-22 00:22:25

------------

You can also alternatively use the index or the value to select an option:
```
handle.selectOption([{'label': 'Banana'}])  # selects 'Banana'
handle.selectOption([{'index': 3}])         # selects 'Carrot'
handle.selectOption([{'value': ''}])        # selects the empty option (works even though it is disabled)
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-05-06 08:57:42

------------

You don't need the handle, you can use the page or frame directly.
Using playwright-python:
```
page.select_option('select#name-fruit', label='Banana')
page.select_option('select#name-fruit', value='')
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2023-01-17 05:50:47

------------

If you have to select a list option based on "contains" , here is what I used
- Basically get a locator to find the option with text- Get the full text of the option- Use that full text to select the option```
dropdown_locator = page.locator(DROPDOWN_LOCATOR)
  dropdown_locator = dropdown_locator.filter(has_text="Banana")
  options_txt = activity_locator.text_content()
  options = options_txt.split('\n')
  print("Monkey searching banana in " + str(options))
  full_label = ""
  for item in options:
      if "Banana" in item:
          full_label = item.strip()
  print(full_label)

  if len(full_label) != 0:
      page.select_option(DROPDOWN_LOCATOR, label=full_label)
```



------------
    
    