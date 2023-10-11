
# Post \#65438156 [Link](https://stackoverflow.com/questions/65438156/)

## Tensorflow Keras error: Unknown image file format. One of JPEG, PNG, GIF, BMP required

**Vote**: 8 (428/702) **Views**: 10410 (411/702) 

**Internal ID** \#1-3-296

Created at 2020-12-24 12:12:25

Tags: `python` `image` `tensorflow` `keras` `google-colaboratory`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

i'm training a classifier and i made sure all the pictures are jpg but still, this error occurs:
InvalidArgumentError:  Unknown image file format. One of JPEG, PNG, GIF, BMP required.
[[{{node decode_image/DecodeImage}}]]
[[IteratorGetNext]] [Op:__inference_train_function_1481]
i tried training on a smaller dataset and also they were all jpg and there was no problem
this is the code:
```
import numpy as np
import tensorflow as tf
from tensorflow import keras

dataset = keras.preprocessing.image_dataset_from_directory(
  '/content/drive/MyDrive/fi_dataset/train', batch_size=64, image_size=(200, 200))

dense = keras.layers.Dense(units=16)
inputs = keras.Input(shape=(None, None, 3))

from tensorflow.keras import layers

x = CenterCrop(height=150, width=150)(inputs)
x = Rescaling(scale=1.0 / 255)(x)

x = layers.Conv2D(filters=32, kernel_size=(3, 3), activation="relu")(x)
x = layers.MaxPooling2D(pool_size=(3, 3))(x)
x = layers.Conv2D(filters=32, kernel_size=(3, 3), activation="relu")(x)
x = layers.MaxPooling2D(pool_size=(3, 3))(x)
x = layers.Conv2D(filters=32, kernel_size=(3, 3), activation="relu")(x)

x = layers.GlobalAveragePooling2D()(x)

num_classes = 1
outputs = layers.Dense(num_classes, activation="sigmoid")(x)

model = keras.Model(inputs=inputs, outputs=outputs)

data = np.random.randint(0, 256, size=(64, 200, 200, 3)).astype("float32")
processed_data = model(data)

model.compile(optimizer='adam',
              loss='binary_crossentropy',
               metrics=[keras.metrics.binary_accuracy],)

history=model.fit(dataset, epochs=10)
```



----------
        
## Answer \#0

**Accepted** Vote: 9

Created at 2020-12-24 15:49:17

------------

When you say you made sure they were jpg's how did you verify that? Just because the extension is .jpg does not mean the file is a true jpg image. I suggest you run the code below to see which image may be defective.
```
import os
import cv2
def check_images( s_dir, ext_list):
    bad_images=[]
    bad_ext=[]
    s_list= os.listdir(s_dir)
    for klass in s_list:
        klass_path=os.path.join (s_dir, klass)
        print ('processing class directory ', klass)
        if os.path.isdir(klass_path):
            file_list=os.listdir(klass_path)
            for f in file_list:               
                f_path=os.path.join (klass_path,f)
                index=f.rfind('.')
                ext=f[index+1:].lower()
                if ext not in ext_list:
                    print('file ', f_path, ' has an invalid extension ', ext)
                    bad_ext.append(f_path)
                if os.path.isfile(f_path):
                    try:
                        img=cv2.imread(f_path)
                        shape=img.shape
                    except:
                        print('file ', f_path, ' is not a valid image file')
                        bad_images.append(f_path)
                else:
                    print('*** fatal error, you a sub directory ', f, ' in class directory ', klass)
        else:
            print ('*** WARNING*** you have files in ', s_dir, ' it should only contain sub directories')
    return bad_images, bad_ext

source_dir =r'c:\temp\people\storage'
good_exts=['jpg', 'png', 'jpeg', 'gif', 'bmp' ] # list of acceptable extensions
bad_file_list, bad_ext_list=check_images(source_dir, good_exts)
if len(bad_file_list) !=0:
    print('improper image files are listed below')
    for i in range (len(bad_file_list)):
        print (bad_file_list[i])
else:
    print(' no improper image files were found')
```

even this might not be enough because it checks the file's extension name.
Actually it might have an extension name jpg but be in say a tiff format.
To take it a stepfurther you can add some code that if the extension is not in
the good extension list you could read the image and if it is valid use cv2 to convert it to say a jpg and then write it back to the file.


------------
    
    
## Answer \#1

 Vote: 10

Created at 2021-12-15 12:02:55

------------

> Actually it might have an extension name jpg but be in say a
tiff format. To take it a stepfurther you can add some code ...
If you want to check for a , not an , then try this modificated version of code above:
```
import os
import cv2
import imghdr

def check_images( s_dir, ext_list):
    bad_images=[]
    bad_ext=[]
    s_list= os.listdir(s_dir)
    for klass in s_list:
        klass_path=os.path.join (s_dir, klass)
        print ('processing class directory ', klass)
        if os.path.isdir(klass_path):
            file_list=os.listdir(klass_path)
            for f in file_list:               
                f_path=os.path.join (klass_path,f)
                tip = imghdr.what(f_path)
                if ext_list.count(tip) == 0:
                  bad_images.append(f_path)
                if os.path.isfile(f_path):
                    try:
                        img=cv2.imread(f_path)
                        shape=img.shape
                    except:
                        print('file ', f_path, ' is not a valid image file')
                        bad_images.append(f_path)
                else:
                    print('*** fatal error, you a sub directory ', f, ' in class directory ', klass)
        else:
            print ('*** WARNING*** you have files in ', s_dir, ' it should only contain sub directories')
    return bad_images, bad_ext

source_dir =r'c:\temp\people\storage'
good_exts=['jpg', 'png', 'jpeg', 'gif', 'bmp' ] # list of acceptable extensions
bad_file_list, bad_ext_list=check_images(source_dir, good_exts)
if len(bad_file_list) !=0:
    print('improper image files are listed below')
    for i in range (len(bad_file_list)):
        print (bad_file_list[i])
else:
    print(' no improper image files were found')
```

Python has many modules in its standard library, and one that helps here is . It lets you identify what is the image type that is contained in a file, byte stream or path-like object. The imghdr can recognize the following image types: , , , , , , , , , , ,  and .


------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-08-08 19:02:31

------------

```
from pathlib import Path
import imghdr

data_dir = "/home/user/datasets/samples/"
image_extensions = [".png", ".jpg"]  # add there all your images file extensions

img_type_accepted_by_tf = ["bmp", "gif", "jpeg", "png"]
for filepath in Path(data_dir).rglob("*"):
    if filepath.suffix.lower() in image_extensions:
        img_type = imghdr.what(filepath)
        if img_type is None:
            print(f"{filepath} is not an image")
        elif img_type not in img_type_accepted_by_tf:
            print(f"{filepath} is a {img_type}, not accepted by TensorFlow")
```

Try this, it will print the file having issue


------------
    
    