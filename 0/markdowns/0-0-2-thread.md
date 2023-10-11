
# Post \#54550006 [Link](https://stackoverflow.com/questions/54550006/)

## unsafe:data:image/jpeg;base64, net::ERR_UNKNOWN_URL_SCHEME in Angular 7

**Vote**: 17 (290/702) **Views**: 46770 (173/702) 

**Internal ID** \#0-0-2

Created at 2019-02-06 09:10:40

Tags: `angular`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am getting image as base64 blob from a service and I am binding into
view. But I am facing an issue. How can I sanitize the url into a
trusted url. I have tried with sanitizer but no luck..
Please help me out..

```
<img src="data:image/jpeg;base64,{{inspectionDetails.reportImage}}" width="100%" height="100%" alt="Image" />
```


```
this.ImgUrl = this.inspectionDetails.reportImage;
this.base64Image = this._sanitizer.bypassSecurityTrustResourceUrl(this.ImgUrl);
```



----------
        
## Answer \#0

**Accepted** Vote: 16

Created at 2019-02-06 09:15:29

------------

You need to make this change
```
this.ImgUrl = 'data:image/png;base64,' + this.inspectionDetails.reportImage;
```

or
```
this.ImgUrl = `data:image/png;base64,${{this.inspectionDetails.reportImage}}`;
```

then Your HTML will be
```
<img [src]="ImgUrl " width="100%" height="100%" alt="Image" />
```

this should work
EDIT:
```
public ImgUrl = ' ';
```



------------
    
    
## Answer \#1

 Vote: 40

Created at 2020-01-20 23:02:54

------------

Use `_sanitizer.bypassSecurityTrustUrl` to tell angular src value is safe
You can change your code like below.
Import DomSanitizer
```
import { DomSanitizer } from '@angular/platform-browser';
```

Inject this dependency into the constructor
```
constructor(private domSanitizer: DomSanitizer) { }


myReader.onloadend = (e) => {
     this.base64Image = this.domSanitizer.bypassSecurityTrustUrl(myReader.result);
     console.log(this.base64Image);
   }
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-10-01 09:14:35

------------

I have also faced similar kind of issue for video and here's a way how I resolved it. You can set SRC in HTML like mentioned below to resolve unsafe data issue for image/video.



------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-12-05 15:14:30

------------

I had similar issue manage to resolve with using [src] instead of src
code that didnt work
```
<img src=`{{recipe.imagePath}}` class="img-responsive" style="max-height: 50px;">
```

fixed with
```
<img [src]="recipe.imagePath" class="img-responsive" style="max-height: 50px;">
```

where imagePath was just simple URL to some image from internet


------------
    
    