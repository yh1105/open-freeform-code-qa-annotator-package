
# Post \#51785616 [Link](https://stackoverflow.com/questions/51785616/)

## import error when using react-icons

**Vote**: 25 (228/702) **Views**: 84246 (101/702) 

**Internal ID** \#0-0-86

Created at 2018-08-10 11:33:16

Tags: `reactjs`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have tried installing react icons, in my application I ran the npm command:

```
sudo npm install react-icons --save
```


I didn't get any errors, apart from some optional dependencies, that where skipped

```
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.4 
(node_modules/fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for 
fsevents@1.2.4: wanted {"os":"darwin","arch":"any"} (current: 
{"os":"linux","arch":"x64"})
```


Whenever I try to import some of the icons, I get an error

```
./src/components/SkiDaysCount.js
Module not found: Can't resolve 'react-icons/lib/md' in ' 
'/home/kristoffer/ReactApps/navbar/src/components'
```


here are my imports:

```
import {Terrain} from 'react-icons/lib/md'
import {SnowFlake} from 'react-icons/lib/ti'
import {Calender} from 'react-icons/lib/fa'
```


Why am I getting this error?

EDIT:

i have also tried using the old syntax for importing, with the same issue as such:

```
import Calender from 'react-icons/lib/fa/calender'
```



----------
        
## Answer \#0

**Accepted** Vote: 27

Created at 2018-08-10 11:42:27

------------

When you use the v3 way of importing the icons, you should not have `lib` be a part of the import path.

The icons also have the icon library name as prefix for the export.

```
import { FaCalendar } from 'react-icons/fa'
```



------------
    
    
## Answer \#1

 Vote: 20

Created at 2018-08-26 22:56:56

------------

After looking through the icon directories react-icons/[fa,ti,md] and looking at the index.dt.ts file for the new names of the icons I came up with your answer. 

```
import { MdTerrain } from "react-icons/md";
import { TiWeatherSnow } from "react-icons/ti";
import { FaCalendarAlt } from "react-icons/fa";
```


To use the icons in your component use the tags: 

```
<FaCalendarAlt />
<TiWeatherSnow />
<MdTerrain />
```


For an explanation look at the Migration from version 2-> 3 on the react-icon page.
[https://www.npmjs.com/package/react-icons](https://www.npmjs.com/package/react-icons)


------------
    
    
## Answer \#2

 Vote: 10

Created at 2018-10-08 14:24:06

------------

Not all the `fa` icons are free. I was getting this error when I was trying to import the paid icon of pencil as below.

```
import { FaPencil } from 'react-icons/fa'
```


If you see [here](https://fontawesome.com/icons?d=gallery&q=Pencil) for all the fa icons, you can see that the `pencil` needs the [pro license](https://fontawesome.com/icons/pencil?style=regular). 

As I was not having a pro license, I was getting the error. But we can always use the free [versions of pencil](https://fontawesome.com/icons?d=gallery&q=Pencil&m=free), and we just need to import it as below.

```
import { FaPencilAlt } from 'react-icons/fa'
<button><FaPencilAlt /></button>
```


You should also check the implementation of your version from [here](https://www.npmjs.com/package/react-icons?activeTab=versions).


------------
    
    
## Answer \#3

 Vote: 3

Created at 2018-08-18 15:55:55

------------

If you have installed the 3.0.5 version of react-icons, Do try :

For Pencil(Edit):

```
import {FaPencilAlt} from 'react-icons/fa'
```


For Trash Can (Delete):

```
import {FaTrash} from 'react-icons/fa'
```



------------
    
    
## Answer \#4

 Vote: 2

Created at 2020-02-24 05:20:19

------------

```
import {FaHiking, FaRightAlign,...} from 'react-icons/fa'
```


from the v3 of react icons library '/libname' is mandatory.

libname as 'fa'-> fontawesome, 'fi'->feathericons. for more info visit

[https://react-icons.netlify.com/#/](https://react-icons.netlify.com/#/)


------------
    
    
## Answer \#5

 Vote: 2

Created at 2020-08-25 11:21:28

------------

maybe you have to update the "react-icons" package.
command:
```
npm update react-icons
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2019-10-03 11:27:04

------------

This can also be caused by typo error.
say

```
import {Haiking) from 'react-icons/fa'
```


instead of 

```
import {Hiking) from 'react-icons/fa'
```



------------
    
    
## Answer \#7

 Vote: 0

Created at 2021-09-16 09:19:47

------------

perhaps you are in yarn mode and you need to use `yarn add` instead of `npm install` to install a package.


------------
    
    
## Answer \#8

 Vote: 0

Created at 2022-11-28 09:04:35

------------

import {  } from '';
import {  } from "";


------------
    
    
## Answer \#9

 Vote: 0

Created at 2022-12-07 16:31:07

------------

Mine was such an easy fix after all. All I needed was the full import name of the icon, even if just one icon always put curly {} and that was it fixed!
How many times do I delete node modules, install @latest and then realise ist the curlies! :-)
```
import {GoMail} from "react-icons/go";
```



------------
    
    