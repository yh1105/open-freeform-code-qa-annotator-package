
# Post \#61455473 [Link](https://stackoverflow.com/questions/61455473/)

## How to use :not() in tailwind.css?

**Vote**: 40 (158/702) **Views**: 40131 (195/702) 

**Internal ID** \#0-1-148

Created at 2020-04-27 09:17:33

Tags: `css` `css-selectors` `tailwind-css`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

I recently started to give tailwind.css a try in my Nuxt project. so I needed to use `:not(:last-child)` pseudo-elements but I didn't find how.

```
<ul>
    <li
      v-for="(item, index) in items"
      :key="`item-${index}`"
      class="border-solid border-b border-black"
    >
      Item
    </li>
  </ul>
```


I want to add a border-bottom to all of the `<li>` except the last one.

I know Tailwind has [first](https://tailwindcss.com/docs/pseudo-class-variants/#first-child-v1-1-0) & [last](https://tailwindcss.com/docs/pseudo-class-variants/#last-child-v1-1-0) pseudo-class variant but I can't use them with `:not()`


----------
        
## Answer \#0

**Accepted** Vote: 54

Created at 2020-04-28 22:29:45

------------

The answer is in the link to [last in the docs](https://tailwindcss.com/docs/pseudo-class-variants#last-child-v1-1-0), that you shared.

Just add `last:border-b-0` to all list items, and it will remove the `border-bottom` if it is the `last-child`.

```
<ul>
  <li
    v-for="(item, index) in items"
    :key="`item-${index}`"
    class="border-solid border-b border-black last:border-b-0"
  >
    Item
  </li>
</ul>
```



------------
    
    
## Answer \#1

 Vote: 17

Created at 2022-11-15 12:50:45

------------

You can use [Tailwind arbitrary variants](https://tailwindcss.com/docs/adding-custom-styles#arbitrary-variants):
```
<li class="[&:not(:last-child)]:border border-sky-500">Item</li>
```

This is available since [Tailwind v3](https://tailwindcss.com/blog/tailwindcss-v3).


------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-09-16 11:10:00

------------

TESTED
We could also do this by selecting the index number we want to edit
EXAMPLE: I will change the first one, and all the following should have a style
```
<div
                    v-for="(item, i) in items"
                    :key="i"
                    :class="{ 'mx-0': i === 0, 'mx-4': i > 0 }"
                  >
                  </div>
```

so the FIRST element has an index from 0, and therefore we will have a margin x 0,
and all of the following would have margin x 4.


------------
    
    