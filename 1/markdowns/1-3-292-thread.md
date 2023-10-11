
# Post \#67353650 [Link](https://stackoverflow.com/questions/67353650/)

## Extract part of a image using openCV

**Vote**: 6 (473/702) **Views**: 8141 (462/702) 

**Internal ID** \#1-3-292

Created at 2021-05-02 06:41:20

Tags: `python` `opencv` `image-processing`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I'm new to image processing. I wonder if there is any way to extract the star logo from the image?
[](https://i.stack.imgur.com/x8Nbb.png)


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2021-05-02 07:36:41

------------

Give this a go
```
import cv2 as cv
import numpy as np

img = cv.imread('airplane.png')
cv.imshow('Original', img)

blank = np.zeros(img.shape[:2], dtype='uint8')

center_coordinates = (144, 233)

radius = 10

mask = cv.circle(blank, center_coordinates, radius, 255, -1)

masked = cv.bitwise_and(img, img, mask=mask)
cv.imshow('Output', masked)

cv.waitKey(0)
```




------------
    
    
## Answer \#1

 Vote: 4

Created at 2021-05-02 06:45:51

------------

If you want to crop the logo out using OpenCV's cropping tool
```
import cv2
img = cv2.imread("fight plane.png")
crop_img = img[y:y+h, x:x+w]
cv2.imshow("cropped", crop_img)
cv2.waitKey(0)
```

Take coordinates. It is extremely easy. There is another question by the topic over here - [How to crop an image in OpenCV using Python](https://stackoverflow.com/questions/15589517/how-to-crop-an-image-in-opencv-using-python)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-05-02 07:15:39

------------

Use cv2.selectROI  function
```
cv2.namedWindow('ROI') 
# define area by mouse
r=cv2.selectROI('ROI', img,False,False)
imROI = img[int(r[1]):int(r[1]+r[3]), int(r[0]):int(r[0]+r[2])]
cv2.destroyWindow('ROI')
cv2.imshow("ROI", imROI)
cv2.waitKey(0)
cv2.destroyAllWindows()
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-05-02 06:45:08

------------

```
import cv2
import argparse

ref_point = []
crop = False

def shape_selection(event, x, y, flags, param):
    global ref_point, crop

    if event == cv2.EVENT_LBUTTONDOWN:
        ref_point = [(x, y)]
    elif event == cv2.EVENT_LBUTTONUP:
        ref_point.append((x, y))
        cv2.rectangle(image, ref_point[0], ref_point[1], (0, 255, 0), 2)
        cv2.imshow("image", image)


ap = argparse.ArgumentParser()
ap.add_argument("-i", "--image", required = True, help ="Path to the image")
args = vars(ap.parse_args())
image = cv2.imread(args["image"])
clone = image.copy()
cv2.namedWindow("image")
cv2.setMouseCallback("image", shape_selection)
while True:
    cv2.imshow("image", image)
    key = cv2.waitKey(1) & 0xFF
    if key == ord("r"):
        image = clone.copy()
    elif key == ord("c"):
        break
if len(ref_point) == 2:
    crop_img = clone[ref_point[0][1]:ref_point[1][1], ref_point[0][0]:                                                        ref_point[1][0]]
    cv2.imshow("crop_img", crop_img)
    cv2.waitKey(0)
cv2.destroyAllWindows()
```

try this it will work


------------
    
    