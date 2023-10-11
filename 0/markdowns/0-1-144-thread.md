
# Post \#66335238 [Link](https://stackoverflow.com/questions/66335238/)

## Changing the color arrow in Bootstrap

**Vote**: 8 (428/702) **Views**: 11377 (402/702) 

**Internal ID** \#0-1-144

Created at 2021-02-23 14:39:06

Tags: `css` `twitter-bootstrap`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

I am beginner web developer. I have in my website accordion:  [https://getbootstrap.com/docs/5.0/components/accordion/](https://getbootstrap.com/docs/5.0/components/accordion/)
and this code:
```
<div class="accordion pb-3" id="accordionExample">
                    <div class="accordion-item">
                        <h2 class="accordion-header" id="headingOne">
                            <button class="accordion-button" type="button" data-bs-toggle="collapse" data-bs-target="#collapseOne" aria-expanded="true" aria-controls="collapseOne" style="background-color: red;border:0">
                                name and surname
                            </button>
                        </h2>
                        <div id="collapseOne" class="accordion-collapse collapse show" aria-labelledby="headingOne" data-bs-parent="#accordionExample" style="background-color: red;">
                            <div class="accordion-body">
                                This is aółóźńthe first item's accordion body.It is hidden by default, until the collapse plugin adds the appropriate classes that we use to style each element. These classes control the overall appearance, as well as the showing and hiding via CSS transitions. You can modify any of this with custom CSS or overriding our default variables. It's also worth noting that just about any HTML can go within the <code>.accordion-body</code>, though the transition does limit overflow.
                            </div>
                        </div>
                    </div>
                </div>
```

It's work fine. I would like to change the accordion arrow color to green. How can this be done?
Please help me


----------
        
## Answer \#0

**Accepted** Vote: 19

Created at 2021-02-23 15:07:58

------------

Bootstrap uses background images in their accordion arrows. You could edit the fill property of the linked svg.
I checked up on the svg used on [https://getbootstrap.com/docs/5.0/components/accordion/](https://getbootstrap.com/docs/5.0/components/accordion/), and changed the color to white with css
```
.accordion-button:after {
  background-image: url("data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 16 16' fill='%23ffffff'><path fill-rule='evenodd' d='M1.646 4.646a.5.5 0 0 1 .708 0L8 10.293l5.646-5.647a.5.5 0 0 1 .708.708l-6 6a.5.5 0 0 1-.708 0l-6-6a.5.5 0 0 1 0-.708z'/></svg>") !important;
}
```

Note here that I wrote `fill="%23ffffff"`, this changes the color to #ffffff (%23 is parsed to #)
You can find my codepen [here](https://codepen.io/harmlessharm/pen/NWbwjJe)
I would like to add that I don't think this is the most elegant way of solving this. You might want to change the content of the `:after` element of the `.accordion-button`.
Take a look at [Adding arrows to the right using Bootstrap Accordion](https://stackoverflow.com/questions/51024532/adding-arrows-to-the-right-using-bootstrap-accordion)


------------
    
    
## Answer \#1

 Vote: 5

Created at 2021-05-03 23:52:01

------------

You could override the selector in your own CSS file - `.accordion-button:not(.collapsed)::after`
with:
```
background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 16 16' fill='%23212529'%3e%3cpath fill-rule='evenodd' d='M1.646 4.646a.5.5 0 0 1 .708 0L8 10.293l5.646-5.647a.5.5 0 0 1 .708.708l-6 6a.5.5 0 0 1-.708 0l-6-6a.5.5 0 0 1 0-.708z'/%3e%3c/svg%3e");
```

This is how I did it for a project using Bootstrap v5 in order to keep the arrow colour black as I have changed the background colour.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-08-02 09:26:53

------------

This is the solution that worked for me and also note the two colons before the css after rule
```
.accordion-button::after {
    background-image: url("data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 16 16' fill='%23ffffff'><path fill-rule='evenodd' d='M1.646 4.646a.5.5 0 0 1 .708 0L8 10.293l5.646-5.647a.5.5 0 0 1 .708.708l-6 6a.5.5 0 0 1-.708 0l-6-6a.5.5 0 0 1 0-.708z'/></svg>") !important;
 }
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-08-04 15:36:06

------------

I did have the same issue and this worked for me.
```
background-image: url(arrow_right.svg)!important;
```

but you will also need to work on the transformation part of the css also like in my case the below worked.
```
.accordion-button:not(.collapsed)::after {transform: rotate(-90deg)!important;}
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-12-12 00:19:07

------------

```
This Worked for me try this solution

 .accordion-button:not(.collapsed)::after {
  background-color: #d2153d;
  background-image: url("/img/Home/FAQ/arrow-up.png");
  transform: rotate(360deg);
  }

  .accordion-button.collapsed::after {
  background-image: url("/img/Home/FAQ/arrow-down.png");
  }

  .accordion-button:not(.collapsed) {
  color: #ffffff;
  background-color: #d2153d;
  }
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-12-18 15:21:14

------------

This worked for me, by using the filter property and finding my desired invert percentage.
```
.accordion-button:not(.collapsed)::after {
    filter: invert(50%);
}
```



------------
    
    