
# Post \#63390194 [Link](https://stackoverflow.com/questions/63390194/)

## Package ‘XXX’ was installed before R 4.0.0: please re-install it

**Vote**: 21 (256/702) **Views**: 27524 (244/702) 

**Internal ID** \#1-4-319

Created at 2020-08-13 07:30:21

Tags: `r`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `r`)

----------

**Notepad**


----------

I am using R 4.0.2.
I manually installed 2 packages from `cfcdae` and `Stats5303lib` from [here](http://users.stat.umn.edu/%7Egary/classes/5303/software.html). I followed the instruction [here](https://cran.r-project.org/bin/windows/Rtools/) to download Rtools and use it to install packages above.
Problem is
```
writeLines('PATH="${RTOOLS40_HOME}\\usr\\bin;${PATH}"', con = "~/.Renviron") # runs fine
Sys.which("make") #works fine
                               make 
"C:\\rtools40\\usr\\bin\\make.exe" 
install.packages("Stat5303libs_0.7-5.zip",repos=NULL,type="source") # these all run fine
install.packages("cfcdae_0.8-4.zip",repos=NULL,type="source")  # these all run fine
```

However, when I ran into problem below when I tried to run the libraries.
```
Error: package or namespace load failed for ‘cfcdae’:
 package ‘cfcdae’ was installed before R 4.0.0: please re-install it
```

I tried below but still in vain.
```
update.packages(ask=FALSE, checkBuilt=TRUE)
```

Why is this happening? Is it because the package is too old?

As requested, i have changed my `.libPaths()` as below and updated the `SessionInfo()` as well.
```
> .libPaths()
[1] "C:/Users/UserME/Documents/R/win-library/4.0"
[2] "C:/Program Files/R/R-4.0.2/library"   

R version 4.0.2 (2020-06-22)
Platform: x86_64-w64-mingw32/x64 (64-bit)
Running under: Windows 10 x64 (build 18363)

Matrix products: default

locale:
[1] LC_COLLATE=English_Hong Kong SAR.1252  LC_CTYPE=English_Hong Kong SAR.1252   
[3] LC_MONETARY=English_Hong Kong SAR.1252 LC_NUMERIC=C                          
[5] LC_TIME=English_Hong Kong SAR.1252    

attached base packages:
[1] stats     graphics  grDevices utils     datasets  methods   base     

loaded via a namespace (and not attached):
 [1] Rcpp_1.0.4.6        rstudioapi_0.11     magrittr_1.5       
 [4] splines_4.0.2       MASS_7.3-51.6       tidyselect_1.1.0   
 [7] munsell_0.5.0       statmod_1.4.34      lattice_0.20-41    
[10] colorspace_1.4-1    R6_2.4.1            rlang_0.4.6        
[13] minqa_1.2.4         dplyr_1.0.0         tools_4.0.2        
[16] grid_4.0.2          nlme_3.1-148        gtable_0.3.0       
[19] ellipsis_0.3.1      lme4_1.1-23         tibble_3.0.1       
[22] lifecycle_0.2.0     numDeriv_2016.8-1.1 crayon_1.3.4       
[25] Matrix_1.2-18       nloptr_1.2.2.2      purrr_0.3.4        
[28] ggplot2_3.3.2       vctrs_0.3.1         glue_1.4.1         
[31] compiler_4.0.2      pillar_1.4.6        generics_0.0.2     
[34] scales_1.1.1        boot_1.3-25         lmerTest_3.1-2     
[37] pkgconfig_2.0.3
```



----------
        
## Answer \#0

**Accepted** Vote: 22

Created at 2020-08-29 14:03:28

------------

All packages need to be reinstalled under the new [version](https://www.r-bloggers.com/get-all-your-packages-back-on-r-4-0-0/) (4.0). I had to first remove and then reinstall all the packages.
The following worked for me:
```
# check your package library path 
.libPaths()

# grab old packages names
old_packages <- installed.packages(lib.loc = "/Library/Frameworks/R.framework/Versions/3.6/Resources/library")
old_packages <- as.data.frame(old_packages)
list.of.packages <- unlist(old_packages$Package)

# remove old packages 
remove.packages( installed.packages( priority = "NA" )[,1] )

# reinstall all packages 
new.packages <- list.of.packages[!(list.of.packages %in% installed.packages()[,"Package"])]
if(length(new.packages)) install.packages(new.packages)
lapply(list.of.packages,function(x){library(x,character.only=TRUE)})
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2020-08-13 08:45:01

------------

This issue is likely to happen when R is reading from an old directory, e.g. pre R 4.0.x .
Here is a few possible ways to fix this:
- `.libPaths()`- `update.packages(ask=FALSE, checkBuilt=TRUE)`- 


------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-08-13 13:09:46

------------

A zip file is a pre-built  package for use on Windows, not a source package. Installing it with `install.packages(*, type="source")` won't make a difference. You'll need to contact the person/people who wrote these packages to build them again for R 4.0, or provide you with the real source -- this will usually be a `.tar.gz` file.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-05-28 11:41:01

------------

If you're having trouble with this issue while using `renv`, you may need to look at your [renv cache](https://cran.r-project.org/web/packages/renv/vignettes/renv.html#cache). I had to delete mine to force my projects to use newly-installed versions of packages instead of the old ones.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-02-08 12:23:04

------------

In a similar situation, I renamed 'site-library' to 'old-site-library', made a new empty 'site-library' folder and proceeded from there successfully.


------------
    
    
## Answer \#5

 Vote: 0

Created at 2023-01-12 16:31:50

------------

In case anyone runs into this error message when running `revdepcheck::revdep_check()`, what worked for me was deleting the `/revdep/` folder in my R package's directory.


------------
    
    
## Answer \#6

 Vote: 0

Created at 2023-01-25 03:35:50

------------

This code worked for me
```
tmp = as.data.frame(installed.packages()) 
  max_version = max(as.numeric(substr(tmp$Built, 1,1)))
  tmp = tmp[as.numeric(substr(tmp$Built, 1,1)) < max_version,]
  lapply(tmp$Package, remove.packages)
  lapply(tmp$Package, function(x) install.packages(x, dependencies = TRUE))
```



------------
    
    