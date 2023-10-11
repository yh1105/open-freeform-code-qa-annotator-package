
# Post \#56823496 [Link](https://stackoverflow.com/questions/56823496/)

## How to fix "pip installation error on pillow"

**Vote**: 21 (256/702) **Views**: 74246 (118/702) 

**Internal ID** \#1-3-176

Created at 2019-06-30 08:33:08

Tags: `pip` `windows-10` `python-imaging-library`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I just ran `pip install pillow` on pycharm windows but it gives an error as mentioned below help me to solve this problem

I tired manually installing pillow form downloading pillow form github by
`python setup.py install` 

but didn't work on installing on pycharm I don't know what the error is
the entire process in mentioned below:-

```
Collecting Pillow
  Using cached https://files.pythonhosted.org/packages/81/1a/6b2971adc1bca55b9a53ed1efa372acff7e8b9913982a396f3fa046efaf8/Pillow-6.0.0.tar.gz
Installing collected packages: Pillow
  Running setup.py install for Pillow ... error
    Complete output from command C:\Users\rijal\Desktop\webdevlops\django_project\venv\Scripts\python.exe -u -c "import setuptools, tokenize;__file__='C:\\Users\\rijal\\AppData\\Local\\Temp\\pip-install-di5is9gd\\
Pillow\\setup.py';f=getattr(tokenize, 'open', open)(__file__);code=f.read().replace('\r\n', '\n');f.close();exec(compile(code, __file__, 'exec'))" install --record C:\Users\rijal\AppData\Local\Temp\pip-record-zdea
w2p6\install-record.txt --single-version-externally-managed --compile --install-headers C:\Users\rijal\Desktop\webdevlops\django_project\venv\include\site\python3.8\Pillow:
    C:\Users\rijal\AppData\Local\Temp\pip-install-di5is9gd\Pillow\setup.py:29: RuntimeWarning: Pillow does not yet support Python 3.8 and does not yet provide prebuilt Windows binaries. We do not recommend buildin
g from source on Windows.
      warnings.warn(
    running install
    running build
    running build_py
    creating build
    creating build\lib.win-amd64-3.8
    creating build\lib.win-amd64-3.8\PIL
    copying src\PIL\BdfFontFile.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\BlpImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\BmpImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\BufrStubImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ContainerIO.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\CurImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\DcxImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\DdsImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\EpsImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ExifTags.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\features.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\FitsStubImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\FliImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\FontFile.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\FpxImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\FtexImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\GbrImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\GdImageFile.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\GifImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\GimpGradientFile.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\GimpPaletteFile.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\GribStubImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\Hdf5StubImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\IcnsImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\IcoImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\Image.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageChops.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageCms.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageColor.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageDraw.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageDraw2.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageEnhance.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageFile.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageFilter.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageFont.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageGrab.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageMath.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageMode.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageMorph.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageOps.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImagePalette.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImagePath.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageQt.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageSequence.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageShow.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageStat.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageTk.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageTransform.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImageWin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\ImtImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\IptcImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\Jpeg2KImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\JpegImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\JpegPresets.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\McIdasImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\MicImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\MpegImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\MpoImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\MspImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\PaletteFile.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\PalmImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\PcdImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\PcfFontFile.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\PcxImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\PdfImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\PdfParser.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\PixarImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\PngImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\PpmImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\PsdImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\PSDraw.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\PyAccess.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\SgiImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\SpiderImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\SunImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\TarIO.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\TgaImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\TiffImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\TiffTags.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\WalImageFile.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\WebPImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\WmfImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\XbmImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\XpmImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\XVThumbImagePlugin.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\_binary.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\_tkinter_finder.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\_util.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\_version.py -> build\lib.win-amd64-3.8\PIL
    copying src\PIL\__init__.py -> build\lib.win-amd64-3.8\PIL
    running egg_info
    writing src\Pillow.egg-info\PKG-INFO
    writing dependency_links to src\Pillow.egg-info\dependency_links.txt
    writing top-level names to src\Pillow.egg-info\top_level.txt
    reading manifest file 'src\Pillow.egg-info\SOURCES.txt'
    reading manifest template 'MANIFEST.in'
```


and the error is like this

```
warning: no files found matching '*.c'
warning: no files found matching '*.h'
warning: no files found matching '*.sh'
no previously-included directories found matching 'docs\_static'
warning: no previously-included files found matching '.appveyor.yml'
warning: no previously-included files found matching '.coveragerc'
warning: no previously-included files found matching '.codecov.yml'
warning: no previously-included files found matching '.editorconfig'
warning: no previously-included files found matching '.landscape.yaml'
warning: no previously-included files found matching '.readthedocs.yml'
warning: no previously-included files found matching 'azure-pipelines.yml'
warning: no previously-included files found matching 'tox.ini'
warning: no previously-included files matching '.git*' found anywhere in distribution
warning: no previously-included files matching '*.pyc' found anywhere in distribution
warning: no previously-included files matching '*.so' found anywhere in distribution
no previously-included directories found matching '.azure-pipelines'
no previously-included directories found matching '.travis'
writing manifest file 'src\Pillow.egg-info\SOURCES.txt'
running build_ext

The headers or library files could not be found for zlib,
a required dependency when compiling Pillow from source.

Please see the install instructions at:
   https://pillow.readthedocs.io/en/latest/installation.html

Traceback (most recent call last):
  File "C:\Users\rijal\AppData\Local\Temp\pip-install-di5is9gd\Pillow\setup.py", line 759, in <module>
    setup(name=NAME,
  File "C:\Users\rijal\Desktop\webdevlops\django_project\venv\lib\site-packages\setuptools-40.8.0-py3.8.egg\setuptools\__init__.py", line 145, in setup
  File "C:\Users\rijal\AppData\Local\Programs\Python\Python38\lib\distutils\core.py", line 148, in setup
    dist.run_commands()
  File "C:\Users\rijal\AppData\Local\Programs\Python\Python38\lib\distutils\dist.py", line 966, in run_commands
    self.run_command(cmd)
  File "C:\Users\rijal\AppData\Local\Programs\Python\Python38\lib\distutils\dist.py", line 985, in run_command
    cmd_obj.run()
  File "C:\Users\rijal\Desktop\webdevlops\django_project\venv\lib\site-packages\setuptools-40.8.0-py3.8.egg\setuptools\command\install.py", line 61, in run
  File "C:\Users\rijal\AppData\Local\Programs\Python\Python38\lib\distutils\command\install.py", line 545, in run
    self.run_command('build')
  File "C:\Users\rijal\AppData\Local\Programs\Python\Python38\lib\distutils\cmd.py", line 313, in run_command
    self.distribution.run_command(command)
  File "C:\Users\rijal\AppData\Local\Programs\Python\Python38\lib\distutils\dist.py", line 985, in run_command
    cmd_obj.run()
  File "C:\Users\rijal\AppData\Local\Programs\Python\Python38\lib\distutils\command\build.py", line 135, in run
    self.run_command(cmd_name)
  File "C:\Users\rijal\AppData\Local\Programs\Python\Python38\lib\distutils\cmd.py", line 313, in run_command
    self.distribution.run_command(command)
  File "C:\Users\rijal\AppData\Local\Programs\Python\Python38\lib\distutils\dist.py", line 985, in run_command
    cmd_obj.run()
  File "C:\Users\rijal\AppData\Local\Programs\Python\Python38\lib\distutils\command\build_ext.py", line 340, in run
    self.build_extensions()
  File "C:\Users\rijal\AppData\Local\Temp\pip-install-di5is9gd\Pillow\setup.py", line 606, in build_extensions
    raise RequiredDependencyException(f)
__main__.RequiredDependencyException: zlib

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "<string>", line 1, in <module>
  File "C:\Users\rijal\AppData\Local\Temp\pip-install-di5is9gd\Pillow\setup.py", line 804, in <module>
    raise RequiredDependencyException(msg)
__main__.RequiredDependencyException:

The headers or library files could not be found for zlib,
a required dependency when compiling Pillow from source.

Please see the install instructions at:
   https://pillow.readthedocs.io/en/latest/installation.html

----------------------------------------
Command "C:\Users\rijal\Desktop\webdevlops\django_project\venv\Scripts\python.exe -u -c "import setuptools, tokenize;__file__='C:\\Users\\rijal\\AppData\\Local\\Temp\\pip-install-di5is9gd\\Pillow\\setup.py';f=geta
ttr(tokenize, 'open', open)(__file__);code=f.read().replace('\r\n', '\n');f.close();exec(compile(code, __file__, 'exec'))" install --record C:\Users\rijal\AppData\Local\Temp\pip-record-zdeaw2p6\install-record.txt
--single-version-externally-managed --compile --install-headers C:\Users\rijal\Desktop\webdevlops\django_project\venv\include\site\python3.8\Pillow" failed with error code 1 in C:\Users\rijal\AppData\Local\Temp\pi
p-install-di5is9gd\Pillow\
```



----------
        
## Answer \#0

**Accepted** Vote: 46

Created at 2019-11-19 13:09:39

------------

Pillow 6.2.0 now supports Python 3.8 and has prebuilt wheels available.

Make sure you're using Python 3.8.0 final or later with pip 19.2 or later.

To upgrade:

```
python -m pip install --upgrade pip
python -m pip install --upgrade pillow
```



---



Reason for preferring `python -m pip install` over `pip install`:

- [https://snarky.ca/why-you-should-use-python-m-pip/](https://snarky.ca/why-you-should-use-python-m-pip/)


------------
    
    
## Answer \#1

 Vote: 6

Created at 2022-04-24 08:00:58

------------

I had the same missing zlib issue on an old Ubuntu 18.04 VM.
```
The headers or library files could not be found for zlib,
a required dependency when compiling Pillow from source.
```

Installing the zlib dev package made it go further. It then hit similar issue for jpeg and png libraries.
This is what finally worked for me:
```
sudo apt install zlib1g-dev libjpeg-dev libpng-dev
```



------------
    
    
## Answer \#2

 Vote: 3

Created at 2020-01-26 23:17:55

------------

If you are using windows make sure python is 3.8 and pip v19.0. then use python -m pip install --upgrade Pillow to install v7. this worked for me


------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-10-21 13:36:51

------------

On Windows using MSYS2/MinGW
To build Pillow using MSYS2, make sure you run the MSYS2 MinGW 32-bit or MSYS2 MinGW 64-bit console, not MSYS2 directly.
The following instructions target the 64-bit build, for 32-bit replace all occurrences of `mingw-w64-x86_64-` with `mingw-w64-i686-`.
Make sure you have Python and GCC installed:
```
pacman -S \
    mingw-w64-x86_64-gcc \
    mingw-w64-x86_64-python3 \
    mingw-w64-x86_64-python3-pip \
    mingw-w64-x86_64-python3-setuptools
```

Then install Prerequisites on MSYS2 MinGW 64-bit with:
```
pacman -S \
    mingw-w64-x86_64-libjpeg-turbo \
    mingw-w64-x86_64-zlib \
    mingw-w64-x86_64-libtiff \
    mingw-w64-x86_64-freetype \
    mingw-w64-x86_64-lcms2 \
    mingw-w64-x86_64-libwebp \
    mingw-w64-x86_64-openjpeg2 \
    mingw-w64-x86_64-libimagequant \
    mingw-w64-x86_64-libraqm
```

Now install Pillow with:
```
python3 -m pip install --upgrade pip
python3 -m pip install --upgrade Pillow
```

It works for me.


------------
    
    
## Answer \#4

 Vote: -1

Created at 2021-07-16 11:52:31

------------

After trying everything else, I Could resolve the issue by updating the python version from 3.8.9 to latest version(3.9.*).


------------
    
    
## Answer \#5

 Vote: -2

Created at 2020-04-30 20:33:49

------------

Try 
pip install pillow
and then
python -m pip install pillow


------------
    
    