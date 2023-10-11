
# Post \#68877941 [Link](https://stackoverflow.com/questions/68877941/)

## Is there a shorter way to write a border on only one side?

**Vote**: 12 (356/702) **Views**: 23806 (270/702) 

**Internal ID** \#0-2-153

Created at 2021-08-22 01:51:23

Tags: `html` `css` `tailwind-css`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

I got borders on all the edges by doing something like:
It is actually a mistake, but at the time of the question I believe I can draw a line only to the TOP with the following code. (Actually, lines are drawn on all edges.)
```
<div class="border border-blue-900 border-t-1">foo</div>
```

I wanted to display it only on one side, so I did some research.
I noticed that when I used `border` in `tailwindcss`,
the following was output in the output file.
> /**- 
1. Prevent padding and border from affecting element width.

- - - - - `content-box`- - [https://github.com/mozdevs/cssremedy/issues/4](https://github.com/mozdevs/cssremedy/issues/4)- - - 
1. Allow adding a border to an element by just adding a border-width.

- - - `none`- - - `border-width`- `solid`- `border`- `border-width``border`- - - [https://github.com/tailwindcss/tailwindcss/pull/116](https://github.com/tailwindcss/tailwindcss/pull/116)
The original of this output would have come from
[https://github.com/tailwindlabs/tailwindcss/blob/723e8d4377eb25b66a6224f767937fa02762eb52/src/plugins/css/preflight.css#L71](https://github.com/tailwindlabs/tailwindcss/blob/723e8d4377eb25b66a6224f767937fa02762eb52/src/plugins/css/preflight.css#L71)
I thought this was probably the cause.
I have succeeded in applying it only to the top edge by writing the following.
```
<div class="border border-blue-900 border-t-1 border-l-0 border-r-0 border-b-0">foo</div>
```

However, this is too long. Is there any way to make it shorter?
 I am also considering the following as alternatives.
[How do I take it as a parameter in JIT?](https://stackoverflow.com/questions/68877759/how-do-i-take-it-as-a-parameter-in-jit)
This way, I can also adjust the width (not the width in the border, it is for `display: block`).
What I want to do is just display it on any one side, so the above link is trying to display it on the bottom. (In other words, it doesn't matter if it's at the top, bottom, or anywhere else. It's just an example.)


----------
        
## Answer \#0

**Accepted** Vote: 15

Created at 2021-08-22 02:04:00

------------

You can apply border-width of 2px only to the top edge according to the [documentation](https://tailwindcss.com/docs/border-width) as following
```
<div class="border-t-2 border-blue-900">foo</div>
```

The error made was there is no utility class called `border-t-1` in tailwind-CSS. Also applying `border` utility class adds the the CSS styling of `border-width: 1px;` which adds a border-width to all sides.
Check out the solution at [tailwind playground](https://play.tailwindcss.com/uBB7kVb2zB)
 Check out [shingo.nakanishi's answer](https://stackoverflow.com/a/68878038/13833218) top apply `border-top-width` of `1px` if you are using [JIT](https://tailwindcss.com/docs/just-in-time-mode#enabling-jit-mode) mode


------------
    
    
## Answer \#1

 Vote: 7

Created at 2021-08-22 02:21:14

------------

Thanks [to this answer](https://stackoverflow.com/a/68877990/1979953).
I use JIT, I can make the following for 1px:
```
<div class="border-t-[1px] border-blue-900">foo</div>
```



------------
    
    
## Answer \#2

 Vote: 6

Created at 2022-05-19 17:50:42

------------

You can achieve this by using below class
```
border-solid border-0 border-t border-blue-900
```



------------
    
    
## Answer \#3

 Vote: 2

Created at 2022-12-14 12:51:09

------------

TL;DR; for no preflight: `border-[0] border-t border-solid border-black`

---


For those who do not have `preflight` from Tailwind (which is their css reset rules), the default browser `border-width` is `1px`, so when setting `border-solid` all border will be shown.
A `1px` top border can then be added with `border-[0] border-t border-solid border-black`, where `border-[0]` will first reset the `border-width`, and `border-t` is `border-width-top: 1px;`


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-05-25 09:54:55

------------

I don't know why but sometimes I need to add `border-b-0 border-r-0 border-l-0` in addition to the provided answers ( That worked for me, but not always ). If someone knows why, let me know.


------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-07-03 16:17:49

------------

If anyone else is pulling their hair out as to why something like `border-t border-t-sky-500` isn’t working, and you’re using Tailwind through PostCSS, then check your PostCSS invocation.
If you omit the `-o {outfile}` and use the stdout output feature, Tailwind prints debug output so you end up with something like this at the beginning of your final production CSS:
```
Source path:foo/bar/baz.css Setting up new context... Finding changed files:123.456ms Reading changed files:123.456ms Generate rules:123.456ms Build stylesheet:123.456ms Potential classes:123 Active contexts:123 JIT TOTAL:967.483ms *, ::before, ::after {
    -webkit-box-sizing: border-box;
    box-sizing: border-box;
    border-width: 0;
    border-style: solid;
    border-color:#e5e7eb
}
```

In other words, using `stdout` output of PostCSS with Tailwind ruins the first style rule of Tailwind, which includes setting `border-style: solid` on everything.
This is a bug, and it must be filed somewhere, but I couldn’t find it so far.


------------
    
    