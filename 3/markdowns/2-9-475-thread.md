
# Post \#63650689 [Link](https://stackoverflow.com/questions/63650689/)

## package configuration for libffi is not found in macOS while installing travis-cli

**Vote**: 22 (248/702) **Views**: 20511 (303/702) 

**Internal ID** \#2-9-475

Created at 2020-08-29 18:52:37

Tags: `ruby` `macos` `rubygems` `libffi` `travis-ci-cli`

----------

#### Metadata:

Area: `Back-end`

Language: `ruby` (full parsed tag list: `ruby`)

----------

**Notepad**


----------

I am trying to install `gem install travis` to use `travis-cli` as per reference [https://rubygems.org/gems/travis](https://rubygems.org/gems/travis) but getting below error.
Tried solution mentioned here [https://github.com/ffi/ffi/issues/653](https://github.com/ffi/ffi/issues/653) :
```
brew reinstall libffi
export LDFLAGS="-L/usr/local/opt/libffi/lib"
export PKG_CONFIG_PATH="/usr/local/opt/libffi/lib/pkgconfig"
gem install travis
```

> package configuration for libffi is not found
"xcrun clang -o conftest -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/include/ruby-2.3.0/universal-darwin16 -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/include/ruby-2.3.0/ruby/backward -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/include/ruby-2.3.0 -I. -D_XOPEN_SOURCE -D_DARWIN_C_SOURCE -D_DARWIN_UNLIMITED_SELECT -D_REENTRANT    -g -Os -pipe -DHAVE_GCC_ATOMIC_BUILTINS -iwithsysroot /usr/local/libressl/include conftest.c  -L. -L/System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib -L.             -L /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.12.Internal.sdk/usr/local/libressl/lib -L/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.12.Internal.sdk/usr/local/lib   -arch x86_64 -arch i386   -lruby.2.3.0  -lpthread -ldl -lobjc "
In file included from conftest.c:1:
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/include/ruby-2.3.0/ruby.h:33:
/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/include/ruby-2.3.0/ruby/ruby.h:24:10: fatal error: 'ruby/config.h' file not found
#include "ruby/config.h"
^~~~~~~~~~~~~~~
1 error generated.
checked program was:
/* begin */
1: #include "ruby.h"
2:
3: int main(int argc, char * end */
Any help will be appreciated! try finding out but didn't find helpful


----------
        
## Answer \#0

**Accepted** Vote: 62

Created at 2021-01-26 13:52:19

------------

This will do the trick
```
sudo gem install -n /usr/local/bin cocoapods -v 1.8.4
```

Latest CocoaPods 1.10.0 won't work.

if you want to instal CocoaPods 1.10.0 +  you can use [HomeBrew](https://brew.sh/) and then enter the below in command line
```
brew install cocoapods
```



------------
    
    
## Answer \#1

 Vote: 7

Created at 2020-11-17 07:45:35

------------

I had the same problem. I noticed the files are searched in
> /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/include/ruby-2.3.0/universal-darwin16
I had installed XCode 12.2 with its command line tools which didn't have that folder but universal-darwin20.
I solved it by soft linking that folder to universal-darwin16.
From terminal:
> ln -s
/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/include/ruby-2.3.0/universal-darwin20
/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/include/ruby-2.3.0/universal-darwin16


------------
    
    
## Answer \#2

 Vote: 5

Created at 2020-12-08 15:19:14

------------

Thanks to the previous response who symlinked the universal-darwin I figured out the location on mine was little different within Xcode app.
For anyone who might need this:
```
ln -s /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX11.0.sdk/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/include/ruby-2.6.0/universal-darwin20 /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX11.0.sdk/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/include/ruby-2.6.0/universal-darwin19
```



------------
    
    
## Answer \#3

 Vote: 4

Created at 2022-03-18 05:08:41

------------

This worked for me
```
brew install cocoapods
```



------------
    
    
## Answer \#4

 Vote: 3

Created at 2021-08-18 06:19:03

------------

I fixed it by Xcode.
```
$ sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
$ sudo gem install -n /usr/local/bin cocoapods
```



------------
    
    
## Answer \#5

 Vote: 1

Created at 2021-07-03 04:04:43

------------

This is the way I was able to install cocoapods verion 1.10.x on Catalina 10.15.x
Download Command Line Tools 11.5 (works on Catalina) from [Apple Developer Downloads](https://developer.apple.com/download/more/?=xcode)
```
sudo rm -rf /Library/Developer/CommandLineTools
Install Command Line Tools from .dmg/.pkg
sudo xcode-select --switch /Library/Developer/CommandLineTools
sudo gem install -n /usr/local/bin cocoapods
sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
```



------------
    
    