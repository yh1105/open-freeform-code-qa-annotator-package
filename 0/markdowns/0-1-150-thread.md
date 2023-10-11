
# Post \#67133460 [Link](https://stackoverflow.com/questions/67133460/)

## How to make a triangle shape with Tailwind?

**Vote**: 7 (449/702) **Views**: 17833 (323/702) 

**Internal ID** \#0-1-150

Created at 2021-04-16 23:56:44

Tags: `css` `tailwind-css`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

```
<div class="">
                <div class="w-16 h-16 border-b-30 border-l-30 border-solid border-black">
                    <div class="h-16 w-16 border-t-30 border-r-30 bg-transparent"></div>
                </div>
</div>
```

how to make a triangle with tailwindCss without plugin ??


----------
        
## Answer \#0

**Accepted** Vote: 32

Created at 2021-04-17 00:19:43

------------

You may try using transform:
```
<link href="https://unpkg.com/tailwindcss@^2/dist/tailwind.min.css" rel="stylesheet">
<div class="w-16 overflow-hidden inline-block">
 <div class=" h-11 w-11 bg-black rotate-45 transform origin-bottom-left"></div>
</div>

<div class="w-16 overflow-hidden inline-block">
 <div class=" h-11 w-11 bg-black -rotate-45 transform origin-top-left"></div>
</div>

<div class="w-11  overflow-hidden inline-block">
 <div class=" h-16  bg-black -rotate-45 transform origin-top-right"></div>
</div>

<div class="w-11  overflow-hidden inline-block">
 <div class=" h-16  bg-black rotate-45 transform origin-top-left"></div>
</div>

<div class="w-11  overflow-hidden inline-block">
 <div class=" h-16  bg-black -rotate-45 transform origin-bottom-right"></div>
</div>

<div class="w-11  overflow-hidden inline-block">
 <div class=" h-16  bg-black rotate-45 transform origin-bottom-left"></div>
</div>

<div class="w-11  overflow-hidden inline-block">
 <div class=" h-16  bg-black -rotate-45 transform origin-top-left"></div>
</div>

<div class="w-11  overflow-hidden inline-block">
 <div class=" h-16  bg-black rotate-45 transform origin-top-right"></div>
</div>
```




------------
    
    
## Answer \#1

 Vote: 5

Created at 2022-04-19 14:57:33

------------

You can also try using borders
[tailwind playground](https://play.tailwindcss.com/RCiIARRSxx)
```
<!-- down -->
<div class="border-solid border-t-black border-t-8 border-x-transparent border-x-8 border-b-0"></div>
<!-- up -->
<div class="border-solid border-b-black border-b-8 border-x-transparent border-x-8 border-t-0"></div>
<!-- left -->
<div class="border-solid border-r-black border-r-8 border-y-transparent border-y-8 border-l-0"></div>
<!-- right -->
<div class="border-solid border-l-black border-l-8 border-y-transparent border-y-8 border-r-0"></div>
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-06-10 17:38:54

------------

Maybe you want to use the triangle for tooltip. (such search got me here):
[](https://i.stack.imgur.com/BNxlB.png)
```
<div class="group relative mt-4 ml-4 inline-block">
  <button type="button" class="rounded-md border border-neutral-600 px-1">...</button>

  <!-- container for triangle and the menu ↓-->
  <div class="invisible absolute left-0 -mt-[2px] flex flex-col group-focus-within:visible group-active:visible">
     <div class="ml-2 -mb-[1px] inline-block overflow-hidden"> <!-- ← triangle container -->
        <!-- triangle ↓ -->
      <div class="h-3 w-3 origin-bottom-left rotate-45 transform border border-neutral-500 bg-neutral-100"></div>
    </div>

    <!-- menu ↓ -->
    <div class="flex min-w-max flex-col rounded-md border border-neutral-500 bg-neutral-100 px-2 py-1">
      <div class="cursor-pointer hover:underline">Do amazing stuff</div>
      <div class="cursor-pointer hover:underline">Go back</div>
    </div>
  </div>
</div>
```

[Demo on play.tailwindcss.com](https://play.tailwindcss.com/KO8cXX7sBN)
Based on:
- [answer](https://stackoverflow.com/a/67133568/1154773)- [https://github.com/tesar-tech/BlazorAndTailwind/blob/master/Articles/DropdownMenu/README.md](https://github.com/tesar-tech/BlazorAndTailwind/blob/master/Articles/DropdownMenu/README.md)


------------
    
    
## Answer \#3

 Vote: 2

Created at 2022-09-26 10:07:17

------------

By pseudo-element (small triangle):
```
<script src="https://cdn.tailwindcss.com"></script>
<div>small triangle:</div>
<div class="flex gap-x-1">
  <div class="before:content-['▴']"></div>
  <div class="before:content-['▾']"></div>
  <div class="before:content-['◂']"></div>
  <div class="before:content-['▸']"></div>
</div>
```



---


By pseudo-element (normal triangle):
```
<script src="https://cdn.tailwindcss.com"></script>

<div>normal triangle:</div>
<div class="flex gap-x-1">
  <div class="before:content-['▲']"></div>
  <div class="before:content-['▼']"></div>
  <div class="before:content-['◀']"></div>
  <div class="before:content-['▶']"></div>
</div>
```



---


By border:
```
<script src="https://cdn.tailwindcss.com"></script>
<div>triangle by border</div>
<div class="flex gap-x-3">
  <div class="w-0 h-0 border-8 border-solid border-transparent border-b-black"></div>
  <div class="w-0 h-0 border-8 border-solid border-transparent border-t-black"></div>
  <div class="w-0 h-0 border-8 border-solid border-transparent border-r-black"></div>
  <div class="w-0 h-0 border-8 border-solid border-transparent border-l-black"></div>
</div>
```



---


By border (simplify):
```
<script src="https://cdn.tailwindcss.com"></script>

<div>triangle by border (simplify)</div>
<style type="text/tailwindcss">
  @layer components { .triangle { @apply w-0 h-0 border-8 border-solid border-transparent } }
</style>
<div class="flex gap-x-3">
  <div class="triangle border-b-black"></div>
  <div class="triangle border-t-black"></div>
  <div class="triangle border-r-black"></div>
  <div class="triangle border-l-black"></div>
</div>
```




------------
    
    