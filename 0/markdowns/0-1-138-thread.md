
# Post \#50840168 [Link](https://stackoverflow.com/questions/50840168/)

## How to detect if the OS is in dark mode in browsers?

**Vote**: 270 (8/702) **Views**: 104230 (80/702) 

**Internal ID** \#0-1-138

Created at 2018-06-13 14:47:48

Tags: `css` `macos` `safari` `media-queries` `darkmode`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

Similar to "[How to detect if OS X is in dark mode?](https://stackoverflow.com/questions/25207077/how-to-detect-if-os-x-is-in-dark-mode)" only for browsers.

Has anyone found if there is a way to detect if the user's system is in the new OS X Dark Mode in Safari/Chrome/Firefox?

We would like to change our site's design to be dark-mode friendly based on the current operating mode.


----------
        
## Answer \#0

**Accepted** Vote: 347

Created at 2018-10-25 10:01:55

------------

The new standard is registered on [W3C in Media Queries Level 5](https://drafts.csswg.org/mediaqueries-5/#prefers-color-scheme).
[Safari Technology Preview Release 68](https://developer.apple.com/safari/technology-preview/release-notes/)
In case user preference is `light`:
```
/* Light mode */
@media (prefers-color-scheme: light) {
    body {
        background-color: white;
        color: black;
    }
}
```

In case user preference is `dark`:
```
/* Dark mode */
@media (prefers-color-scheme: dark) {
    body {
        background-color: black;
        color: white;
    }
}
```

There is also the option `no-preference` in case a user has no preference. But I recommend you just to use normal CSS in that case and cascade your CSS correctly.
>  (7 dec 2018):
In [Safari Technology Preview Release 71](https://developer.apple.com/safari/technology-preview/release-notes/) they announced a toggle switch in Safari to make testing easier. I also made a [test page](https://davy.dev/darkmode) to see the browser behaviour.
If you have  installed you can activate through:
> Develop > Experimental Features > Dark Mode CSS Support
Then if you open the [test page](https://davy.dev/darkmode) and open the element inspector you have a new icon to toggle Dark/Light mode.
[](https://i.stack.imgur.com/z2SYe.png)

---


>  (11 feb 2019):
Apple ships in the new [Safari 12.1](https://developer.apple.com/documentation/safari_release_notes/safari_12_1_release_notes) dark mode

---


>  (5 sep 2019):
Currently 25% of the world can use dark mode CSS. Source: [caniuse.com](https://caniuse.com/#search=prefers-color-scheme)Upcoming browsers:- - 

---


>  (5 nov 2019):
Currently 74% of the world can use dark mode CSS. Source: [caniuse.com](https://caniuse.com/#search=prefers-color-scheme)

---


>  (3 Feb 2020): Microsoft Edge 79 supports dark mode. (released on 15 Jan 2020)My suggestion would be: that you should consider implementing dark mode because most of the users can use it now (for night-time users of your site). All major browsers are supporting dark mode now, except: IE, 

---


>  (19 Nov 2020):
Currently 88% of the world can use dark mode CSS. Source: [caniuse.com](https://caniuse.com/#search=prefers-color-scheme)CSS-framework [Tailwind CSS v2.0](https://tailwindcss.com/) supports dark-mode. (released on 18 Nov 2020)

---


>  (2 Dec 2020):Google Chrome adds Dark Theme emulation to Dev Tools. Source: [developer.chrome.com](https://developer.chrome.com/blog/new-in-devtools-96/#auto-dark-mode)

---


>  (2 May 2022):Currently 90% of the world can use dark mode CSS. Source: [caniuse.com](https://caniuse.com/#search=prefers-color-scheme)


------------
    
    
## Answer \#1

 Vote: 192

Created at 2019-09-04 20:54:31

------------

If you want to detect it from JS, you can use this code:

```
if (window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches) {
    // dark mode
}
```


To watch for changes:

```
window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', e => {
    const newColorScheme = e.matches ? "dark" : "light";
});
```



------------
    
    
## Answer \#2

 Vote: 24

Created at 2018-08-11 12:06:20

------------

Spec has launched (see above), available as a media query. [Something](https://bugs.webkit.org/show_bug.cgi?id=186606) has already landed in Safari, see also [here](https://github.com/WebKit/webkit/commit/46198bd7636f0d1f85e36d830fd3108707d4c169). So in theory you can do this in Safari/Webkit:
```
@media (prefers-dark-interface) { color: white; background: black }
```

On MDN a CSS media feature `inverted-colors` is [mentioned](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/inverted-colors). Plug: I blogged about dark mode [here](https://medium.freecodecamp.org/how-to-get-dark-mode-working-with-css-740ad31e22e).


------------
    
    
## Answer \#3

 Vote: 12

Created at 2019-09-30 07:09:21

------------

According to Mozilla, here is the preferred method as of 2020
```
@media (prefers-color-scheme: dark) {
  body {
    background: #000;
  }
}
@media (prefers-color-scheme: light) {
  body {
    background: #fff;
  }
}
```

For Safari/Webkit you can use
```
@media (prefers-dark-interface) { background: #000; }
```



------------
    
    
## Answer \#4

 Vote: 5

Created at 2018-06-13 18:30:25

------------

I searched though Mozilla API, they don't seem to have any variables corresponding to the browser-windows color. Though i found a page that might help you: [How to Use Operating System Styles in CSS](https://www.sitepoint.com/css-system-styles/). Despite the article-header the colors are different for Chrome and Firefox.


------------
    
    
## Answer \#5

 Vote: 0

Created at 2023-01-07 22:39:56

------------

Using BootStrap 5.3.0-alpha1 and based on Mark Szabo's answer, I used this (I was using TypeScript):
```
window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', () => {
        switchColorScheme();
    });

    function switchColorScheme() {
        document.querySelector('html')!.dataset.bsTheme = window.matchMedia('(prefers-color-scheme: dark)').matches
            ? "dark"
            : "light";
    }

    switchColorScheme();
```

Before the page is loaded, it already detects the browser's current light or dark mode, and sets the bootstrap property on the  element accordingly.


------------
    
    