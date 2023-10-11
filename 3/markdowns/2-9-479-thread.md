
# Post \#59350892 [Link](https://stackoverflow.com/questions/59350892/)

## Installing ruby-2.1.2: Cannot load such file -- openssl (LoadError)

**Vote**: 18 (275/702) **Views**: 15639 (349/702) 

**Internal ID** \#2-9-479

Created at 2019-12-16 05:18:54

Tags: `ruby` `macos` `openssl` `rvm`

----------

#### Metadata:

Area: `Back-end`

Language: `ruby` (full parsed tag list: `ruby`)

----------

**Notepad**


----------

Getting few errors while installing ruby-2.1.2 using rvm. I am new to ruby and hence need assistance. Please let me know 

Installation log and other relevant information here: [log](https://paste.ubuntu.com/p/RTXNP2D25K/)

From the log provided in problem description, I have ruby 2.1.2 installed in the system. However, getting error when tried listing gems with `gem list` due to installation errors.After changing to 2.1.2 i.e., `rvm use 2.1.2` and executing `ruby -r openssl -e 'puts OpensSSL::OPENSSL_VERSION'` gives this error: 

> /Users/praveenk.k/.rvm/rubies/ruby-2.1.2/lib/ruby/site_ruby/2.1.0/rubygems/core_ext/kernel_require.rb:54:in require': cannot load such file -- openssl (LoadError) from /Users/praveenk.k/.rvm/rubies/ruby-2.1.2/lib/ruby/site_ruby/2.1.0/rubygems/core_ext/kernel_require.rb:54:in require'


----------
        
## Answer \#0

**Accepted** Vote: 17

Created at 2019-12-16 22:12:46

------------

Ruby <2.4 is incompatible with OpenSSL 1.1 (e.g see [https://github.com/rbenv/ruby-build/issues/1353](https://github.com/rbenv/ruby-build/issues/1353)).

Just a hunch, but if you're using Mac + Homebrew, OpenSSL 1.0 was recently deleted, so anything along the lines of `brew upgrade openssl` would trash that.

There are several options to deal with this:


1. I have older Rubies via asdf installed with LibreSSL (which may be safer anyway, see https://security.stackexchange.com/questions/112545/what-are-the-main-advantages-of-using-libressl-in-favor-of-openssl):



```
$ openssl
OpenSSL> version
LibreSSL 2.8.3
```



1. Compile and link your own OpenSSL 1.0 (or then set compiler flags as environment variables when building Ruby -- https://rvm.io/rubies/installing)
2. See if your system's package manager allows you to access openssl 1.0 and install with that (was helping a coworker with this recently and we weren't able to find a good keg for homebrew at this time, unfortunately)



There might be something less convoluted than that; if someone has ideas, please feel free to comment or give an alternate answer.



I personally ran into this issue with some other software and discovered that this fixed it, since the old SSL version was sitting around still:

```
brew switch openssl 1.0.2s
```


Should work so long as Homebrew doesn't "helpfully" clean that out for me.


------------
    
    
## Answer \#1

 Vote: 25

Created at 2020-04-29 03:32:50

------------

Worked example

```
brew install rbenv/tap/openssl@1.0
rvm install 2.1.2 -C --with-openssl-dir=`brew --prefix openssl@1.0`
```


It's from [https://github.com/rvm/rvm/issues/4819#issuecomment-595644550](https://github.com/rvm/rvm/issues/4819#issuecomment-595644550)


------------
    
    
## Answer \#2

 Vote: 12

Created at 2020-05-12 06:34:41

------------

For debian 10 : 

sudo apt-get install -y libssl-dev


------------
    
    
## Answer \#3

 Vote: 3

Created at 2021-09-24 13:47:54

------------

On mac BigSur, I fixed it via
```
$ rvm pkg install openssl
$ rvm install 2.1.2 -C --with-openssl-dir=$HOME/.rvm/usr
```

or you can also try with
```
$ rvm pkg install openssl
$ CFLAGS="-Wno-error=implicit-function-declaration" rvm install 2.1.2 -C --with-openssl-dir=$HOME/.rvm/usr
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2021-02-11 16:51:35

------------

For me solve this in centos 7
```
$ openssl version -a


OpenSSL 1.0.2k-fips  26 Jan 2017
built on: reproducible build, date unspecified
platform: linux-x86_64
options:  bn(64,64) md2(int) rc4(8x,int) des(idx,cisc,16,int) idea(int) blowfish(idx) 
compiler: gcc -I. -I.. -I../include  -fPIC -DOPENSSL_PIC -DZLIB -DOPENSSL_THREADS -D_REENTRANT -DDSO_DLFCN -DHAVE_DLFCN_H -DKRB5_MIT -m64 -DL_ENDIAN -Wall -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches   -m64 -mtune=generic -Wa,--noexecstack -DPURIFY -DOPENSSL_IA32_SSE2 -DOPENSSL_BN_ASM_MONT -DOPENSSL_BN_ASM_MONT5 -DOPENSSL_BN_ASM_GF2m -DRC4_ASM -DSHA1_ASM -DSHA256_ASM -DSHA512_ASM -DMD5_ASM -DAES_ASM -DVPAES_ASM -DBSAES_ASM -DWHIRLPOOL_ASM -DGHASH_ASM -DECP_NISTZ256_ASM

------------------------------------------------------------------------
OPENSSLDIR: "/etc/pki/tls" <- ************* COPY THIS PATH *************
------------------------------------------------------------------------

engines:  rdrand dynamic
```

paste the path in the bellow line how --with-openssl-dir param
```
$ RUBY_CONFIGURE_OPTS="--with-openssl-dir=/etc/pki/tls" rbenv install 2.7.2
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2020-11-30 20:23:43

------------

On Amazon Linux 2, even if openssl is installed, you need the full dev kit
```
yum install openssl-devel
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-01-21 16:47:18

------------

On Mac OS X Big Sur, to install Ruby 2.3.2 with RVM, the fix was
```
brew install rbenv/tap/openssl@1.0
CFLAGS="-Wno-error=implicit-function-declaration" rvm reinstall ruby-2.2.5 --with-openssl-dir='/usr/local/opt/openssl@1.0'
```

Reference was here -- [https://github.com/rvm/rvm/issues/4889](https://github.com/rvm/rvm/issues/4889)


------------
    
    