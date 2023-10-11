
# Post \#73364211 [Link](https://stackoverflow.com/questions/73364211/)

## hide open/active accordion when another accordion is clicked

**Vote**: 3 (575/702) **Views**: 980 (658/702) 

**Internal ID** \#0-2-165

Created at 2022-08-15 17:04:31

Tags: `javascript` `html` `jquery`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

I suspect the issue is with the line `const active = document.querySelector(".accordion.active");` in JS code below. It doesn't seem to be retrieving that element. Could you please help me debug it? Or should I use something else instead of `querySelector`? It is also found that `this.classList.add("active");` is not adding the "active class" to the accordion element when it is clicked.
```
document.addEventListener("DOMContentLoaded", function(event) {
  var acc = document.getElementsByClassName("accordion");
  var i;
  for (i = 0; i < acc.length; i++) {
    acc[i].addEventListener("click", function() {
      const active = document.querySelector(".accordion.active");
      console.log(active);
      if (active) {
        active.classList.remove('active'); // remove active class from accordions
      }
      this.classList.add("active"); // add it to this one
      
      this.classList.toggle("active");
      var panel = this.nextElementSibling;
      if (panel.style.display === "block") {
        panel.style.display = "none";
      } else {
        panel.style.display = "block";
      }
    });
  }
});
```
```
.accordion {
  background-color: #eee;
  color: #444;
  cursor: pointer;
  padding: 1.5rem;
  width: 100%;
  border: none;
  text-align: left;
  outline: none;
  font-size: 15px;
  transition: 0.4s;
}

.active,
.accordion:hover {
  background-color: #ccc;
}

.panel {
  padding: 0 18px;
  display: none;
  background-color: white;
  overflow: hidden;
  background: red;
}

.active+.panel {
  display: block;
}
```
```
<div class="row">
  <button class="accordion"><div class="question"><?php echo $label; ?></div></button>
  <div class="panel">
    <p class="answer">
      <?php echo $answer; ?>
    </p>
    </br>
  </div>
</div>
<div class="row">
  <button class="accordion"><div class="question"><?php echo $label; ?></div></button>
  <div class="panel">
    <p class="answer">
      <?php echo $answer; ?>
    </p>
    </br>
  </div>
</div>
```




----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2022-08-16 14:11:49

------------

You probably should re-think your approach since in your case, you will not even be in need of JavaScript - for the basics! If you need a custom accordion, then you can use JavaScript, and I try to explain to you how.
What you need is a clean HTML with `<details>` and `<summary>`. See this example:
```
<details class="accordion">
  <summary>Question 1</summary>
  <strong>Answer:</strong> Lorem ipsum dolor sit amet, consectetur adipisicing elit. Delectus molestias ex, rem ducimus quibusdam nihil aliquam corporis id sint aperiam dolores, accusantium culpa adipisci similique doloremque eius reiciendis. Veniam, perferendis.
</details>
<details class="accordion">
  <summary>Question 2</summary>
  <strong>Answer:</strong> Lorem ipsum dolor sit amet, consectetur adipisicing elit. Laborum doloribus tenetur tempore esse consectetur incidunt, distinctio eaque suscipit error fugit tempora, quas accusantium recusandae autem voluptatibus qui quasi molestiae odit.
</details>
```


With CSS you can style it the way you want it. If you want to remove the arrows, you can try it with `details > summary { list-style: none; }`. Also, you can use any other characters. In this example, we use the signs  (plus) and when the accordion is already opened, it should be  (minus.):
```
details > summary {
  list-style-type: '+ ';
}
details[open] > summary {
  list-style-type: "- ";
}
details > summary::-webkit-details-marker {
  display: none;
}

summary {
  background-color: #ccc;
}
```
```
<details class="accordion">
  <summary>Question 1</summary>
  <strong>Answer:</strong> Lorem ipsum dolor sit amet, consectetur adipisicing elit. Delectus molestias ex, rem ducimus quibusdam nihil aliquam corporis id sint aperiam dolores, accusantium culpa adipisci similique doloremque eius reiciendis. Veniam, perferendis.
</details>
<details class="accordion">
  <summary>Question 2</summary>
  <strong>Answer:</strong> Lorem ipsum dolor sit amet, consectetur adipisicing elit. Laborum doloribus tenetur tempore esse consectetur incidunt, distinctio eaque suscipit error fugit tempora, quas accusantium recusandae autem voluptatibus qui quasi molestiae odit.
</details>
```


As you can see, all open questions remain open. If you want only the active question to stay open, you may use JavaScript.
```
document.querySelectorAll('details').forEach((accordion) => {
  accordion.addEventListener('click', (e) => {
    document.querySelectorAll('details').forEach((event) => {
      if (accordion !== event) {
        event.removeAttribute('open');
      }
    });
  });
});
```
```
details > summary {
  list-style-type: '+ ';
}
details[open] > summary {
  list-style-type: "- ";
}
details > summary::-webkit-details-marker {
  display: none;
}

summary {
  background-color: #ccc;
}
```
```
<details class="accordion">
  <summary>Question 1</summary>
  <strong>Answer:</strong> Lorem ipsum dolor sit amet, consectetur adipisicing elit. Delectus molestias ex, rem ducimus quibusdam nihil aliquam corporis id sint aperiam dolores, accusantium culpa adipisci similique doloremque eius reiciendis. Veniam, perferendis.
</details>
<details class="accordion">
  <summary>Question 2</summary>
  <strong>Answer:</strong> Lorem ipsum dolor sit amet, consectetur adipisicing elit. Laborum doloribus tenetur tempore esse consectetur incidunt, distinctio eaque suscipit error fugit tempora, quas accusantium recusandae autem voluptatibus qui quasi molestiae odit.
</details>
```




------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-08-16 07:34:40

------------

You don't need to manipulate the `display` of the active element, your CSS already does that. Also you should not both add and toggle the active class on `this` - that is equivalent to removing it.
I've also added an if statement to check if the clicked element is already active so that collapsing it again works.
```
document.addEventListener("DOMContentLoaded", function(event) {
  var acc = document.getElementsByClassName("accordion");
  var i;
  for (i = 0; i < acc.length; i++) {
    acc[i].addEventListener("click", function() {
      const active = document.querySelector(".accordion.active");
      console.log(active);
      if (active) {
        active.classList.remove('active'); // remove active class from accordions
      }

      if (active !== this) {
        this.classList.toggle("active");
      }
    });
  }
});
```
```
.accordion {
  background-color: #eee;
  color: #444;
  cursor: pointer;
  padding: 1.5rem;
  width: 100%;
  border: none;
  text-align: left;
  outline: none;
  font-size: 15px;
  transition: 0.4s;
}

.active,
.accordion:hover {
  background-color: #ccc;
}

.panel {
  padding: 0 18px;
  display: none;
  background-color: white;
  overflow: hidden;
  background: red;
}

.active+.panel {
  display: block;
}
```
```
<div class="row">
  <button class="accordion"><div class="question">Label1</div></button>
  <div class="panel">
    <p class="answer">
      Answer goes here
    </p>
  </div>
</div>
<div class="row">
  <button class="accordion"><div class="question">Label2</div></button>
  <div class="panel">
    <p class="answer">
      Second answer
    </p>
  </div>
</div>
```




------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-08-16 07:50:57

------------

I always prefer delegation.
I am wrapping the accordion in a DIV and delegate the clicks from there
No need to show / hide the panes since the CSS `.active+.panel { display: block; }` does that for us
```
document.addEventListener("DOMContentLoaded", function(event) { // when page has loaded
  const acc = document.getElementById("accodionContainer"); // the container
  const buttons = acc.querySelectorAll(".accordion");       // the buttons in the container
  acc.addEventListener("click", e => { // any click in the container
    const currentButton = e.target.closest(".accordion"); // you have stuff inside the relevant element, make sure we use the .accordion element
    if (!currentButton) return // something not a (in a ) button was clicked
    // toggle clicked button, remove active from the rest
    buttons.forEach(acc =>  acc.classList[acc === currentButton ? "toggle" : "remove"]("active"));
  });
});
```
```
.accordion {
  background-color: #eee;
  color: #444;
  cursor: pointer;
  padding: 1.5rem;
  width: 100%;
  border: none;
  text-align: left;
  outline: none;
  font-size: 15px;
  transition: 0.4s;
}

.active,
.accordion:hover {
  background-color: #ccc;
}

.panel {
  padding: 0 18px;
  display: none;
  background-color: white;
  overflow: hidden;
}

.active+.panel {
  display: block;
}

.accordion.active span::after {
  content: "➖";
}

.accordion span::after {
  content: "➕";
}
```
```
<div id="accodionContainer">
  <div class="row">
    <button class="accordion"><span></span> Question 1</button>
    <div class="panel">
      <p class="answer">
        Answer 1
      </p>
    </div>
  </div>
  <div class="row">
    <button class="accordion"><span></span> Question 2</button>
    <div class="panel">
      <p class="answer">
        Answer 2
      </p>
    </div>
  </div>
</div>
```




------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-08-16 10:24:57

------------

//
this.classList.add("active"); is not adding the "active class" to the accordion element when it is clicked.
//
It is adding. But immediately the class is toggled, so it is removed. That toogle class line is commented.
I have added the  css for green color to active accordion, which you can see after moving the cursor off the accordion element.
```
document.addEventListener("DOMContentLoaded", function(event) {
  var acc = document.getElementsByClassName("accordion");
  var i;
  for (i = 0; i < acc.length; i++) {
    acc[i].addEventListener("click", function() {
      const active = document.querySelector(".accordion.active");
      console.log(active);
      if (active) {
        active.classList.remove('active'); // remove active class from accordions
        // this is if other heading is clicked
      }
      
      
     // this.classList.toggle("active");  // not needed
      var panel = this.nextElementSibling;
      if (panel.style.display === "block") {
      this.classList.remove("active"); // remove it to this one
        panel.style.display = "none";
      } else {
      this.classList.add("active"); // add it to this one
        panel.style.display = "block";
      }
    });
  }
});
```
```
.accordion {
  background-color: #eee;
  color: #444;
  cursor: pointer;
  padding: 1.5rem;
  width: 100%;
  border: none;
  text-align: left;
  outline: none;
  font-size: 15px;
  transition: 0.4s;
}
.accordion.active{
  background:green;
}

.active,
.accordion:hover {
  background-color: #ccc;
}

.panel {
  padding: 0 18px;
  display: none;
  background-color: white;
  overflow: hidden;
  background: red;
}

.active+.panel {
  display: block;
}
```
```
<div class="row">
  <button class="accordion"><div class="question">Accordion Label 1</div></button>
  <div class="panel">
    <p class="answer">
      Accordion Answer 1
    </p>
    </br>
  </div>
</div>
<div class="row">
  <button class="accordion"><div class="question">Accordion Label 2</div></button>
  <div class="panel">
    <p class="answer">
      Accordion Answer 1
    </p>
    </br>
  </div>
</div>
```




------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-08-16 10:41:50

------------

The below answer is not related to the question, but it is good to have the html hierarchy.
```
const accordionToggles = document.querySelectorAll('.accordion-toggle');
const accordionItems = document.querySelectorAll('.accordion-item');
const flush = true; // set to true,if only one accordion bodyto be open. set false, to open multiple accordion-body
accordionToggles.forEach((toggleBtn) => {
  toggleBtn.addEventListener('click', (event) => {
    const accordionItem = event.target.closest(".accordion-item");
    if (!flush) {
      accordionItem.classList.toggle('active');
      return;
    }
    if (accordionItem.classList.contains('active')) {
      accordionItem.classList.toggle('active');
      return;
    }
    accordionItems.forEach((item) => {
      item.classList.remove('active');
    })
    accordionItem.classList.add('active');
  })

});
```
```
.accordion {}

.accordion-item {
  border: 1px solid gray;
}

.accordion-item+.accordion-item {
  border-top: none;
}

.accordion-header {}

.accordion-toggle {
  width: 100%;
  border: none;
  height: 30px;
  border-bottom: 1px solid black;
  text-align: left;
  position: relative;
}

.accordion-toggle:after {
  position: absolute;
  top: 5px;
  right: 5px;
  font-size: 18px;
}

.accordion-body {
  padding: 10px;
  display: none;
}

.accordion-item.active .accordion-body {
  display: block;
}

.accordion-item .accordion-toggle:after {
  content: "+"
}

.accordion-item.active .accordion-toggle:after {
  content: "-"
}
```
```
<div class="accordion">
  <div class="accordion-item">
    <div class="accordion-header">
      <button class="accordion-toggle">Title 1</button>
    </div>
    <div class="accordion-body">
      Accordion Content 1
    </div>
  </div>
  <div class="accordion-item">
    <div class="accordion-header">
      <button class="accordion-toggle">Title 2</button>
    </div>
    <div class="accordion-body">
      Accordion Content 2
    </div>
  </div>
</div>
```




------------
    
    