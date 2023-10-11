
# Post \#45801457 [Link](https://stackoverflow.com/questions/45801457/)

## Node.js: Python not found exception due to node-sass and node-gyp

**Vote**: 379 (3/702) **Views**: 575107 (4/702) 

**Internal ID** \#0-0-5

Created at 2017-08-21 16:02:37

Tags: `node.js` `node-gyp` `node-sass`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

Suddenly in one of my jenkins environment build has started failing, while in local machine it seems to be working fine as i have python installed,

From the logs i was able to detect that the problem is with internal dependency that is  from  when i researched by visiting [this](https://github.com/nodejs/node-gyp) about node-gyp and found the prerequisite that Python needs to be installed.

So my question is that what version of node-sass can i install to bypass this or is there a better solution as my build was running fine till this morning on the same environment.





```
gyp verb check python checking for Python executable "python2" in the PATH
gyp verb `which` failed Error: not found: python2
gyp verb `which` failed     at getNotFoundError (C:\Program Files (x86)\Jenkins\jobs\NdbSite-hot-fix-Manual-PreBuild\workspace\src\NdbSite.UI\node_modules\which\which.js:13:12)
gyp verb `which` failed     at F (C:\Program Files (x86)\Jenkins\jobs\NdbSite-hot-fix-Manual-PreBuild\workspace\src\NdbSite.UI\node_modules\which\which.js:68:19)
gyp verb `which` failed     at E (C:\Program Files (x86)\Jenkins\jobs\NdbSite-hot-fix-Manual-PreBuild\workspace\src\NdbSite.UI\node_modules\which\which.js:80:29)
gyp verb `which` failed     at C:\Program Files (x86)\Jenkins\jobs\NdbSite-hot-fix-Manual-PreBuild\workspace\src\NdbSite.UI\node_modules\which\which.js:89:16
gyp verb `which` failed     at C:\Program Files (x86)\Jenkins\jobs\NdbSite-hot-fix-Manual-PreBuild\workspace\src\NdbSite.UI\node_modules\isexe\index.js:44:5
gyp verb `which` failed     at C:\Program Files (x86)\Jenkins\jobs\NdbSite-hot-fix-Manual-PreBuild\workspace\src\NdbSite.UI\node_modules\isexe\windows.js:29:5
gyp verb `which` failed     at FSReqWrap.oncomplete (fs.js:82:15)
gyp verb `which` failed  python2 { [Error: not found: python2] code: 'ENOENT' }
gyp verb check python checking for Python executable "python" in the PATH
gyp verb `which` failed Error: not found: python
gyp verb `which` failed     at getNotFoundError (C:\Program Files (x86)\Jenkins\jobs\NdbSite-hot-fix-Manual-PreBuild\workspace\src\NdbSite.UI\node_modules\which\which.js:13:12)
gyp verb `which` failed     at F (C:\Program Files (x86)\Jenkins\jobs\NdbSite-hot-fix-Manual-PreBuild\workspace\src\NdbSite.UI\node_modules\which\which.js:68:19)
gyp verb `which` failed     at E (C:\Program Files (x86)\Jenkins\jobs\NdbSite-hot-fix-Manual-PreBuild\workspace\src\NdbSite.UI\node_modules\which\which.js:80:29)
gyp verb `which` failed     at C:\Program Files (x86)\Jenkins\jobs\NdbSite-hot-fix-Manual-PreBuild\workspace\src\NdbSite.UI\node_modules\which\which.js:89:16
gyp verb `which` failed     at C:\Program Files (x86)\Jenkins\jobs\NdbSite-hot-fix-Manual-PreBuild\workspace\src\NdbSite.UI\node_modules\isexe\index.js:44:5
gyp verb `which` failed     at C:\Program Files (x86)\Jenkins\jobs\NdbSite-hot-fix-Manual-PreBuild\workspace\src\NdbSite.UI\node_modules\isexe\windows.js:29:5
gyp verb `which` failed     at FSReqWrap.oncomplete (fs.js:82:15)
gyp verb `which` failed  python { [Error: not found: python] code: 'ENOENT' }
gyp verb could not find "python". checking python launcher 
gyp verb could not find "python". guessing location 
gyp verb ensuring that file exists: C:\Python27\python.exe
gyp ERR! configure error 
gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.
gyp ERR! stack     at Object.failNoPython (C:\Program Files (x86)\Jenkins\jobs\NdbSite-hot-fix-Manual-PreBuild\workspace\src\NdbSite.UI\node_modules\node-gyp\lib\configure.js:454:19)
gyp ERR! stack     at Object.<anonymous> (C:\Program Files (x86)\Jenkins\jobs\NdbSite-hot-fix-Manual-PreBuild\workspace\src\NdbSite.UI\node_modules\node-gyp\lib\configure.js:480:16)
gyp ERR! stack     at C:\Program Files (x86)\Jenkins\jobs\NdbSite-hot-fix-Manual-PreBuild\workspace\src\NdbSite.UI\node_modules\graceful-fs\polyfills.js:284:29
gyp ERR! stack     at FSReqWrap.oncomplete (fs.js:82:15)
gyp ERR! System Windows_NT 6.3.9600
gyp ERR! command "C:\\Program Files\\nodejs\\node.exe" "C:\\Program Files (x86)\\Jenkins\\jobs\\NdbSite-hot-fix-Manual-PreBuild\\workspace\\src\\NdbSite.UI\\node_modules\\node-gyp\\bin\\node-gyp.js" "rebuild" "--verbose" "--libsass_ext=" "--libsass_cflags=" "--libsass_ldflags=" "--libsass_library="
gyp ERR! cwd C:\Program Files (x86)\Jenkins\jobs\NdbSite-hot-fix-Manual-PreBuild\workspace\src\NdbSite.UI\node_modules\node-sass
gyp ERR! node -v v5.10.1
gyp ERR! node-gyp -v v3.5.0
gyp ERR! not ok 
Build failed
```


Any thoughts are much appreciated, Thanks. 


----------
        
## Answer \#0

**Accepted** Vote: 33

Created at 2017-08-22 00:51:13

------------

Node-sass tries to download the binary for you platform when installing. Node 5 is supported by 3.8 [https://github.com/sass/node-sass/releases/tag/v3.8.0](https://github.com/sass/node-sass/releases/tag/v3.8.0)
If your Jenkins can't download the prebuilt binary, then you need to follow the platform requirements on Node-gyp README (Python2, VS or MSBuild, ...)
If possible I'd suggest updating your Node to at least 6 since 5 isn't supported by Node anymore.
If you want to upgrade to 8, you'll need to update node-sass to 4.5.3


------------
    
    
## Answer \#1

 Vote: 361

Created at 2018-10-26 13:17:04

------------

so this happened to me on windows recently. I fix it by following the following steps using a PowerShell with admin privileges:

1. delete node_modulesfolder
2. running npm install --global windows-build-tools with administrative privilege. (in my case need restart - and restart without ask!!!)
3. reinstalling node modules or node-sass with npm install




------------
    
    
## Answer \#2

 Vote: 142

Created at 2020-10-22 16:37:22

------------

The error message means that it cannot locate your python executable or binary.
In many cases, it's installed at c:\python27.
if it's not installed yet, you can install it with `npm install --global windows-build-tools`, which will only work if it hasn't been installed yet.
Adding it to the environment variables does not always work.
A better alternative, is to just set it in the npm config.
`npm config set python "C:\Python27\python.exe"`


------------
    
    
## Answer \#3

 Vote: 93

Created at 2021-09-09 14:54:28

------------

You need to follow the below steps to fix the error:
```
1: DELETE node_modules
    2: npm install node-sass@<version-as-per-below-table>
    3. npm install
```


Below is a quick guide for minimum and maximum support supported version of node-sass ([Check updates to below table here](https://www.npmjs.com/package/node-sass)) :

---


```
| NodeJS  | Supported node-sass version | Node Module |
|---------|-----------------------------|-------------|
| Node 16 | 6.0+                        | 93          |
| Node 15 | 5.0+                        | 88          |
| Node 14 | 4.14+                       | 83          |
| Node 13 | 4.13+, <5.0                 | 79          |
| Node 12 | 4.12+                       | 72          |
| Node 11 | 4.10+, <5.0                 | 67          |
| Node 10 | 4.9+, <6.0                  | 64          |
| Node 8  | 4.5.3+, <5.0                | 57          |
| Node <8 | <5.0                        | <57         |
```


---




------------
    
    
## Answer \#4

 Vote: 37

Created at 2019-10-07 18:44:04

------------

My machine is , I've faced similar problems while tried to compile  using `node-sass` package. My node version is  and npm version is 

The way that I resolved the problem:


1. At first delete package-lock.json file and node_modules/ folder.
2. Open Windows PowerShell as Administrator.
3. Run the command npm i -g node-sass.
4. After that, go to the project folder and run npm install
5. And finally, run the SASS compiling script, in my case, it is npm run build:css



And it works!!


------------
    
    
## Answer \#5

 Vote: 37

Created at 2021-12-08 12:15:25

------------


## Two options (December 2021):


- - `.exe`- `PATH`- 

## Precondition


You updated Node.JS to the latest version (v16.13.1) on Windows 10.
You run `yarn install` or `npm install` in your project's folder and receive an error (look below).

## Option 1. Installing of Python-2 and fixing PATH env


[](https://i.stack.imgur.com/wAT87.png)
[See MSI installer](https://www.python.org/downloads/release/python-2718/) [](https://i.stack.imgur.com/UQS7g.png)
 [](https://i.stack.imgur.com/3Q5E5.png)
 Try to reinstall the latest Node.js to x86 from x64, if you get the following error on x64. (But with x86 you could receive memory limit errors, also.)
```
Error: Node Sass does not yet support your current environment: Windows 64-bit with Unsupported runtime (93)
```


#### Resort Python records in PATH env variable


Python 3 records must be above Python 2 records!
[](https://i.stack.imgur.com/mCyNV.png)
[](https://i.stack.imgur.com/hY5OB.png)

#### Copy python.exe to python2.exe


[](https://i.stack.imgur.com/MUqPf.png)

## Option 2. Upgrading of node-sass package



#### Over package.json file


Modify `package.json` and run `yarn install` or `npm install` on your project's folder.
Other files will be updated automatically.
Before [](https://i.stack.imgur.com/XpPy1.png) [](https://i.stack.imgur.com/rDGfh.png)
After updating: [](https://i.stack.imgur.com/tDSXy.png)
Or use yarn or npm CLI to do the same but it may noticeably change your `.lock` files.
After updating you could realize that you need to update also `sass-loader`, `webpack`, etc.

## Error you may have (FYI)


```
gyp verb check python checking for Python executable "python2" in the PATH
gyp verb `which` failed Error: not found: python2

gyp ERR! stack Error: Command failed: C:\Python310\python.EXE -c import sys; print "%s.%s.%s" % sys.version_info[:3];
gyp ERR! stack   File "<string>", line 1
gyp ERR! stack     import sys; print "%s.%s.%s" % sys.version_info[:3];
gyp ERR! stack                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
gyp ERR! stack SyntaxError: Missing parentheses in call to 'print'. Did you mean print(...)?
```



------------
    
    
## Answer \#6

 Vote: 25

Created at 2019-05-09 17:06:14

------------

I had to:

```
Delete node_modules
Uninstall/reinstall node
npm install node-sass@4.12.0
```


worked fine after forcing it to the right sass version, according to the version said to be working with the right node. 

```
NodeJS  Minimum node-sass version   Node Module
Node 12 4.12+   72
Node 11 4.10+   67
Node 10 4.9+    64
Node 8  4.5.3+  57
```


There was lots of other errors that seemed to be caused by the wrong sass version defined. 


------------
    
    
## Answer \#7

 Vote: 25

Created at 2020-10-30 15:23:49

------------

 requires old  - [link](https://github.com/nodejs/node-gyp/issues/1268#issuecomment-323550728)
If you don't have it installed - check other answers about installing .
If you are like me and have both , chances are that node-gyp tries to use Python 3. And that results in the following `SyntaxError: invalid syntax` error.
I found an article about having two Python versions installed. And they recommend renaming Python 2.* executable to `python2.exe` - [link](https://datascience.com.co/how-to-install-python-2-7-and-3-6-in-windows-10-add-python-path-281e7eae62a).
So it looks like node-gyp is expecting to find old Python 2 executable renamed. Hence the error message:
```
...
gyp verb check python checking for Python executable "python2" in the PATH
gyp verb `which` failed Error: not found: python2
...
```

Once I renamed `C:\Python27\python.exe` to `C:\Python27\python2.exe` it worked without errors.
Of course, both `C:\Python27\` and `C:\Python39\` have to be in PATH variable. And no need in setting old Python version in npm config. Your default Python still will be the new one.


------------
    
    
## Answer \#8

 Vote: 23

Created at 2022-05-15 14:04:39

------------

go to package.json and change
```
"node-sass": "4.14.1",
```

with
```
"node-sass": "^7.0.1",
```


in other word you need to use the latest package which can support the latest python version.
 Node Saas 7.0.1 is compatible with  "react-scripts": "5.0.0" if your package.json has  "react-scripts": "4.0.0", then change it as
```
"react-scripts": "5.0.0",
```



------------
    
    
## Answer \#9

 Vote: 18

Created at 2022-05-05 04:56:20

------------

[node-sass](https://sass-lang.com/blog/libsass-is-deprecated)
No need to mess around with installing old versions of Python, just migrate to [dart.sass](https://sass-lang.com/dart-sass)
For my ReactJS project (npm), the following steps worked for me:

1. Delete node_modules from the project
2. In package.json, remove this dependency: "dependencies": {
    …,
    "node-sass": "^3.0.0",
    …
}
3. npm install --save-dev sass
4. npm install
5. Now try running your project using npm start
6. If you receive an error like "To import Sass files, you first need to install node-sass", run this command as described here: npm install node-sass@npm:sass


If you're using yarn, do the equivalent commands but on the last step do this instead, as described [here](https://stackoverflow.com/questions/63943756/replace-node-sass-with-dart-sass-in-create-react-app-v3-x):
`yarn remove node-sass`
`yarn add sass`

---




------------
    
    
## Answer \#10

 Vote: 7

Created at 2020-01-31 17:56:10

------------


Node version: `v10.16.0`
NPM: `6.9.0`

I was having a lot of trouble using [node-sass](https://www.npmjs.com/package/node-sass) and [node-sass-middleware](https://www.npmjs.com/package/node-sass-middleware). They are interesting packages because they are widely used (millions of downloads weekly), but their githubs show a limited dependencies and coverage. I was updating an older platform I'd been working on.

What I ended up having to do was:

 Manually `Delete node_modules`

 Manually `Delete package-lock.json`

 `sudo npm install node-sass --unsafe-perm=true --allow-root`

 `sudo npm install node-sass-middleware --unsafe-perm=true --allow-root`



[Pre-built binaries not found for grpc@1.10.1 and node@10.9.0](https://stackoverflow.com/questions/52164351/pre-built-binaries-not-found-for-grpc1-10-1-and-node10-9-0)

[Error: EACCES: permission denied when trying to install ESLint using npm](https://stackoverflow.com/questions/35954725/error-eacces-permission-denied-when-trying-to-install-eslint-using-npm)


------------
    
    
## Answer \#11

 Vote: 7

Created at 2022-02-10 19:31:43

------------

I'm on a Windows 11 machine, and to fix it I downloaded python27 myself, using this link: [https://www.python.org/download/releases/2.7/](https://www.python.org/download/releases/2.7/).
And after installed I ran this command:
```
npm config set python c:\python27\python.exe
```



------------
    
    
## Answer \#12

 Vote: 6

Created at 2020-10-03 13:50:59

------------


- - - `c:\python27`- [link](https://geek-university.com/python/add-python-to-the-windows-path/)- 


------------
    
    
## Answer \#13

 Vote: 6

Created at 2022-03-15 02:54:15

------------

Worked for me in 2022, actually we can install python 2.7 through `npm`
After the installation for `python 2.7`, we can force to run `npm install` with using `python 2.7`
Sass

1. npm install --global windows-build-tools --save
2. npm install --python=python2.7




------------
    
    
## Answer \#14

 Vote: 5

Created at 2019-08-19 13:54:16

------------

This is 2 years old, but none of them helped me.

I uninstalled my NodeJS v12.8.1 (Current) and installed a brand new v10.16.3 (LTS) and my `ng build --prod` worked.


------------
    
    
## Answer \#15

 Vote: 5

Created at 2021-02-01 15:01:58

------------

I had `node 15.x.x` , and `"node-sass": "^4.11.0"`.
I saw in the release notes from `node-sass` and saw the node higest version compatible with node-sass 4.11.0 was 11, so I uninstalled node and reinstall 11.15.0 version (I'm working with Windows).
Check [node-sass releases](https://github.com/sass/node-sass/releases).
[(this is what you should see in the node-sass releases.)](https://i.stack.imgur.com/PRuoC.png)
Hope that helps and sorry for my english :)


------------
    
    
## Answer \#16

 Vote: 4

Created at 2021-05-21 14:53:14

------------

Here is what helped me to prevent the error `Error: not found: python2` when running a project based on the old `node-gyp v3.8.0`.

1. Run npm install --global --production windows-build-tools@4.0.0
2. Rename python.exe to python2.exe in %USERPROFILE%\.windows-build-tools\python27
3. Make sure that %USERPROFILE%\.windows-build-tools\python27 is part of your PATH environment variable (this is what windows-build-tools should already do for you)
4. Download Visual Studio 2017 Build Tools
5. Tell npm to use VS 2017 with npm config set msvs_version 2017
6. Run the script which uses node-gyp (node-gyp will now find python2.exe)



## Alternative: Force upgrade of node-gyp


You can also try to upgrade your project to use [node-gyp v8.0.0](https://www.npmjs.com/package/node-gyp/v/8.0.0) instead of [node-gyp v3.8.0](https://www.npmjs.com/package/node-gyp/v/3.8.0). In this case, add the following `resolution` entry to your `package.json`:
```
"resolutions": {
    "node-gyp": "8.0.0"
  }
```

Afterwards, make sure that you have  installed and  and/or . You can also run `npm config delete msvs_version` to let `node-gyp` automatically detect your VS version.
Tested with:
- - - - 


------------
    
    
## Answer \#17

 Vote: 3

Created at 2018-06-05 10:54:08

------------

had the same issue lost hours trying to install different version of python on my PC. Simply Upgrade node to the latest version v8.11.2 and npm 5.6.0, then after install node-sass@4.5.3 and you'll be fine.


------------
    
    
## Answer \#18

 Vote: 3

Created at 2021-08-06 14:56:45

------------

We encountered this problem in 2021 when setting up a new TeamCity build agent. Originally we had installed nodejs using Chocolatey. We reinstalled going back to the original installer `node-v12.12.0-x64.msi`. The third screen in the installer dialog offers to install the tools necessary to compile native modules:
[](https://i.stack.imgur.com/Chj1L.png)
After doing this our problem was gone.


------------
    
    
## Answer \#19

 Vote: 2

Created at 2019-07-08 08:49:29

------------

After looking at all the answers, i notice this solution might be very helpful. It explains [how to configure "npm" to find your installed python version](https://stackoverflow.com/a/56931250/10849438) while installing node-sass. Remember, node-sass requires node-gyp (an npm build-tool) which looks for your python path. Or just install python, and follow the same solution given (in the link). Thanks.


------------
    
    
## Answer \#20

 Vote: 2

Created at 2021-04-05 11:05:05

------------

In case of yarn projects, ignoring the node version with "yarn install --ignore-engines" will install any node-sass v. without these python errors


------------
    
    
## Answer \#21

 Vote: 2

Created at 2021-05-04 05:19:10

------------

Python2 maintenance status is  so it not installed by default on new ubuntu releases.
Installing the latest python2.7 worked fine for me.
Here is the instructions to install it:
- [https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tgz](https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tgz)- 
```
tar -xf Python-2.7.18.tgz
cd Python-2.7.18
./configure --enable-optimizations
sudo make altinstall
```

Open a new terminal and try agin the `npm install`


------------
    
    
## Answer \#22

 Vote: 1

Created at 2020-10-20 07:42:51

------------

I found the same issue with  and  on Windows 10. I fixed the problem by installing latest stable python 64-bit with adding the path to Environment Variables during python installation. After python installation, I restarted my machine for env vars.


------------
    
    
## Answer \#23

 Vote: 1

Created at 2022-02-09 06:17:42

------------

In my case, i had node version `v16` installed in the machine, but the react project used node-sass version `v4.x.x`, Updating the package.json with node-sass version to `v6.0.1` solved the issue, 
node version support for node-sass can be checked from official npm page: [https://www.npmjs.com/package/node-sass](https://www.npmjs.com/package/node-sass)


------------
    
    
## Answer \#24

 Vote: 1

Created at 2022-07-03 05:14:39

------------

A simple solution for windows 11 machines is,
- - - - - 


------------
    
    
## Answer \#25

 Vote: 0

Created at 2021-05-27 20:21:06

------------

also you can try to use sass module instead of node-sass:
```
npm install sass
```



------------
    
    
## Answer \#26

 Vote: 0

Created at 2021-07-20 17:21:01

------------


```
npm cache clean --force
npm config set msvs_version 2019 --global
npm install node-sass
```



------------
    
    
## Answer \#27

 Vote: 0

Created at 2021-07-29 06:24:34

------------

This same issue I faced few days ago. Believe on me their is no need to install or uninstall other dependencies in your project or system.
How had I resolve this issue let me share my experience.
- - - - 

Please check your package.json file if  element is exist inside the  Object then you need to run npm command in this way `npm install --no-optional` or also If just remove  element from  Object

[enter image description here](https://i.stack.imgur.com/OMfWn.png)
Please Check out this screenshot of package.json file


------------
    
    
## Answer \#28

 Vote: 0

Created at 2022-01-12 12:09:38

------------

Downgrading to node 10.24.1 version with npm 6.14.12 worked for me.
Along with installing windows-build-tools using
> npm i -g windows-build-tools


------------
    
    
## Answer \#29

 Vote: 0

Created at 2022-02-01 19:48:18

------------

I am using Windows 11 and I had tried using `npm i -g windows-build-tools` but it stuck forever. So, what worked for me was:

1. Install Visual Studio Build tools 2017 from here.
2. After installing, open Powershell as admin.
3. Enter this command: npm install --global --production windows-build-tools --vs2015
4. Thank me later!


It fixed all errors related to build tools for me on Windows 11.


------------
    
    
## Answer \#30

 Vote: 0

Created at 2022-02-08 17:59:38

------------

`npm i npm -g` will work if node version is updated. I ran into this issue, and `npm install -g --production windows-build-tools` worked as interim solution. But upgrading Node.js, NPM, and associated package versions is a better long term option.


------------
    
    
## Answer \#31

 Vote: 0

Created at 2022-03-06 07:47:02

------------

My OS is Win 10 x64.
The similar issue found while doing npm install.

1. Check that the node version suits the node-sass version.(Refer to the above tables.) Upgrade or downgrade node version, if needed
2. Delete node_modules folder $ npm install node-sass@ $ npm install


Well done.


------------
    
    
## Answer \#32

 Vote: 0

Created at 2022-08-19 17:13:42

------------

None of the above answers solved my problem, it was way simpler than i tought.
Simply downgrade your node version to match the node-sass version:
```
nvm list
```

```
nvm user <node-version>
```



------------
    
    
## Answer \#33

 Vote: 0

Created at 2023-01-11 16:57:44

------------

Got this error on Windows 11 with latest Node.js LTS Version: 18.13.0 (includes npm 8.19.3)
[https://nodejs.org/en/download/](https://nodejs.org/en/download/)
Fixed by first downloading `Python 2.7.18` manually since only `Python 3.11` was installed.
[https://www.python.org/downloads/windows/](https://www.python.org/downloads/windows/)
[https://www.python.org/downloads/release/python-2718/](https://www.python.org/downloads/release/python-2718/)
After install I ran the command from @bvdb:
```
npm config set python "C:\Python27\python.exe"
```

Source:
[https://stackoverflow.com/a/64486670/3850405](https://stackoverflow.com/a/64486670/3850405)
This fixed the error


------------
    
    
## Answer \#34

 Vote: 0

Created at 2023-02-16 14:58:42

------------

Stuck through the same issue but nothing seemed to be working with me, unless I tried installing node-sass again on my global node modules config
```
npm install -g node-sass@6
```

This worked like a charm


------------
    
    
## Answer \#35

 Vote: 0

Created at 2023-02-23 06:13:51

------------

node-sass deprecated now. we need to use sass instead of node-sass.
as a solution to this question, I occurred the same error. So I install the python2. node gyp finds the python2.


------------
    
    