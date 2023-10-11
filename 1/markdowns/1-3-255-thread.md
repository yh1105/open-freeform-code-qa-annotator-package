
# Post \#63732353 [Link](https://stackoverflow.com/questions/63732353/)

## ERROR: Could not build wheels for opencv-python which use PEP 517 and cannot be installed directly

**Vote**: 28 (207/702) **Views**: 77017 (110/702) 

**Internal ID** \#1-3-255

Created at 2020-09-03 22:15:17

Tags: `python` `docker` `opencv` `cmake` `nvidia-jetson-nano`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I was trying to install OpenCV4 in a docker on jetson nano. It has jetpack 4.4 s os. The docker was successfully created and Tensorflow is running but while installing OpenCV using pip it is showing CMake error.
```
root@5abf405fb92d:~# pip3 install opencv-python
Collecting opencv-python
  Downloading opencv-python-4.4.0.42.tar.gz (88.9 MB)
     |████████████████████████████████| 88.9 MB 2.5 kB/s 
  Installing build dependencies ... done
  Getting requirements to build wheel ... done
    Preparing wheel metadata ... done
Requirement already satisfied: numpy>=1.13.3 in /usr/local/lib/python3.6/dist-packages (from opencv-python) (1.18.4)
Building wheels for collected packages: opencv-python
  Building wheel for opencv-python (PEP 517) ... error
  ERROR: Command errored out with exit status 1:
   command: /usr/bin/python3 /usr/local/lib/python3.6/dist-packages/pip/_vendor/pep517/_in_process.py build_wheel /tmp/tmpqpzwrofy
       cwd: /tmp/pip-install-93nxibky/opencv-python
  Complete output (9 lines):
    File "/tmp/pip-build-env-o_hualnr/overlay/lib/python3.6/site-packages/skbuild/setuptools_wrap.py", line 560, in setup
      cmkr = cmaker.CMaker(cmake_executable)
    File "/tmp/pip-build-env-o_hualnr/overlay/lib/python3.6/site-packages/skbuild/cmaker.py", line 95, in __init__
      self.cmake_version = get_cmake_version(self.cmake_executable)
    File "/tmp/pip-build-env-o_hualnr/overlay/lib/python3.6/site-packages/skbuild/cmaker.py", line 82, in get_cmake_version
      "Problem with the CMake installation, aborting build. CMake executable is %s" % cmake_executable)
  Traceback (most recent call last):
  
  Problem with the CMake installation, aborting build. CMake executable is cmake
  ----------------------------------------
  ERROR: Failed building wheel for opencv-python
Failed to build opencv-python
ERROR: Could not build wheels for opencv-python which use PEP 517 and cannot be installed directly
```



----------
        
## Answer \#0

**Accepted** Vote: 76

Created at 2020-09-05 09:40:34

------------

I had the same problem and i did this,
```
pip install --upgrade pip setuptools wheel
```

then install opencv again,
```
pip install opencv-python
```

this worked for me


------------
    
    
## Answer \#1

 Vote: 12

Created at 2021-06-06 01:28:26

------------

If after
```
pip install --upgrade pip setuptools wheel
```

you still have the same error,
You can try to specify the older version of OpenCV to install.
Ex.
```
pip3 install opencv-python==3.4.13.47
```



------------
    
    
## Answer \#2

 Vote: 5

Created at 2020-09-28 12:41:27

------------

Yes .. Finally found a work around.
Follow this [https://github.com/mdegans/nano_build_opencv](https://github.com/mdegans/nano_build_opencv) and build from source and finally gets installed.
PS: It may take a bit long for building, for me it took 10 Hrs :P.
Happy Image-Processing..


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-08-23 16:51:15

------------

I had a similar problem and what solved it for me was not to use `python:3-alpine` but `python:3.8-slim`. E.g.:
```
FROM python:3.8-slim
WORKDIR /usr/src/app

RUN apt update
RUN apt -y install build-essential libwrap0-dev libssl-dev libc-ares-dev uuid-dev xsltproc
RUN apt-get update -qq \
    && apt-get install --no-install-recommends --yes \
        build-essential \
        gcc \
        python3-dev \
        mosquitto \
        mosquitto-clients


RUN pip3 install --upgrade pip setuptools wheel

RUN python3 -m pip install --no-cache-dir \
      numpy scipy matplotlib scikit-build opencv-contrib-python-headless \
      influxdb paho-mqtt configparser Pillow \
      qrcode
```

worked finally for me.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-11-02 01:52:12

------------

please check your python specifications:
```
- opencv -> python[
version='
>=2.7,<2.8.0a0
>=3.5,<3.6.0a0
>=3.6,<3.7.0a0
>=3.7,<3.8.0a0']
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2021-11-17 13:18:32

------------

I handled it by reinstalling python3 from scratch in my MacBook:
```
brew reinstall python@3.9
```

I also reinstalled numpy and matplotlib packages experimentally.
```
pip3 install numpy
pip3 install matplotlib 
pip3 install opencv-contrib-python
```

The versions:
macOS Mojave 10.14.5
Python 3.9.7
OpenCV 4.5.3
OpenCV's version is 4.5.3 [by this way](https://kirr.co/uux6ty):
```
import cv2 
print(cv2.__version__)
```

But by "[pip list](https://note.nkmk.me/en/python-pillow-basic/)", it shows "opencv-contrib-python 3.4.9.31".


------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-04-17 09:53:24

------------

if you are trying to install opencv in your raspberrypi 3B, Use following steps:

1. sudo raspi-config
2. advanced -- expand filesystem
3. reboot your pi


Open your raspi terminal and do following stuff:

1. use command: sudo apt-get update
2. use command: sudo apt-get upgrade
3. check your python version and upgarde it to latest one
4. install pip and upgrade pip
5. use command: mkdir project
6. use command: cd project
7. create virtual environment
8. activate virtual environment
9. install dependencies ,can get dependencies from https://singleboardbytes.com/647/install-opencv-raspberry-pi-4.htm
10. if error in installing libdhf5-dev
11. use command: sudo apt-get install python3-h5py and reinstall libdhf5-dev
12. use command: pip install scikit-build
13. use command: pip install cython
14. before installing opencv ,make sure you are in virtual environment or activate environment
15. use command: pip install --no-cache-dir opencv-contrib-python==4.5.3.56
16. Remember to use mentioned version...


Thank You...


------------
    
    
## Answer \#7

 Vote: 0

Created at 2022-05-07 14:33:45

------------

I came across similiar situation
I had error
> Failed to build opencv-python ERROR: Could not build wheels for
opencv-python which use PEP 517 and cannot be installed directly
WARNING: You are using pip version 19.2.3, however version 22.0.4 is
available. You should consider upgrading via the 'python -m pip
install --upgrade pip' command.
I ran in command prompt with admin privileges following
python -m pip install --upgrade pip
Now "pip install opencv-python" works


------------
    
    