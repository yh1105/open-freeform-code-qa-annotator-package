
# Post \#58884265 [Link](https://stackoverflow.com/questions/58884265/)

## Python - convert binary mask to polygon

**Vote**: 4 (532/702) **Views**: 15311 (354/702) 

**Internal ID** \#1-3-183

Created at 2019-11-15 20:30:41

Tags: `python` `numpy` `coordinates` `polygon` `mask`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

Given a simple binary mask (e.g. the boundary of a rectangle).

![binary mask](https://i.stack.imgur.com/w3kmz.png)

How can I use a polygon to get the x-y coordinates?

This is what I have tried so far:

```
coords = np.transpose(np.nonzero(mask))
```


However, this approach generates a filled object and not the desired boundary.

```
plt.plot(coords[:, 1], coords[:,0])
```


![unwanted output](https://i.stack.imgur.com/rFrwN.png)

Basically, I want a list of x-y coordinates of the white pixels to use this list to re-draw the rectangle (not filled).


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2019-11-15 20:45:02

------------

You can use `np.column_stack()` + `np.where()`. The idea is to determine the white pixels in the binary image then order then in corresponding `(x, y)` order

```
coords = np.column_stack(np.where(image > 0))
```



---



Another way is to find the coordinates of the bounding rectangle using OpenCV's `cv2.boundingRect()`. This will give you the width, height, and top-left `(x,y)` coordinates. Here's an example finding the coordinates then drawing the polygon onto a blank mask

![](https://i.stack.imgur.com/XBE69.png)

```
import cv2
import numpy as np

image = cv2.imread('1.png', 0)
x,y,w,h = cv2.boundingRect(image)
mask = np.ones(image.shape, dtype=np.uint8) * 255
mask = cv2.merge([mask,mask,mask])
cv2.rectangle(mask, (x, y), (x + w, y + h), (36,255,12), 2)

cv2.imshow('mask', mask)
cv2.waitKey()
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2020-11-02 16:47:42

------------

Using `cv2.findContours` works both for complex shapes and multiple objects. `Polygons` list contains `coords` lists each looking like this [x1, y1, x2, y2, x3, y3, ...].
```
contours, _ = cv2.findContours(mask, cv2.RETR_TREE, cv2.CHAIN_APPROX_NONE)
polygons = []

    for object in contours:
        coords = []
        
        for point in object:
            coords.append(int(point[0][0]))
            coords.append(int(point[0][1]))

        polygons.append(coords)
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-07-20 18:46:00

------------

You can treat the square like an object and acess its properties with `skimage.measure.regionprops` function.
I strongly advices to take a look at the documentation as its a very usefull function in many cases:
[https://scikit-image.org/docs/dev/api/skimage.measure.html#skimage.measure.regionprops](https://scikit-image.org/docs/dev/api/skimage.measure.html#skimage.measure.regionprops)
Also, there is `skimage.measure.regionprops_table` wich gives you a dictionarie to covert to a pandas dataframe.
Here's my solution:
```
from skimage.io import imread
from skimage.measure import regionprops_table
from pandas import DataFrame

import numpy as np
import matplotlib.pyplot as plt

rectangle = imread('rectangle_img.png')    
props_rect = DataFrame(regionprops_table(rectangle, properties=['coords']))

new_img = np.zeros((rectangle.shape[0], rectangle.shape[1]))

for point in props_rect['coords'][0]:

    new_img[point[0], point[1]] = 1


plt.imshow(new_img)
```



------------
    
    