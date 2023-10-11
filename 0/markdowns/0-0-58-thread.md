
# Post \#47545940 [Link](https://stackoverflow.com/questions/47545940/)

## When I run `npm install`, it returns with `ERR! code EINTEGRITY` (npm 5.3.0)

**Vote**: 236 (12/702) **Views**: 295889 (22/702) 

**Internal ID** \#0-0-58

Created at 2017-11-29 05:50:45

Tags: `node.js` `ubuntu` `npm`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am getting this error while running `sudo npm install`. On my server, npm was installed earlier. I've tried to delete the `package-lock.json` file, and ran `npm cache clean --force`, but it didn't work. 

My npm version is 5.3.0.

The error:

```
npm ERR! code EINTEGRITY
npm ERR! sha512-MKiLiV+I1AA596t9w1sQJ8jkiSr5+ZKi0WKrYGUn6d1Fx+Ij4tIj+m2WMQSGczs5jZVxV339chE8iwk6F64wjA== integrity checksum failed when using sha512: wanted sha512-MKiLiV+I1AA596t9w1sQJ8jkiSr5+ZKi0WKrYGUn6d1Fx+Ij4tIj+m2WMQSGczs5jZVxV339chE8iwk6F64wjA== but got sha512-WXI95kpJrxw4Nnx8vVI90PuUhrQjnNgghBl5tn54rUNKZYbxv+4ACxUzPVpJEtWxKmeDwnQrzjc0C2bYmRJVKg==. (65117 bytes)

npm ERR! A complete log of this run can be found in:
npm ERR!     /home/ubuntu/.npm/_logs/2017-11-29T05_33_52_182Z-debug.log
```



----------
        
## Answer \#0

**Accepted** Vote: 36

Created at 2017-12-01 05:24:50

------------

Actually the above is related to the network connectivity in side the server. When I've good connectivity in the server, the npm install gone good and didn't throw any error


------------
    
    
## Answer \#1

 Vote: 362

Created at 2017-12-21 09:42:06

------------

See [https://github.com/npm/npm/issues/16861](https://github.com/npm/npm/issues/16861)

`npm cache verify`
Then I re-ran:
`npm install -g create-react-app`
And it installed as expected: Issue resolved.

---


[GitHub issue](https://github.com/npm/npm/issues/16861)
`npm cache clean --force`
OR
Deleting npm and npm-cache folders in `Users%username%\AppData\Roaming` (Windows 7 and Windows 10) and running `npm install`
OR
Update npm by via `npm i -g npm`
OR
Delete `package-lock.json`
OR
`npm cache clean`
OR
Do these steps to fix the problem:

1. Find all outdated packages and update theme: npm outdated -g sudo npm i -g outDatedPKG
2. Upgrade npm to latest version with: sudo npm i -g npm
3. Delete package-lock.json file.
4. Delete _cacache directory in ~/.npm: npm cache verify
5. Every time I get that error, do steps 2 & 3.
6. If you still get the error, clear npm's cache: npm cache clean --force


OR

1. Add proxy to .npmrc in ~ directory:


`proxy=http://localhost:8123`
`https-proxy=http://localhost:8123`

1. Try again! slow internet connection and censorship may cause this ugly problem.


OR
`npm cache clear --force && npm install --no-shrinkwrap --update-binary`
OR
`npm config set package-lock false`


------------
    
    
## Answer \#2

 Vote: 76

Created at 2018-02-15 12:02:40

------------

Delete package-lock.json file and then try to install 


------------
    
    
## Answer \#3

 Vote: 43

Created at 2018-03-01 14:19:22

------------

The issue was indeed in `package-lock.json`, and after replacing it with a working version from another branch it worked.
What's interesting is seeing the diff:
[](https://i.stack.imgur.com/l0UuL.png)
So there really is some integrity checksum in the `package-lock.json` and it was replaced in our `package-lock.json` with a SHA1 instead of a SHA-512 checksum. See [here](https://stackoverflow.com/a/47834854/492336) for more info.
In case you . Consider the message
```
npm ERR! code EINTEGRITY
npm ERR!
  sha512-MKiLiV+I1AA596t9w1sQJ8jkiSr5+ZKi0WKrYGUn6d1Fx+Ij4tIj+m2WMQSGczs5jZVxV339chE8iwk6F64wjA==
integrity checksum failed when using sha512: wanted
  sha512-MKiLiV+I1AA596t9w1sQJ8jkiSr5+ZKi0WKrYGUn6d1Fx+Ij4tIj+m2WMQSGczs5jZVxV339chE8iwk6F64wjA==
but got
  sha512-WXI95kpJrxw4Nnx8vVI90PuUhrQjnNgghBl5tn54rUNKZYbxv+4ACxUzPVpJEtWxKmeDwnQrzjc0C2bYmRJVKg==
. (65117 bytes)
```

Find the package in `package-lock.json` using the first checksum:
```
sha512-MKiLiV+I1AA596t9w1sQJ8jkiSr5+ZKi0WKrYGUn6d1Fx+Ij4tIj+m2WMQSGczs5jZVxV339chE8iwk6F64wjA==
```

and put the third checksum into its "integrity" field:
```
sha512-WXI95kpJrxw4Nnx8vVI90PuUhrQjnNgghBl5tn54rUNKZYbxv+4ACxUzPVpJEtWxKmeDwnQrzjc0C2bYmRJVKg==
```

A more detailed description is [here](https://github.com/npm/cli/issues/473#issuecomment-635677353).


------------
    
    
## Answer \#4

 Vote: 18

Created at 2018-09-16 08:45:59

------------




1. Bad package-lock.json file
2. The existance of npm-shrinkwrap.json together with the package-lock.json file






1. Deleted the package-lock.json file
2. Delete the npm-shrinkwrap.json file
3. Ran npm install again (which recreated a good package-lock file)



Fixed my error!


------------
    
    
## Answer \#5

 Vote: 10

Created at 2018-11-16 19:36:55

------------

I was stuck at this for a long time and this is what helped me. 

Try this:

```
npm cache clean --force
npm install --update-binary --no-shrinkwrap
```


Found this answer after digging into GitHub issues!!


------------
    
    
## Answer \#6

 Vote: 6

Created at 2018-09-10 18:26:35

------------

As a workaround, follow the below steps:


1. Go to the project directory
2. Remove the node_modules directory: rm -rf node_modules
3. Remove package-lock.json file: rm package-lock.json
4. Clear the cache: npm cache clean --force
5. Run npm install --verbose If after following the above steps still the issue exists then please provide us the output of installation command with --verbose.




------------
    
    
## Answer \#7

 Vote: 4

Created at 2018-04-26 07:58:42

------------

There are several valid and helpful responses here, but I would like to add that in my case the simplest solution was: 


1. Delete package-lock.json;
2. Remove folder AppData\Local\npm\cache or AppData\Roaming\npm\cache;
3. Remove folder node_modules.staging;
4. Run npm install again.



After that everything ran smoothly.


------------
    
    
## Answer \#8

 Vote: 3

Created at 2018-05-29 13:58:23

------------

I had a very similar problem, and in my case it worked by doing:

```
npm clean
```


This is the  option since it clears every package from the cache as expained [here](https://www.ludovf.net/blog/npm-cache-error/).


------------
    
    
## Answer \#9

 Vote: 3

Created at 2018-08-04 22:03:09

------------

Try the following:

```
npm cache clean --force
```


This has worked for me.


------------
    
    
## Answer \#10

 Vote: 3

Created at 2020-09-06 12:30:15

------------

This Worked for me .
open the project in CMD
the run
```
npm cache verify 
npm install 
npm start
```



------------
    
    
## Answer \#11

 Vote: 2

Created at 2018-10-08 05:47:39

------------

I was using private npm registry and trying to install private npm module. Logging to npm local registry fixed it (used command `npm --add-user`)


------------
    
    
## Answer \#12

 Vote: 1

Created at 2018-02-23 22:32:33

------------

[SherylHohman's answer](https://stackoverflow.com/a/47922056/5411817) solved the issue I had, but only after I switched my internet connection. Intitially, I was on the hard-line connection at work, and I switched to the WiFi connection at work, but that still didn't work. 

As a last resort, I switched my WiFi to a pocket-WiFi, and running the following worked well:

```
npm cache verify

npm install -g create-react-app

create-react-app app-name
```


Hope this helps others.


------------
    
    
## Answer \#13

 Vote: 1

Created at 2018-09-07 07:22:40

------------

I am behind my organization's proxy, running the following commands fixed the issue for me

```
npm config set proxy http://proxy.yourproxydomain.com:port
npm config set https-proxy http://proxy.yourproxydomain.com:port
npm config set strict-ssl false
npm config set registry https://registry.npmjs.org/
```



------------
    
    
## Answer \#14

 Vote: 1

Created at 2020-01-31 08:01:26

------------

Updating `.npmrc` and the registry to `https://` worked for me

```
registry=https://registry.npmjs.org/
```



------------
    
    
## Answer \#15

 Vote: 1

Created at 2020-02-25 13:30:24

------------

This was not yet mentioned but make sure that your SYSTEM TIME is correct. If it is too out of sync it will cause a EINTEGRITY error. When you are doing npm publish / install.


------------
    
    
## Answer \#16

 Vote: 1

Created at 2020-06-26 14:00:10

------------

None of the above answers worked for me. The solution to my issue was to change the way the snapshot dependency was consumed inside the package.json. Use the following template to pull in the snapshot dependency that you need
```
"dependency": "git+http://github.com/[pathtoproject].git#[branchname]",
```



------------
    
    
## Answer \#17

 Vote: 1

Created at 2020-10-30 18:27:58

------------

I had the same issue. I used yarn instead of npm to install the dependencies and it worked.
```
yarn add *****
```



------------
    
    
## Answer \#18

 Vote: 1

Created at 2020-12-02 08:33:10

------------

After going through all the answers and executing most of them. Although I resisted to try the Restart magic, eventually, the issue is solved after restart on my macbook(MacOS Catalina Ver. 10.15.7).
It seems like a cache issue indeed but none of the commands that I have executed cleared the cache.


------------
    
    
## Answer \#19

 Vote: 1

Created at 2021-01-10 10:54:36

------------

What worked for me was `npm cache verify` then re-run your command. All should be good.


------------
    
    
## Answer \#20

 Vote: 0

Created at 2018-02-13 16:35:57

------------

Updating the `.npmrc` files' entries for scoped and un-scoped packages worked for me.
So I ended up using the

`npm config set @scope_name:registry SCOPED_REGISTRY_URL`

and

`npm config set registry PUBLIC_REGISTRY_URL`


------------
    
    
## Answer \#21

 Vote: 0

Created at 2020-03-11 11:38:57

------------

You should check Connection-specific DNS Suffix when type “ipconfig” or “ifconfig” in terminal


------------
    
    
## Answer \#22

 Vote: 0

Created at 2020-04-08 07:18:17

------------

I faced same issue. Plus while I was trying to resolve from picking solutions from other devs, I faced few more issues like one listed here.

[Angular 9 ng new myapp gives error The Schematic workflow failed](https://stackoverflow.com/questions/60185874/angular-9-ng-new-myapp-gives-error-the-schematic-workflow-failed)

[https://medium.com/@codewin/npm-warn-deprecated-request-2-88-2-b6da20766fd7](https://medium.com/@codewin/npm-warn-deprecated-request-2-88-2-b6da20766fd7)

Finally after trying cache clean and verify and reinstall node of different versions and npm update, nvm and many other solution like set proxy and better internet connection, I still could not arrive to a resolve.

What worked for me is : I browsed a bit inside my C:\Users--- folder, I found package-lock.json and .npmrc files. I deleted those and reinstalled angular and tried. npm install and uninstall of different modules started working.

[](https://i.stack.imgur.com/Hn52g.png) 


------------
    
    
## Answer \#23

 Vote: 0

Created at 2020-04-30 04:00:47

------------

Before i was running this command

```
npm install typescript -g
```


after changing the command it worked perfectly.

```
npm install -g typescript
```



------------
    
    
## Answer \#24

 Vote: 0

Created at 2020-05-15 06:12:07

------------

I faced this issue. It was my network connectivity. I changed network (from Broadband WiFi to 4G WiFi) and tried. It worked.

My broadband ISP was blocking all http requests. That might be the reason I guess in my case.


------------
    
    
## Answer \#25

 Vote: 0

Created at 2020-05-25 17:28:33

------------

all solutions failed for me till i checked router settings; it was set to only IPV4.. i changed and put ipv4v6 and all is working fine now.


------------
    
    
## Answer \#26

 Vote: 0

Created at 2021-01-20 21:48:33

------------

In my case the sha command was missing from my linux distro; steps were
- - - - 


------------
    
    
## Answer \#27

 Vote: 0

Created at 2021-02-26 10:14:54

------------

Easy and fast fix for me was to npm install the specific package on which it said the sha is wrong. Say your package is called `awesome-package`.
My solution was:
`npm i awesome-package`
This updated my sha within the package-lock.json.


------------
    
    
## Answer \#28

 Vote: 0

Created at 2021-06-01 06:59:34

------------

If none of the above solved your problem, then just upgrade your npm verion and try. It worked for me.


------------
    
    
## Answer \#29

 Vote: 0

Created at 2022-02-26 12:43:13

------------

Updating nodejs version worked for me.
curl -fsSL [https://deb.nodesource.com/setup_12.x](https://deb.nodesource.com/setup_12.x) | sudo -E bash -
sudo apt-get install -y nodejs


------------
    
    
## Answer \#30

 Vote: 0

Created at 2022-10-04 20:53:56

------------

The issue occurred for me during an upgrade to node 18 - my package-lock file was holding onto a stale resolution from a custom registry our team no longer used.
Unforutnately, removing package lock, clearing the cache and running npm install were not enough.
Deleting the stale resolutions in package-lock followed by npm install fix the issue for me.
Here I deleted the line beginning with :
```
// package-lock.json

     "node_modules/closest-file-data": {
       "version": "0.1.4",
-      "resolved": "https://foo.foobar.io/foo/api/npm/npm-dev/closest-file-data/-/closest-file-data-0.1.4.tgz"
,
       "integrity": "sha1-l1+HwTLymdJKA3W59jyj+4j3Kzo=",
```

Generally you want to avoid manual changes to this file but my machine kept insisting I use the cached package


------------
    
    
## Answer \#31

 Vote: -1

Created at 2018-06-08 09:52:55

------------

Run the commands below on your project..

```
npm rm -rf node_modules && npm rm package-lock.json && npm rm -rf ~/.npm && npm install --update-binary --no-shrinkwrap
```



------------
    
    
## Answer \#32

 Vote: -1

Created at 2018-10-15 13:28:18

------------

We had this problem multiple times in the company I work at. Deleting the `node_modules` folder from the .nvm folder fixed the problem:

```
rm -rf ~/.nvm/versions/node/v8.6.0/lib/node_modules
```



------------
    
    
## Answer \#33

 Vote: -1

Created at 2019-02-18 11:22:22

------------

Try this

```
Step-1) Delete package-lock.json from root folder.
  Step-2) Delete node_modules folder
  Step-3) run npm install command in root
```



------------
    
    
## Answer \#34

 Vote: -1

Created at 2022-01-12 19:28:13

------------

Make sure you have no VPN running


------------
    
    