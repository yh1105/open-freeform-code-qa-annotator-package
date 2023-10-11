
# Post \#58142214 [Link](https://stackoverflow.com/questions/58142214/)

## To invert colours from black to white in opencv python

**Vote**: 5 (499/702) **Views**: 17184 (332/702) 

**Internal ID** \#1-3-248

Created at 2019-09-27 23:28:17

Tags: `python` `image` `opencv` `image-processing` `image-manipulation`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a condition where I want to detect white line incase of black background and black line incase of white background. I used bitwise_not operation something like this:

```
cv2.bitwise_not(mask_black)
```


It is working perfectly until and unless i give a condition like this:

```
if mask_black == cv2.bitwise_not(mask_black):
```


I get an error

> ValueError: The truth value of an array with more than one element is ambiguous. Use a.any() or a.all()

I am having doubts about using conditions where if black background occurs white line is to be detected and if white background occurs black line is to be detected

```
mask_black = cv2.inRange(hsv, low_black, high_black)
mask_not=cv2.bitwise_not(mask_black)

if mask_black==cv2.bitwise_and(mask_black, mask_not):
    body 
else:
    body
```


This is returning the above error


----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2019-09-28 01:58:04

------------

The idea is to check the background for white pixels with `cv2.countNonZero()`. We set a threshold, say 50%. If more than 50% of the background is white pixels then it means we are looking for the black line. Similarly, if  the majority of the background is black then we are looking for the white line.

```
import cv2

image = cv2.imread('1.png', 0)
w, h = image.shape[:2]

if cv2.countNonZero(image) > ((w*h)//2):
    # Detect black line
    ...
else:
    # Detect white line
    ...
```



---



To invert an image, you can do this

```
invert = cv2.bitwise_not(image) # OR
#invert = 255 - image
```


![](https://i.stack.imgur.com/TPVnd.png)
![](https://i.stack.imgur.com/tUMip.png)


------------
    
    
## Answer \#1

 Vote: 7

Created at 2021-02-08 11:59:31

------------

```
invert = cv2.bitwise_not(image)
```

This is working perfectly.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2019-09-27 23:35:40

------------

Use `cv2.bitwise_and(mask_black, mask_not)` where `mask_not` is `cv2.bitwise_not(mask_black)`


------------
    
    