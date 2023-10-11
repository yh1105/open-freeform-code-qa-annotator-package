
# Post \#46415343 [Link](https://stackoverflow.com/questions/46415343/)

## Downgrade npm to an older version

**Vote**: 311 (5/702) **Views**: 486955 (8/702) 

**Internal ID** \#0-0-122

Created at 2017-09-25 22:54:08

Tags: `node.js` `npm` `installation` `downgrade`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I tried updating `npm` to see if it would solve some dependency problems we were having, and now I want to downgrade to the version the rest of the development team is using. How can I install an older version?
I updated `npm` according to the instructions on the [About npm CLI versions](https://docs.npmjs.com/about-npm-versions):
> 
## The latest release of npm

The latest release of `npm` is the most recent stable version. When you install Node.js, `npm` is automatically installed. However, `npm` is released more frequently than Node.js, so to install the latest stable version of `npm`, on the command line, run:`npm install npm@latest -g`


----------
        
## Answer \#0

**Accepted** Vote: 501

Created at 2017-09-25 22:54:08

------------

Just replace `@latest` with the version number you want to downgrade to. I wanted to downgrade to version 3.10.10, so I used this command:

```
npm install -g npm@3.10.10
```


If you're not sure which version you should use, look at the [version history](https://www.npmjs.com/package/npm?activeTab=versions). For example, you can see that 3.10.10 is the latest version of npm 3.


------------
    
    
## Answer \#1

 Vote: 56

Created at 2018-01-03 06:42:53

------------

Just need to add version of which you want
> upgrade or downgrade
```
npm install -g npm@version
```

 if you want to downgrade from npm 5.6.0 to 4.6.1 then,
```
npm install -g npm@4.6.1
```

It is tested on linux


------------
    
    
## Answer \#2

 Vote: 31

Created at 2018-03-21 10:24:49

------------

```
npm install -g npm@4
```

This will install the latest version on the major release 4, so no need to specify version number. Replace 4 with whatever major release you want.


------------
    
    
## Answer \#3

 Vote: 5

Created at 2018-03-28 06:45:38

------------

Even I run `npm install -g npm@4`, it is not ok for me.

Finally, I download and install the old node.js version.

[https://nodejs.org/download/release/v7.10.1/](https://nodejs.org/download/release/v7.10.1/)

It is npm version 4.

You can choose any version here
[https://nodejs.org/download/release/](https://nodejs.org/download/release/)


------------
    
    