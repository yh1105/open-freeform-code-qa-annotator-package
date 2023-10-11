
# Post \#71093772 [Link](https://stackoverflow.com/questions/71093772/)

## How to truncate text in TailwindCSS?

**Vote**: 4 (532/702) **Views**: 9031 (430/702) 

**Internal ID** \#0-1-145

Created at 2022-02-12 16:36:41

Tags: `css` `tailwind-css`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

I am using `truncate` in TailwindCSS to make text ellipsis if text-overflow more than one line but it does not work.
My code not works below:
```
<div className="ml-1 inline-block">
   <span>Label: </span>
   <span className="font-semibold truncate">
     long texttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttt
   </span>
</div>
```




----------
        
## Answer \#0

**Accepted** Vote: 11

Created at 2022-02-12 17:48:23

------------

First thing first your parent or the element must have a specific width otherwise how the element suppose to know when to stop and truncate? also in order to truncate to work your element's display must not be display `inline` since `span` is display inline by default you should change it to `block`
```
<div className="ml-1 inline-block w-[200px]">
     <span>Label: </span>
     <span className="font-semibold truncate block">
       long texttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttt
     </span>
  </div>
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2023-01-27 11:47:08

------------

In order not to set a fixed width of the element, you can simply specify the `min-width` of the parent block.
```
<div className="ml-1 inline-block min-w-0">
         <span>Label: </span>
         <span className="font-semibold truncate block">
           long texttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttt
         </span>
     </div>
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2023-02-02 10:20:05

------------

Truncating in bare tailwind is pretty frustrating.
Please use this plugin instead
Install it from npm
```
npm install @tailwindcss/line-clamp
```

Then add it in tailwind.config.js file
```
plugins: [
    require('@tailwindcss/line-clamp')
  ],
```

To use it just add it as a class `className="line-clamp-1"`
Here is the documentation
[https://tailwindcss.com/blog/multi-line-truncation-with-tailwindcss-line-clamp](https://tailwindcss.com/blog/multi-line-truncation-with-tailwindcss-line-clamp)


------------
    
    