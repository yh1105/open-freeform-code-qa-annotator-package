
# Post \#72306585 [Link](https://stackoverflow.com/questions/72306585/)

## Brighten only dark areas of image in python

**Vote**: 6 (473/702) **Views**: 1406 (630/702) 

**Internal ID** \#1-3-259

Created at 2022-05-19 14:40:45

Tags: `python` `image` `opencv` `image-processing` `python-imaging-library`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am trying to process images and I would like to brighten the dark areas of my image. I tried Histogram Equalization, however as there are also some bright areas in the image, the result is not satisfying. This is why I am looking for a way to brighten  of the image.
As an example, Input image is on the left and the expected result is on the right, the hair and face of the girl are brightened
[](https://i.stack.imgur.com/hEHxh.jpg)
[](https://i.stack.imgur.com/PhgAN.jpg)
ImageMagick seems to offer some possibilities to achieve this, however, I would like to do it using python


----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2022-05-19 15:50:47

------------

If you want to avoid colour distortions, you could:
- - - - - 
That might go something like this:
```
from PIL import Image

# Open the image
im = Image.open('hEHxh.jpg')

# Convert to HSV colourspace and split channels for ease of separate processing
H, S, V = im.convert('HSV').split()

# Increase the brightness, or Value channel
# Change 30 to 50 for bigger effect, or 10 for smaller effect
newV = V.point(lambda i: i + int(30*(255-i)/255))

# Recombine channels and convert back to RGB
res = Image.merge(mode="HSV", bands=(H,S,newV)).convert('RGB')

res.save('result.jpg')
```

[](https://i.stack.imgur.com/xIqTq.jpg)
Essentially, I am changing the brightness from the black mapping to the green mapping:
[](https://i.stack.imgur.com/DHqRD.png)

---


Reminder to forgetful self... :
```
import matplotlib.pyplot as plt
import numpy as np

# Generate some straight-line data
xdata = np.arange(0,256)
# And the new mapping
ydata = xdata + 30*(255-xdata)/255

# Plot
plt.plot(xdata,xdata,'.k')
plt.plot(xdata,ydata,'g^')
plt.title('Adjustment of V')
plt.xlabel('Input V')
plt.ylabel('Output V')
plt.grid(True)
plt.show()
```



------------
    
    
## Answer \#1

 Vote: 5

Created at 2022-05-19 16:57:25

------------

Here is one way to do that in Imagemagick and Python/OpenCV. Threshold the L channel of LAB colorspace using triangle method. Then brighten the whole image. Then merge the original and brightened image using the threshold as a mask.

```
magick girl_on_chair.jpg \
\( -clone 0 -colorspace LAB -channel 0 -separate +channel \
-auto-threshold triangle -negate +write thresh.png \) \
\( -clone 0 -evaluate multiply 4 \) \
+swap -compose over -composite \
girl_on_chair_processed.jpg
```

Threshold:
[](https://i.stack.imgur.com/qw54R.png)
Result:
[](https://i.stack.imgur.com/xdhyk.jpg)

```
import cv2
import numpy as np

# read image
img = cv2.imread("girl_on_chair.jpg")

# convert to LAB and extract L  channel
LAB = cv2.cvtColor(img, cv2.COLOR_BGR2LAB)
L = LAB[:,:,0]

# threshold L channel with triangle method
value, thresh = cv2.threshold(L, 0, 255, cv2.THRESH_BINARY+cv2.THRESH_TRIANGLE)
print(value)

# threshold with adjusted value
value = value + 10
thresh = cv2.threshold(L, value, 255, cv2.THRESH_BINARY)[1]

# invert threshold and make 3 channels
thresh = 255 - thresh
thresh = cv2.merge([thresh, thresh, thresh])

gain = 3
blue = cv2.multiply(img[:,:,0], gain)
green = cv2.multiply(img[:,:,1], gain)
red = cv2.multiply(img[:,:,2], gain)
img_bright = cv2.merge([blue, green, red])

# blend original and brightened using thresh as mask
result = np.where(thresh==255, img_bright, img)

# save result
cv2.imwrite('girl_on_chair_thresh.jpg', thresh)
cv2.imwrite('girl_on_chair_brighten.jpg', result)

cv2.imshow('img', img)
cv2.imshow('L', L)
cv2.imshow('thresh', thresh)
cv2.imshow('img_bright', img_bright)
cv2.imshow('result', result)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

Threshold:
[](https://i.stack.imgur.com/bY5WV.jpg)
Result:
[](https://i.stack.imgur.com/MYvc0.jpg)


------------
    
    
## Answer \#2

 Vote: 3

Created at 2022-05-19 19:02:11

------------

An alternate way to do this in Imagemagick is to use -fx to threshold and increase brightness using the L channel of LAB or the Y channel of YCbCr or the V channel of HSV by adding a constant value where that channel is less than some threshold.
`u`
```
magick girl_on_chair.jpg \
-colorspace LAB -separate \
\( -clone 0 -fx "u<0.15?u+0.15:u" \) \
-swap 0,3 +delete \
-set colorspace LAB -combine -colorspace sRGB \
girl_on_chair_proc2.jpg

magick girl_on_chair.jpg \
-colorspace YCBCR -separate \
\( -clone 0 -fx "u<0.15?u+0.15:u" \) \
-swap 0,3 +delete \
-set colorspace YCBCR -combine -colorspace sRGB \
girl_on_chair_proc3.jpg

magick girl_on_chair.jpg \
-colorspace HSV -separate \
\( -clone 2 -fx "u<0.15?u+0.15:u" \) \
+swap +delete \
-set colorspace HSV -combine -colorspace sRGB \
girl_on_chair_proc4.jpg
```

LAB Result:
[](https://i.stack.imgur.com/8XVic.jpg)
YCbCr Result:
[](https://i.stack.imgur.com/ZBWd0.jpg)
HSV Result:
[](https://i.stack.imgur.com/fA55I.jpg)
Similar processing can be done using Python/OpenCV as follows:
```
import cv2
import numpy as np

# read image
img = cv2.imread("girl_on_chair.jpg")

# convert to LAB and extract L  channel
LAB = cv2.cvtColor(img, cv2.COLOR_BGR2LAB)
L,A,B = cv2.split(LAB)

# process L channel
L_proc = np.where(L<40, cv2.add(L,40), L)

# recombine L_proc with A and B
LAB_new = cv2.merge([L_proc, A, B])

# convert back to BGR
result = cv2.cvtColor(LAB_new, cv2.COLOR_LAB2BGR)

# save result
cv2.imwrite('girl_on_chair_brighten2.jpg', result)

cv2.imshow('img', img)
cv2.imshow('L', L)
cv2.imshow('L_proc', L_proc)
cv2.imshow('result', result)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

Result:
[](https://i.stack.imgur.com/MFLoS.jpg)


------------
    
    
## Answer \#3

 Vote: 2

Created at 2022-05-19 16:42:04

------------

Here is how you can accomplish that with just the `cv2` module:
```
import cv2

def lighten(img, value=30):
    hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)
    v = hsv[..., 2]
    v[:] = cv2.add(v, value)
    return cv2.cvtColor(hsv, cv2.COLOR_HSV2BGR)

img = cv2.imread("image.jpg")
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
mask = cv2.threshold(gray, 175, 255, cv2.THRESH_BINARY)[1] == 0
img[mask] = lighten(img)[mask]
cv2.imshow("result", img)
cv2.waitKey(0)
```

Input Image and Output Image:
[](https://i.stack.imgur.com/4ejVQ.jpg) [](https://i.stack.imgur.com/EjbM3.png)


------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-05-22 11:55:40

------------

Here's a solution using the [tonelut](https://www.libvips.org/API/current/libvips-create.html#vips-tonelut) operation in [pyvips](https://pypi.org/project/pyvips/). It should be high quality, and pretty quick.
It brightens L* in CIELAB. This is a good choice for this sort of operation, since L* divides brightness into 100 steps of just visible difference. 20%, 50% and 80% of the L* range are useful rule of thumb  positions for the shadows, midtones and highlights.
It works like this:
- - - `tonelut`- `tonelut`- - 
Code:
```
#!/usr/bin/python3

import sys
import pyvips

input_filename = sys.argv[1]
output_filename = sys.argv[2]
shadow_boost = float(sys.argv[3])
midtone_boost = float(sys.argv[4])
highlight_boost = float(sys.argv[5])

image = pyvips.Image.new_from_file(sys.argv[1])

# we work in LABS format, a 16-bit version of LAB, with 0 - 32767 for L*, and
# +/- 32767 for a* and b* +/- 128
original_interpretation = image.interpretation
image = image.colourspace("labs")

# find the histogram of L* and use it to pick the 1% and 99% points as black
# and white
hist = image[0].hist_find();
norm = hist.hist_cum().hist_norm()  # normalised cumulative histogram
black = (norm > norm.width * 0.01).profile()[1].avg()
white = (norm > norm.width * 0.99).profile()[1].avg()

# tonelut wants percentages of 0-32767
black = 100.0 * black / 32767.0
white = 100.0 * white / 32767.0

# make a tone-mapping function using those black and white points
tone = pyvips.Image \
    .tonelut(Lb=black, Lw=white, \
        S=shadow_boost, M=midtone_boost, H=highlight_boost)

# remap L* of the image
image = (image[0].maplut(tone)).bandjoin(image[1:])

# back to the original interpretation, and save
image = image.colourspace(original_interpretation)

image.write_to_file(output_filename)
```

I can run it like this:
```
$ ./tone.py ~/pics/girl.jpg x.jpg +10 +10 0
```

That's boosting the midtones as well, since that's where the skin falls. Here's a before and after:
[](https://i.stack.imgur.com/aUj83.jpg)
We'd be able to pull more out of a better-quality test image.


------------
    
    