
# Post \#67119992 [Link](https://stackoverflow.com/questions/67119992/)

## How to access all the direct children of a div in tailwindcss?

**Vote**: 39 (159/702) **Views**: 48682 (168/702) 

**Internal ID** \#0-2-166

Created at 2021-04-16 06:14:49

Tags: `html` `css` `tailwind-css`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

I have this html:
```
<div class="section">
   <div class="header">header</div>
   <div class="content">
      <div>sub contents 1</div>              
      <div>sub contents 2</div>
   </div>
</div>
```

I want to access the direct children of div with class "section" which would be divs with class: "header" and "content".
I know with css we can do: `div.section > div`
But how to do this using ?


----------
        
## Answer \#0

**Accepted** Vote: 65

Created at 2022-04-08 10:41:59

------------

By default you can't. However I use these simple lines in `tailwind.config.js` to give me `child` and `child-hover` options.
```
plugins: [
    function ({ addVariant }) {
        addVariant('child', '& > *');
        addVariant('child-hover', '& > *:hover');
    }
],
```

Then use like this
```
<div class="child:text-gray-200 child-hover:text-blue-500">...</div>
```

Which will give every child a gray textcolor, and a blue one on hover.
See [here](https://tailwindcss.com/docs/plugins#adding-variants) for more information on adding variants using a plugin
 4th of july 2022: Tailwind added an ad-hoc approach to target specific elements. You can now use `[&>*]:text-gray-200` or `[&>*:hover]:text-blue-500` to mimic the above behaviour. See the answer of @phum for more info!


------------
    
    
## Answer \#1

 Vote: 132

Created at 2022-06-20 07:07:21

------------

In tailwind 3.1, you can use arbitrary values to target child elements.
```
<div class="[&>*]:p-4">...</div>
<div class="[&>p]:mt-0 ">...</div>
```

[https://tailwindcss.com/blog/tailwindcss-v3-1#arbitrary-values-but-for-variants](https://tailwindcss.com/blog/tailwindcss-v3-1#arbitrary-values-but-for-variants)


------------
    
    
## Answer \#2

 Vote: 3

Created at 2021-04-16 06:23:35

------------

If you want to access the direct children of div with selector, please use `@layer` directive. see below:
```
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  div.section > div {
    @apply text-xl;
  }
}
```

[https://tailwindcss.com/docs/adding-base-styles](https://tailwindcss.com/docs/adding-base-styles)


------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-01-25 14:29:33

------------

This is currently not possible and probably won't be implemented soon.
Instead, I recommend using this plugin:  [https://github.com/benface/tailwindcss-children](https://github.com/benface/tailwindcss-children). Follow the README for further instructions.

### Usage:


After you have installed the plugin and added it to your `tailwind.config.js`, you can access direct children by adding `children:{your_style}` to the parent class. If you for example would want to add `p-4` to `header` and `content`, your code would look like this:
```
<div class="section children:p-4">
   <div class="header">header</div>
   <div class="content">
      <div>sub contents 1</div>              
      <div>sub contents 2</div>
   </div>
</div>
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-04-19 04:23:54

------------

This is tailwind v1&v2 version of Willem Mulder's.
only change is variant name `children` instead of `child`
```
plugin(function({ addVariant, e }) {

  addVariant('children', ({ modifySelectors, separator }) => {
    modifySelectors(({ className }) => {
      const newClass = e(`children${separator}${className}`);
      return [
        `.${newClass} > *`,
        // `.${newClass}:hover `,
      ].join(",");
    });
  });

  addVariant('children-first', ({ modifySelectors, separator }) => {
    modifySelectors(({ className }) => {
      const newClass = e(`children-first${separator}${className}`);
      return [
        `.${newClass} > *:first-child`,
      ].join(",");
    });
  });

}),
```

add variants for padding
```
variants: {
    padding: ['responsive', 'children', 'children-hover', 'children-first', ],
   
  },
```



------------
    
    