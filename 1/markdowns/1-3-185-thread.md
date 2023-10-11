
# Post \#60145306 [Link](https://stackoverflow.com/questions/60145306/)

## Remove background text and noise from an image using image processing with OpenCV

**Vote**: 11 (370/702) **Views**: 17358 (329/702) 

**Internal ID** \#1-3-185

Created at 2020-02-10 06:45:51

Tags: `python` `image` `opencv` `image-processing` `computer-vision`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have these images

[](https://i.stack.imgur.com/VOfgD.png)

[](https://i.stack.imgur.com/TdRKg.png)

For which I want to remove the text in the background. Only the `captcha characters` should remain(i.e K6PwKA, YabVzu). The task is to identify these characters later using tesseract.

This is what I have tried, but it isn't giving much good accuracy.

```
import cv2
import pytesseract

pytesseract.pytesseract.tesseract_cmd = r"C:\Users\HPO2KOR\AppData\Local\Tesseract-OCR\tesseract.exe"
img = cv2.imread("untitled.png")
gray_image = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
gray_filtered = cv2.inRange(gray_image, 0, 75)
cv2.imwrite("cleaned.png", gray_filtered)
```


How can I improve the same?

 
I tried all the suggestion that I was getting for this question and none of them worked for me.

 
According to Elias, I tried finding the color of the captcha text using photoshop by converting it to grayscale which came out to be somewhere in between [100, 105]. I then threshold the image based on this range. But the result which I got did not give satisfactory result from tesseract.

```
gray_filtered = cv2.inRange(gray_image, 100, 105)
cv2.imwrite("cleaned.png", gray_filtered)
gray_inv = ~gray_filtered
cv2.imwrite("cleaned.png", gray_inv)
data = pytesseract.image_to_string(gray_inv, lang='eng')
```


Output :

```
'KEP wKA'
```


Result : 

[](https://i.stack.imgur.com/AqYeO.png)



```
def get_text(img_name):
    lower = (100, 100, 100)
    upper = (104, 104, 104) 
    img = cv2.imread(img_name)
    img_rgb_inrange = cv2.inRange(img, lower, upper)
    neg_rgb_image = ~img_rgb_inrange
    cv2.imwrite('neg_img_rgb_inrange.png', neg_rgb_image)
    data = pytesseract.image_to_string(neg_rgb_image, lang='eng')
    return data
```


gives : 

[](https://i.stack.imgur.com/rSrSz.png)

and the text as

```
GXuMuUZ
```


Is there any way to soften it a little


----------
        
## Answer \#0

**Accepted** Vote: 18

Created at 2020-02-25 23:17:16

------------

Here are two potential approaches and a method to correct distorted text:
 Morphological operations + contour filtering

1. Obtain binary image. Load image, grayscale, then Otsu's threshold.
2. Remove text contours. Create a rectangular kernel with cv2.getStructuringElement() and then perform morphological operations to remove noise.
3. Filter and remove small noise. Find contours and filter using contour area to remove small particles. We effectively remove the noise by filling in the contour with cv2.drawContours()
4. Perform OCR. We invert the image then apply a slight Gaussian blur. We then OCR using Pytesseract with the --psm 6 configuration option to treat the image as a single block of text. Look at Tesseract improve quality for other methods to improve detection and Pytesseract configuration options for additional settings.



---


Input image `->` Binary `->` Morph opening
[](https://i.stack.imgur.com/XVnBy.png)
[](https://i.stack.imgur.com/5PZLj.png)
[](https://i.stack.imgur.com/qizcY.png)
Contour area filtering `->` Invert `->` Apply blur to get result
[](https://i.stack.imgur.com/c26gJ.png)
[](https://i.stack.imgur.com/DSD6l.png)
[](https://i.stack.imgur.com/ruARh.png)
Result from OCR
```
YabVzu
```

Code
```
import cv2
import pytesseract
import numpy as np

pytesseract.pytesseract.tesseract_cmd = r"C:\Program Files\Tesseract-OCR\tesseract.exe"

# Load image, grayscale, Otsu's threshold
image = cv2.imread('2.png')
gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
thresh = cv2.threshold(gray, 0, 255, cv2.THRESH_BINARY_INV + cv2.THRESH_OTSU)[1]

# Morph open to remove noise
kernel = cv2.getStructuringElement(cv2.MORPH_RECT, (2,2))
opening = cv2.morphologyEx(thresh, cv2.MORPH_OPEN, kernel, iterations=1)

# Find contours and remove small noise
cnts = cv2.findContours(opening, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
cnts = cnts[0] if len(cnts) == 2 else cnts[1]
for c in cnts:
    area = cv2.contourArea(c)
    if area < 50:
        cv2.drawContours(opening, [c], -1, 0, -1)

# Invert and apply slight Gaussian blur
result = 255 - opening
result = cv2.GaussianBlur(result, (3,3), 0)

# Perform OCR
data = pytesseract.image_to_string(result, lang='eng', config='--psm 6')
print(data)

cv2.imshow('thresh', thresh)
cv2.imshow('opening', opening)
cv2.imshow('result', result)
cv2.waitKey()
```

 Color segmentation
With the observation that the desired text to extract has a distinguishable contrast from the noise in the image, we can use color thresholding to isolate the text. The idea is to convert to HSV format then color threshold to obtain a mask using a lower/upper color range. From were we use the same process to OCR with Pytesseract.

---


Input image `->` Mask `->` Result
[](https://i.stack.imgur.com/Drs7m.png)
[](https://i.stack.imgur.com/99x8o.png)
[](https://i.stack.imgur.com/m60JO.png)
Code
```
import cv2
import pytesseract
import numpy as np

pytesseract.pytesseract.tesseract_cmd = r"C:\Program Files\Tesseract-OCR\tesseract.exe"

# Load image, convert to HSV, color threshold to get mask
image = cv2.imread('2.png')
hsv = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
lower = np.array([0, 0, 0])
upper = np.array([100, 175, 110])
mask = cv2.inRange(hsv, lower, upper)

# Invert image and OCR
invert = 255 - mask
data = pytesseract.image_to_string(invert, lang='eng', config='--psm 6')
print(data)

cv2.imshow('mask', mask)
cv2.imshow('invert', invert)
cv2.waitKey()
```


OCR works best when the image is horizontal. To ensure that the text is in an ideal format for OCR, we can perform a perspective transform. After removing all the noise to isolate the text, we can perform a morph close to combine individual text contours into a single contour. From here we can find the rotated bounding box using [cv2.minAreaRect](https://docs.opencv.org/2.4/modules/imgproc/doc/structural_analysis_and_shape_descriptors.html?highlight=minarearect#minarearect) and then perform a [four point perspective transform](https://www.pyimagesearch.com/2014/08/25/4-point-opencv-getperspective-transform-example/) using [imutils.perspective.four_point_transform](https://github.com/jrosebr1/imutils/blob/master/imutils/perspective.py#L36). Continuing from the cleaned mask, here's the results:
Mask `->` Morph close `->` Detected rotated bounding box `->` Result
[](https://i.stack.imgur.com/wjJKq.png)
[](https://i.stack.imgur.com/9fRqn.png)
[](https://i.stack.imgur.com/QuMk6.png)
[](https://i.stack.imgur.com/S0YRx.png)
Output with the other image
[](https://i.stack.imgur.com/PKdSK.png)
[](https://i.stack.imgur.com/77bJd.png)
[](https://i.stack.imgur.com/9uqWn.png)
[](https://i.stack.imgur.com/n5OvV.png)
Updated code to include perspective transform
```
import cv2
import pytesseract
import numpy as np
from imutils.perspective import four_point_transform

pytesseract.pytesseract.tesseract_cmd = r"C:\Program Files\Tesseract-OCR\tesseract.exe"

# Load image, convert to HSV, color threshold to get mask
image = cv2.imread('1.png')
hsv = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
lower = np.array([0, 0, 0])
upper = np.array([100, 175, 110])
mask = cv2.inRange(hsv, lower, upper)

# Morph close to connect individual text into a single contour
kernel = cv2.getStructuringElement(cv2.MORPH_RECT, (5,5))
close = cv2.morphologyEx(mask, cv2.MORPH_CLOSE, kernel, iterations=3)

# Find rotated bounding box then perspective transform
cnts = cv2.findContours(close, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
cnts = cnts[0] if len(cnts) == 2 else cnts[1]
rect = cv2.minAreaRect(cnts[0])
box = cv2.boxPoints(rect)
box = np.int0(box)
cv2.drawContours(image,[box],0,(36,255,12),2)
warped = four_point_transform(255 - mask, box.reshape(4, 2))

# OCR
data = pytesseract.image_to_string(warped, lang='eng', config='--psm 6')
print(data)

cv2.imshow('mask', mask)
cv2.imshow('close', close)
cv2.imshow('warped', warped)
cv2.imshow('image', image)
cv2.waitKey()
```

 The color threshold range was determined using this HSV threshold script
```
import cv2
import numpy as np

def nothing(x):
    pass

# Load image
image = cv2.imread('2.png')

# Create a window
cv2.namedWindow('image')

# Create trackbars for color change
# Hue is from 0-179 for Opencv
cv2.createTrackbar('HMin', 'image', 0, 179, nothing)
cv2.createTrackbar('SMin', 'image', 0, 255, nothing)
cv2.createTrackbar('VMin', 'image', 0, 255, nothing)
cv2.createTrackbar('HMax', 'image', 0, 179, nothing)
cv2.createTrackbar('SMax', 'image', 0, 255, nothing)
cv2.createTrackbar('VMax', 'image', 0, 255, nothing)

# Set default value for Max HSV trackbars
cv2.setTrackbarPos('HMax', 'image', 179)
cv2.setTrackbarPos('SMax', 'image', 255)
cv2.setTrackbarPos('VMax', 'image', 255)

# Initialize HSV min/max values
hMin = sMin = vMin = hMax = sMax = vMax = 0
phMin = psMin = pvMin = phMax = psMax = pvMax = 0

while(1):
    # Get current positions of all trackbars
    hMin = cv2.getTrackbarPos('HMin', 'image')
    sMin = cv2.getTrackbarPos('SMin', 'image')
    vMin = cv2.getTrackbarPos('VMin', 'image')
    hMax = cv2.getTrackbarPos('HMax', 'image')
    sMax = cv2.getTrackbarPos('SMax', 'image')
    vMax = cv2.getTrackbarPos('VMax', 'image')

    # Set minimum and maximum HSV values to display
    lower = np.array([hMin, sMin, vMin])
    upper = np.array([hMax, sMax, vMax])

    # Convert to HSV format and color threshold
    hsv = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
    mask = cv2.inRange(hsv, lower, upper)
    result = cv2.bitwise_and(image, image, mask=mask)

    # Print if there is a change in HSV value
    if((phMin != hMin) | (psMin != sMin) | (pvMin != vMin) | (phMax != hMax) | (psMax != sMax) | (pvMax != vMax) ):
        print("(hMin = %d , sMin = %d, vMin = %d), (hMax = %d , sMax = %d, vMax = %d)" % (hMin , sMin , vMin, hMax, sMax , vMax))
        phMin = hMin
        psMin = sMin
        pvMin = vMin
        phMax = hMax
        psMax = sMax
        pvMax = vMax

    # Display result image
    cv2.imshow('image', result)
    if cv2.waitKey(10) & 0xFF == ord('q'):
        break

cv2.destroyAllWindows()
```



------------
    
    
## Answer \#1

 Vote: 6

Created at 2020-02-25 15:53:27

------------

Your code produces better results than this. Here, I set a threshold for `upperb` and `lowerb` values based on histogram `CDF` values and a threshold. Press `ESC` button to get next image.

This code is unnecessarily complex and needs to be optimized in various ways. Code can be reordered to skip some steps. I kept it as some parts may help others. Some existing noise can be removed by keeping contour with area above certain threshold. Any suggestions on other noise reduction method is welcome.

Similar easier code for getting 4 corner points for perspective transform can be found here,

[Accurate corners detection?](https://stackoverflow.com/questions/60355983/accurate-corners-detection/60357391#60357391)



- - - - - - - Mark the ROI by drawing rectangle and corner points in original image- Straighten the ROI and extract it- - - - - - 



```
## Press ESC button to get next image

import cv2
import cv2 as cv
import numpy as np


frame = cv2.imread('extra/c1.png')
#frame = cv2.imread('extra/c2.png')


## keeping a copy of original
print(frame.shape)
original_frame = frame.copy()
original_frame2 = frame.copy()


## Show the original image
winName = 'Original'
cv.namedWindow(winName, cv.WINDOW_NORMAL)
#cv.resizeWindow(winName, 800, 800)
cv.imshow(winName, frame)
cv.waitKey(0)



## Apply median blur
frame = cv2.medianBlur(frame,9)


## Show the original image
winName = 'Median Blur'
cv.namedWindow(winName, cv.WINDOW_NORMAL)
#cv.resizeWindow(winName, 800, 800)
cv.imshow(winName, frame)
cv.waitKey(0)


#kernel = np.ones((5,5),np.uint8)
#frame = cv2.dilate(frame,kernel,iterations = 1)



# Otsu's thresholding
frame = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
ret2,thresh_n = cv.threshold(frame,0,255,cv.THRESH_BINARY+cv.THRESH_OTSU)
frame = thresh_n


## Show the original image
winName = 'Otsu Thresholding'
cv.namedWindow(winName, cv.WINDOW_NORMAL)
#cv.resizeWindow(winName, 800, 800)
cv.imshow(winName, frame)
cv.waitKey(0)




## invert color
frame = cv2.bitwise_not(frame)

## Show the original image
winName = 'Invert Image'
cv.namedWindow(winName, cv.WINDOW_NORMAL)
#cv.resizeWindow(winName, 800, 800)
cv.imshow(winName, frame)
cv.waitKey(0)


## Dilate image
kernel = np.ones((5,5),np.uint8)
frame = cv2.dilate(frame,kernel,iterations = 1)


##
## Show the original image
winName = 'SUB'
cv.namedWindow(winName, cv.WINDOW_NORMAL)
#cv.resizeWindow(winName, 800, 800)
img_gray = cv2.cvtColor(original_frame, cv2.COLOR_BGR2GRAY)
cv.imshow(winName, img_gray & frame)
cv.waitKey(0)


## Show the original image
winName = 'Dilate Image'
cv.namedWindow(winName, cv.WINDOW_NORMAL)
#cv.resizeWindow(winName, 800, 800)
cv.imshow(winName, frame)
cv.waitKey(0)


## Get largest contour from contours
contours, hierarchy = cv2.findContours(frame, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)


## Get minimum area rectangle and corner points
rect = cv2.minAreaRect(max(contours, key = cv2.contourArea))
print(rect)
box = cv2.boxPoints(rect)
print(box)


## Sorted points by x and y
## Not used in this code
print(sorted(box , key=lambda k: [k[0], k[1]]))



## draw anchor points on corner
frame = original_frame.copy()
z = 6
for b in box:
    cv2.circle(frame, tuple(b), z, 255, -1)


## show original image with corners
box2 = np.int0(box)
cv2.drawContours(frame,[box2],0,(0,0,255), 2)
cv2.imshow('Detected Corners',frame)
cv2.waitKey(0)
cv2.destroyAllWindows()



## https://stackoverflow.com/questions/11627362/how-to-straighten-a-rotated-rectangle-area-of-an-image-using-opencv-in-python
def subimage(image, center, theta, width, height):
   shape = ( image.shape[1], image.shape[0] ) # cv2.warpAffine expects shape in (length, height)

   matrix = cv2.getRotationMatrix2D( center=center, angle=theta, scale=1 )
   image = cv2.warpAffine( src=image, M=matrix, dsize=shape )

   x = int(center[0] - width / 2)
   y = int(center[1] - height / 2)

   image = image[ y:y+height, x:x+width ]

   return image



## Show the original image
winName = 'Dilate Image'
cv.namedWindow(winName, cv.WINDOW_NORMAL)
#cv.resizeWindow(winName, 800, 800)


## use the calculated rectangle attributes to rotate and extract it
frame = subimage(original_frame, center=rect[0], theta=int(rect[2]), width=int(rect[1][0]), height=int(rect[1][1]))
original_frame = frame.copy()
cv.imshow(winName, frame)
cv.waitKey(0)

perspective_transformed_image = frame.copy()



## Apply median blur
frame = cv2.medianBlur(frame,11)


## Show the original image
winName = 'Median Blur'
cv.namedWindow(winName, cv.WINDOW_NORMAL)
#cv.resizeWindow(winName, 800, 800)
cv.imshow(winName, frame)
cv.waitKey(0)


#kernel = np.ones((5,5),np.uint8)
#frame = cv2.dilate(frame,kernel,iterations = 1)



# Otsu's thresholding
frame = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
ret2,thresh_n = cv.threshold(frame,0,255,cv.THRESH_BINARY+cv.THRESH_OTSU)
frame = thresh_n


## Show the original image
winName = 'Otsu Thresholding'
cv.namedWindow(winName, cv.WINDOW_NORMAL)
#cv.resizeWindow(winName, 800, 800)
cv.imshow(winName, frame)
cv.waitKey(0)



## invert color
frame = cv2.bitwise_not(frame)

## Show the original image
winName = 'Invert Image'
cv.namedWindow(winName, cv.WINDOW_NORMAL)
#cv.resizeWindow(winName, 800, 800)
cv.imshow(winName, frame)
cv.waitKey(0)


## Dilate image
kernel = np.ones((5,5),np.uint8)
frame = cv2.dilate(frame,kernel,iterations = 1)

##
## Show the original image
winName = 'SUB'
cv.namedWindow(winName, cv.WINDOW_NORMAL)
#cv.resizeWindow(winName, 800, 800)
img_gray = cv2.cvtColor(original_frame, cv2.COLOR_BGR2GRAY)
frame = img_gray & frame
frame[np.where(frame==0)] = 255
cv.imshow(winName, frame)
cv.waitKey(0)





hist,bins = np.histogram(frame.flatten(),256,[0,256])

cdf = hist.cumsum()
cdf_normalized = cdf * hist.max()/ cdf.max()
print(cdf)
print(cdf_normalized)
hist_image = frame.copy()




## two decresing range algorithm
low_index = -1
for i in range(0, 256):
   if cdf[i] > 0:
      low_index = i
      break
print(low_index)

tol = 0
tol_limit = 20
broken_index = -1
past_val = cdf[low_index] - cdf[low_index + 1]
for i in range(low_index + 1, 255):
   cur_val = cdf[i] - cdf[i+1]
   if tol > tol_limit:
      broken_index = i
      break
   if cur_val < past_val:
      tol += 1
   past_val = cur_val

print(broken_index)




##
lower = min(frame.flatten())
upper = max(frame.flatten())
print(min(frame.flatten()))
print(max(frame.flatten()))

#img_rgb_inrange = cv2.inRange(frame_HSV, np.array([lower,lower,lower]), np.array([upper,upper,upper]))
img_rgb_inrange = cv2.inRange(frame, (low_index), (broken_index))
neg_rgb_image = ~img_rgb_inrange
## Show the original image
winName = 'Final'
cv.namedWindow(winName, cv.WINDOW_NORMAL)
#cv.resizeWindow(winName, 800, 800)
cv.imshow(winName, neg_rgb_image)
cv.waitKey(0)


kernel = np.ones((3,3),np.uint8)
frame = cv2.erode(neg_rgb_image,kernel,iterations = 1)
winName = 'Final Dilate'
cv.namedWindow(winName, cv.WINDOW_NORMAL)
#cv.resizeWindow(winName, 800, 800)
cv.imshow(winName, frame)
cv.waitKey(0)


##
winName = 'Final Subtracted'
cv.namedWindow(winName, cv.WINDOW_NORMAL)
img2 = np.zeros_like(perspective_transformed_image)
img2[:,:,0] = frame
img2[:,:,1] = frame
img2[:,:,2] = frame
frame = img2
cv.imshow(winName, perspective_transformed_image | frame)
cv.waitKey(0)


##
import matplotlib.pyplot as plt
plt.plot(cdf_normalized, color = 'b')
plt.hist(hist_image.flatten(),256,[0,256], color = 'r')
plt.xlim([0,256])
plt.legend(('cdf','histogram'), loc = 'upper left')
plt.show()
```




[](https://i.stack.imgur.com/WLz95.png)



[](https://i.stack.imgur.com/xsVC9.png)



[](https://i.stack.imgur.com/hgwHb.png)



[](https://i.stack.imgur.com/6H88i.png)



[](https://i.stack.imgur.com/euJfJ.png)



[](https://i.stack.imgur.com/v04Ok.png)



[](https://i.stack.imgur.com/sXQdr.png)



[](https://i.stack.imgur.com/FDO1v.png)



[](https://i.stack.imgur.com/vIdIe.png)



[](https://i.stack.imgur.com/xxYU5.png)



[](https://i.stack.imgur.com/QMrDW.png)



[](https://i.stack.imgur.com/xmrzJ.png)



[](https://i.stack.imgur.com/ZbudT.png)



[](https://i.stack.imgur.com/nIpli.png)



[](https://i.stack.imgur.com/nU7Oz.png)


------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-02-25 09:30:17

------------

Didn't try , but this might work.
step 1:
use ps to find out what color the captcha characters are. For excample, "YabVzu" is (128,128,128),

Step 2:
Use pillow's method getdata()/getcolor(), it will return a sequence which contain the colour of every pixel. 

then ,we project every item in the sequence to the original captcha image.

hence we know the positon of every pixel in the image. 

Step 3:
find all pixels whose colour with the most approximate values to (128,128,128). 
You may set a threshold to control the accuracy. this step return another sequence. 
Lets annotate it as Seq a

Step 4:
generate a picture with the very same height and width as the original one.
plot every pixel in [Seq a] in the very excat position in the picture. Here,we will get a cleaned training items

Step 5:
Use a Keras [project](https://github.com/ypwhs/captcha_break) to break the code. And the precission should be over 72%.


------------
    
    