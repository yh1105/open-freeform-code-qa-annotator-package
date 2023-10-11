
# Post \#61204259 [Link](https://stackoverflow.com/questions/61204259/)

## How can I resolve the "No Font Name" issue when importing fonts into R using extrafont?

**Vote**: 45 (139/702) **Views**: 17235 (331/702) 

**Internal ID** \#1-4-318

Created at 2020-04-14 09:12:38

Tags: `r` `extrafont`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `r`)

----------

**Notepad**


----------

I have a folder on my Windows desktop (`C:\Users\me\Desktop\Fonts`) which contains fonts that I would like to import into R using `extrafont`.

When I try to import the fonts using

```
library(extrafont)
font_import(paths = "C:/Users/me/Desktop/Fonts", prompt=FALSE)
```


I receive the error message

```
Scanning ttf files in C:/Users/me/Desktop/Fonts ...
Extracting .afm files from .ttf files...
C:\Users\me\Desktop\Fonts\arista-light.ttf : No FontName. Skipping.
C:\Users\me\Desktop\Fonts\facebook-letter-faces.ttf : No FontName. Skipping.
C:\Users\me\Desktop\Fonts\Guardian-EgypTT-Text-Regular.ttf : No FontName. Skipping.
C:\Users\me\Desktop\Fonts\pico-black.ttf : No FontName. Skipping.
C:\Users\me\Desktop\Fonts\product-sans.ttf : No FontName. Skipping.
Found FontName for 0 fonts.
Scanning afm files in C:/Users/me/Documents/R/R-3.6.3/library/extrafontdb/metrics
Warning messages:
1: In system2(ttf2pt1, c(args, shQuote(ttfiles[i]), shQuote(tmpfiles[i])),  :
  running command '"C:/Users/me/Documents/R/R-3.6.3/library/Rttf2pt1/exec/ttf2pt1.exe" -a -G fAe "C:\Users\me\Desktop\Fonts\arista-light.ttf" "C:\Users\me\AppData\Local\Temp\RtmpOgbdTh/fonts/arista-light"' had status 1
2: In system2(ttf2pt1, c(args, shQuote(ttfiles[i]), shQuote(tmpfiles[i])),  :
  running command '"C:/Users/me/Documents/R/R-3.6.3/library/Rttf2pt1/exec/ttf2pt1.exe" -a -G fAe "C:\Users\me\Desktop\Fonts\facebook-letter-faces.ttf" "C:\Users\me\AppData\Local\Temp\RtmpOgbdTh/fonts/facebook-letter-faces"' had status 1
3: In system2(ttf2pt1, c(args, shQuote(ttfiles[i]), shQuote(tmpfiles[i])),  :
  running command '"C:/Users/me/Documents/R/R-3.6.3/library/Rttf2pt1/exec/ttf2pt1.exe" -a -G fAe "C:\Users\me\Desktop\Fonts\Guardian-EgypTT-Text-Regular.ttf" "C:\Users\me\AppData\Local\Temp\RtmpOgbdTh/fonts/Guardian-EgypTT-Text-Regular"' had status 1
4: In system2(ttf2pt1, c(args, shQuote(ttfiles[i]), shQuote(tmpfiles[i])),  :
  running command '"C:/Users/me/Documents/R/R-3.6.3/library/Rttf2pt1/exec/ttf2pt1.exe" -a -G fAe "C:\Users\me\Desktop\Fonts\pico-black.ttf" "C:\Users\me\AppData\Local\Temp\RtmpOgbdTh/fonts/pico-black"' had status 1
5: In system2(ttf2pt1, c(args, shQuote(ttfiles[i]), shQuote(tmpfiles[i])),  :
  running command '"C:/Users/me/Documents/R/R-3.6.3/library/Rttf2pt1/exec/ttf2pt1.exe" -a -G fAe "C:\Users\me\Desktop\Fonts\product-sans.ttf" "C:\Users\me\AppData\Local\Temp\RtmpOgbdTh/fonts/product-sans"' had status 1
```


Based on this I have two questions:


1. How can I overcome the No FontName. Skipping. issue?
2. What are the warning messages trying to tell me and do I need to be concerned about this?



I would appreciate any help, many thanks in advance!


----------
        
## Answer \#0

**Accepted** Vote: 102

Created at 2021-08-03 21:13:45

------------

As it was mentioned by @Moritz Schwarz, the problem is [traced to Rttf2pt1](https://github.com/wch/extrafont/issues/32).
According to a solution proposed [here](https://github.com/wch/extrafont/issues/88#issuecomment-890426514), downgrading it to 1.3.8 will fix the problem:
```
library(extrafont)
library(remotes)
remotes::install_version("Rttf2pt1", version = "1.3.8")
extrafont::font_import()
```



------------
    
    
## Answer \#1

 Vote: 12

Created at 2021-03-08 15:37:11

------------

I had the exact same problem with one of my font files - the error always occurs in the `Rttf2pt1` executable.
I created an [issue on the GitHub page for the package](https://github.com/wch/Rttf2pt1/issues/10) - let's see if they can come up with a fix for this.
In the meantime, you can fix the issue though:
It seems to me that the origin of the error comes from a failure in the `.ttf` file. Navigate over to [FontForge](https://fontforge.org/) and download the open-source software. When installed, open your `.ttf` file and resave it as a `.ttf` using the  option in the  menu. You might get a message that warns you that there is something slightly wrong with the font - you can review it if you like, but I went ahead and clicked  anyway.
Then you use the new `.ttf` file for your `ttf_import()` and you are good to go!! :)


------------
    
    
## Answer \#2

 Vote: 4

Created at 2022-04-12 11:45:39

------------

(Writing in March 2022 from a Windows 10 machine...)
Instead of using `extrafont`, use the new package `showtext`.
([https://cran.rstudio.com/web/packages/showtext/vignettes/introduction.html](https://cran.rstudio.com/web/packages/showtext/vignettes/introduction.html)).


------------
    
    