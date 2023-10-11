
# Post \#70906977 [Link](https://stackoverflow.com/questions/70906977/)

## Tailwind underline hover animation

**Vote**: 9 (409/702) **Views**: 8831 (437/702) 

**Internal ID** \#0-1-149

Created at 2022-01-29 15:32:03

Tags: `css` `tailwind-css`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

I've spent a day figuring out how to make an animation after hovering over the link by using Tailwind-CSS.
Here is the animation I want mine link looks like the video.
[Sample from Youtube](https://www.youtube.com/watch?v=aswRKAjjWuE)
I have tried using `:after`, but it didn't work out.
Here is my link component => [https://codepen.io/qqharry21/pen/xxPwqjQ](https://codepen.io/qqharry21/pen/xxPwqjQ)
I Hope can learn how to fix it, and make it works like the video by Tailwind-CSS, thanks!


----------
        
## Answer \#0

**Accepted** Vote: 9

Created at 2022-01-29 15:46:14

------------

You can you transition for that:
```
.link-underline {
        border-bottom-width: 0;
        background-image: linear-gradient(transparent, transparent), linear-gradient(#fff, #fff);
        background-size: 0 3px;
        background-position: 0 100%;
        background-repeat: no-repeat;
        transition: background-size .5s ease-in-out;
    }

    .link-underline-black {
        background-image: linear-gradient(transparent, transparent), linear-gradient(#F2C, #F2C)
    }

    .link-underline:hover {
        background-size: 100% 3px;
        background-position: 0 100%
    }
```
```
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/tailwindcss/2.2.19/tailwind.min.css" integrity="sha512-wnea99uKIC3TJF7v4eKk4Y+lMz2Mklv18+r4na2Gn1abDRPPOeef95xTzdwGD9e6zXJBteMIhZ1+68QC5byJZw==" crossorigin="anonymous" referrerpolicy="no-referrer" />

<div class="min-h-screen bg-gray-100 py-6 flex flex-col justify-center sm:py-12">
    <div class="relative py-3 sm:max-w-xl sm:mx-auto">
        <a href="#" class="font-display max-w-sm text-2xl font-bold leading-tight">
            <span class="link link-underline link-underline-black text-black"> Link Hover Effect </span>
        </a>
    </div>
</div>
```




------------
    
    
## Answer \#1

 Vote: 18

Created at 2022-07-08 16:00:27

------------

You can achieve that in pure tailwind with group max-w-{x} and transition-all, use group-hover on the span so when you pass over the link it start the animation
```
<a href="#" class="group text-sky-600 transition duration-300">
Link
<span class="block max-w-0 group-hover:max-w-full transition-all duration-500 h-0.5 bg-sky-600"></span>
</a>
```



------------
    
    
## Answer \#2

 Vote: 6

Created at 2022-07-19 21:12:28

------------

If you're not wanting to include a CSS file just for this functionality or you just want to do it in Tailwind - the code snippet I've posted below is based purely on Tailwind CSS.
```
<a class='group text-pink-500 transition-all duration-300 ease-in-out' href='#'>
  <span class='bg-left-bottom bg-gradient-to-r from-pink-500 to-pink-500 bg-[length:0%_2px] bg-no-repeat group-hover:bg-[length:100%_2px] transition-all duration-500 ease-out'>
    This text gets 'underlined' on hover
  </span>
</a>
```

Cheers and happy coding!


------------
    
    