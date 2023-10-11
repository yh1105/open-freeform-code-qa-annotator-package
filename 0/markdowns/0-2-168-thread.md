
# Post \#50709625 [Link](https://stackoverflow.com/questions/50709625/)

## Link with target="_blank" and rel="noopener noreferrer" still vulnerable?

**Vote**: 271 (7/702) **Views**: 230094 (31/702) 

**Internal ID** \#0-2-168

Created at 2018-06-05 22:08:25

Tags: `html`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

I see people recommending that whenever one uses `target="_blank"` in a link to open it in a different window, they should put `rel="noopener noreferrer"`. I wonder how does this prevent me from using Developer Tools in Chrome, for example, and removing the rel attribute. Then clicking the link...

Is that an easy way to still keep the vulnerability?


----------
        
## Answer \#0

**Accepted** Vote: 363

Created at 2018-06-05 22:19:54

------------

You may be misunderstanding the vulnerability. You can read more about it here: [https://www.jitbit.com/alexblog/256-targetblank---the-most-underestimated-vulnerability-ever/](https://www.jitbit.com/alexblog/256-targetblank---the-most-underestimated-vulnerability-ever/)
Essentially, adding `rel="noopener noreferrer"` to links protects your site's users against having the site you've  potentially hijacking the browser (via rogue JS).
You're asking about removing that attribute via Developer Tools - that would only potentially expose  (the person tampering with the attribute) to the vulnerability.
 All current versions of major browsers now  automatically use the behavior of `rel="noopener"` for any `target="_blank"` link, nullifying this issue. See more at [chromestatus.com](https://chromestatus.com/feature/6140064063029248).


------------
    
    
## Answer \#1

 Vote: 127

Created at 2018-06-05 22:23:36

------------

Links with `target="_blank"` on them [are vulnerable](https://www.jitbit.com/alexblog/256-targetblank---the-most-underestimated-vulnerability-ever/) to having the referrer page being swapped out in the background while the user's attention is diverted by the newly-opened tab. This is known as [reverse tabnapping](https://www.owasp.org/index.php/Reverse_Tabnabbing):

[](https://i.stack.imgur.com/FFW5y.png)

The referring page is stored in [window.opener](https://developer.mozilla.org/en-US/docs/Web/API/Window/opener), and a malicious site could modify this through:

```
if (window.opener) {
   window.opener.location = "https://phish.example.com";
}
```


Adding `rel="noopener noreferrer"` fixes this vulnerability in all major browsers.

Note that you could theoretically remove the `rel`  through manipulation... but why would you want to? All you are doing is deliberately making yourself vulnerable to the attack.

Other users who visit the same website (and don't modify their own client-side code) would still be safe, as the server would still serve up the `rel="noopener noreferrer"`. Your removal of it only applies to you.


------------
    
    
## Answer \#2

 Vote: 41

Created at 2021-06-04 15:18:38

------------


## Browsers fix


All major browsers have fixed this issue by updating the default behaviour to be secure without having to specify `rel` property. Read more in [About rel=noopener](https://mathiasbynens.github.io/rel-noopener/).
- [Chrome fixed it](https://bugs.chromium.org/p/chromium/issues/detail?id=898942)- [Safari fixed it](https://bugs.webkit.org/show_bug.cgi?id=155166)- [Firefox fixed it](https://bugzilla.mozilla.org/show_bug.cgi?id=1222516)- 


------------
    
    
## Answer \#3

 Vote: 30

Created at 2020-12-07 08:09:43

------------

Regarding the [Lighthouse Best Practices audits](https://web.dev/lighthouse-best-practices/) report:
> Add `rel="noopener"` or `rel="noreferrer"` to any external links to
improve performance and prevent security vulnerabilities.
In general, when you use `target="_blank"`, always add `rel="noopener"` or `rel="noreferrer"`:
For example:
```
<a href="https://www.kaiostech.com/store/" target="_blank" rel="noreferrer">
  KaiStore
</a>
```

- [rel="noopener"](https://html.spec.whatwg.org/multipage/links.html#link-type-noopener)`window.opener`- [rel="noreferrer"](https://html.spec.whatwg.org/multipage/links.html#link-type-noreferrer)`Referer`
See the [official document](https://web.dev/external-anchors-use-rel-noopener/) for more information.


------------
    
    
## Answer \#4

 Vote: 6

Created at 2021-04-25 11:46:01

------------

There's no answer pointing out a case when you would actually  just ignoring any "security risk" warnings: for instance, when you are linking , which is controlled by you/your org and which presumably has no described security vulnerabilities.
For instance, in my case, in I want to see in Google Analytics how many of my visitors read docs by clicking links inside the web app (which uses a separate domain from where docs are) and how many come from search engines, and hence I want to utilize the referrer header for distinguishing these user groups.
Here's how you can do it when linking websites like your own docs that are safe for sure:
```
{/* Safe link to the own website *//* eslint-disable-next-line react/jsx-no-target-blank */}
<a href="https://my-own-website.com" target="_blank">
  Learn more
</a>
```

In React, I made a wrapper common component to avoid putting `eslint-disable` everywhere in the code (an opposite to `UnsafeExternalLink` component):
```
import React, { ReactNode } from 'react';

/* eslint-disable react/jsx-no-target-blank */

/**
 * Safe link to own external websites only.
 */
const SafeExternalLink = ({
  href,
  children,
}: {
  href: string;
  children: ReactNode;
}) => (
  <a href={href} target="_blank">
    {children}
  </a>
);

export default SafeExternalLink;
```



------------
    
    
## Answer \#5

 Vote: 4

Created at 2019-10-16 11:42:52

------------

The anchor tag `rel=”noopener”` or `rel=”noreferrer”` attributes improve the website security, but some people want to ignore them because they think they will affect their website search engine optimization, but that is just a myth.
It protects the confidentiality of your website audience and prevents external website by spreading malicious code.


------------
    
    
## Answer \#6

 Vote: 0

Created at 2020-11-13 05:50:56

------------

If developer console is showing warning regarding `noopener noreferrer`, make sure you add both `noopener` and `noreferrer` in rel. link should be something like below:
```
<a href="www.google.com" target="_blank" rel="noopener noreferrer" />
```



------------
    
    