
# Post \#55551405 [Link](https://stackoverflow.com/questions/55551405/)

## Tailwind CSS - Responsive breakpoints as components

**Vote**: 31 (190/702) **Views**: 49955 (163/702) 

**Internal ID** \#0-1-139

Created at 2019-04-06 16:59:57

Tags: `css` `media-queries` `responsive` `tailwind-css` `scss-mixins`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

How should I deal with responsive breakpoints as components in Tailwind?

Without Tailwind, I used to declare breakpoints as a scss mixins:

```
@mixin tablet-portrait {
  @media (min-width: 700px) {
    @content;
  }
}
```


Then:

```
@include tablet-portrait {
  // whatever
}
```


I know that Tailwind has responsive utility clases to use it inline as `md:color-red` but I need to abstract this breakpoins as components, as in above example.

How should I extract Tailwind breakpoints from Tailwind config file?


----------
        
## Answer \#0

**Accepted** Vote: 83

Created at 2019-04-06 17:44:53

------------

I found the @screen directive, that solves this question:
[https://tailwindcss.com/docs/functions-and-directives/#screen](https://tailwindcss.com/docs/functions-and-directives/#screen)
as easy as
```
@screen md {
  // whatever
}
```

Update. As mentioned in the comment, Taliwind 3.2.7 has different notation:
```
@media screen(sm) {
  /* ... */
}
```



------------
    
    
## Answer \#1

 Vote: 5

Created at 2021-02-04 00:55:47

------------

`@screen md` is not working when using SCSS.
Meanwhile, if you have your breakpoints (`screens` key) set in your `tailwind.config.js`, you can use this
```
.your-selector {
  // your usual CSS
  @media (min-width: theme('screens.xl.min')) {
    // your media-queried CSS when > xl breakpoint
  }
}
```



------------
    
    
## Answer \#2

 Vote: 3

Created at 2019-10-25 08:11:41

------------

By tailwind You can use and Customizing the default breakpoints for your project.

1. Open your tailwind.config.js
2. Update/add screens inside your module.exports: theme: {
  screens: {
    'sm': '640px',
    // => @media (min-width: 640px) { ... } 

    'md': '768px',
    // => @media (min-width: 768px) { ... }

    'lg': '1024px',
    // => @media (min-width: 1024px) { ... }

    'xl': '1280px',
    // => @media (min-width: 1280px) { ... }
  }
}


Source: [https://tailwindcss.com/docs/breakpoints](https://tailwindcss.com/docs/breakpoints)


------------
    
    
## Answer \#3

 Vote: 3

Created at 2021-11-20 02:22:18

------------

in tailwind.config.js
```
const defaultTheme = require("tailwindcss/defaultTheme");

module.exports = {
  
  theme: {
    screens: {
      // adding xs to the rest
      xs: "475px",
      // if you did not add this, you would have only "xs"
      ...defaultTheme.screens,
    },
  },
  
};
```



------------
    
    