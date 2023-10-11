
# Post \#50522376 [Link](https://stackoverflow.com/questions/50522376/)

## npm install hangs on loadIdealTree:loadAllDepsIntoIdealTree: sill install loadIdealTree

**Vote**: 72 (90/702) **Views**: 146321 (52/702) 

**Internal ID** \#0-0-19

Created at 2018-05-25 05:53:13

Tags: `node.js` `npm`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have a Node.js application. When I try to run `npm install` it hangs with this:

```
loadIdealTree:loadAllDepsIntoIdealTree: sill install loadIdealTree
```


`npm install --verbose` gives me a little extra info:

```
npm info it worked if it ends with ok
npm verb cli [ '/usr/local/bin/node',
npm verb cli   '/usr/local/bin/npm',
npm verb cli   'install',
npm verb cli   '--verbose',
npm verb cli   'aws-sdk-js' ]
npm info using npm@5.8.0
npm info using node@v8.9.2
npm verb npm-session ea38310110279de7
npm http fetch GET 404 https://registry.npmjs.org/aws-sdk-js 2211ms
npm verb stack Error: 404 Not Found: aws-sdk-js@latest
npm verb stack     at fetch.then.res (/usr/local/lib/node_modules/npm/node_modules/pacote/lib/fetchers/registry/fetch.js:42:19)
npm verb stack     at tryCatcher (/usr/local/lib/node_modules/npm/node_modules/bluebird/js/release/util.js:16:23)
npm verb stack     at Promise._settlePromiseFromHandler (/usr/local/lib/node_modules/npm/node_modules/bluebird/js/release/promise.js:512:31)
npm verb stack     at Promise._settlePromise (/usr/local/lib/node_modules/npm/node_modules/bluebird/js/release/promise.js:569:18)
npm verb stack     at Promise._settlePromise0 (/usr/local/lib/node_modules/npm/node_modules/bluebird/js/release/promise.js:614:10)
npm verb stack     at Promise._settlePromises (/usr/local/lib/node_modules/npm/node_modules/bluebird/js/release/promise.js:693:18)
npm verb stack     at Async._drainQueue (/usr/local/lib/node_modules/npm/node_modules/bluebird/js/release/async.js:133:16)
npm verb stack     at Async._drainQueues (/usr/local/lib/node_modules/npm/node_modules/bluebird/js/release/async.js:143:10)
npm verb stack     at Immediate.Async.drainQueues (/usr/local/lib/node_modules/npm/node_modules/bluebird/js/release/async.js:17:14)
npm verb stack     at runCallback (timers.js:789:20)
npm verb stack     at tryOnImmediate (timers.js:751:5)
npm verb stack     at processImmediate [as _immediateCallback] (timers.js:722:5)
npm verb cwd /Users/me/git/aws-sdk-js-perf
npm verb Darwin 17.5.0
npm verb argv "/usr/local/bin/node" "/usr/local/bin/npm" "install" "--verbose" "aws-sdk-js"
npm verb node v8.9.2
npm verb npm  v5.8.0
npm ERR! code E404
npm ERR! 404 Not Found: aws-sdk-js@latest
npm verb exit [ 1, true ]

npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/me/.npm/_logs/2018-05-24T10_30_55_688Z-debug.log
```


I came across instances where other people experienced this but the below seemed to resolve their issue. It doesn't fix mine:

```
npm set registry http://registry.npmjs.org/
```


Does anybody know what might be wrong?


----------
        
## Answer \#0

**Accepted** Vote: 90

Created at 2018-06-09 03:50:40

------------

Had the same issue, and this comment [here](https://github.com/npm/npm/issues/17228#issuecomment-318581491) fixed it for me:
> Try to remove 'package-lock.json' file from directory where 'package.json' locate.


------------
    
    
## Answer \#1

 Vote: 29

Created at 2020-09-27 10:05:23

------------

Simply follow the following given steps:

1. npm config set registry http://registry.npmjs.org/ --global
2. npm cache clear --force
3. Delete your package-lock.json file(it will automatically get created after npm i).
4. Now try for npm install


This worked for me.


------------
    
    
## Answer \#2

 Vote: 19

Created at 2019-11-06 08:08:28

------------

This problem happens also when you have a very slow connection


------------
    
    
## Answer \#3

 Vote: 11

Created at 2020-06-03 08:34:18

------------

I also faced the same issue. In my case `git://` port was blocked and `npm install` took too long to time out. (longer than normal it waits before concluding that the process hung.)

A solution was to auto-rewrite all `git://` URLs to `https://`:

Run the following command in your terminal

```
git config --global url."https://".insteadOf git://

npm install
```


Hope this helps!!


------------
    
    
## Answer \#4

 Vote: 9

Created at 2019-04-17 12:47:41

------------

Even after removing , `npm i` didn't worked for us. Instead, it got stuck on another message - `still removeObsoleteDep removing fs-extra from the tree as its been replaced by a newer version`.

Problem was, we used  occasionally. Installing using `yarn` will resolve the issue if it is the case.


------------
    
    
## Answer \#5

 Vote: 8

Created at 2020-11-26 06:48:35

------------

In my case, changing VPN worked.


------------
    
    
## Answer \#6

 Vote: 7

Created at 2020-10-27 01:29:36

------------

I had this problem and the issue was that my VPN connection dropped and some of the packages were in my companies private package store.


------------
    
    
## Answer \#7

 Vote: 3

Created at 2019-05-23 12:01:28

------------

You might not have `package-lock.json` so instead of this file you need to remove the `npm-shrinkwrap.json` file and try to execute `npm install` again.

About npm-shrinkwrap.json: 

> npm-shrinkwrap.json is a file created by npm-shrinkwrap. It is
  identical to package-lock.json , with one major caveat: Unlike
  package-lock.json , npm-shrinkwrap.json may be included when
  publishing a package.


------------
    
    
## Answer \#8

 Vote: 3

Created at 2020-10-23 05:12:30

------------

In my case package-lock.json file didn't exists. I tried with my mobile hotspot and it worked. Might be some issue from wifi end. But my internet was working fine on wifi. Just issue was it got stuck on
> loadIdealTree:loadAllDepsIntoIdealTree: sill install loadIdealTree
Moving on different network can also resolve your issue. I know it is very weird. Hope this will help too. To someone in future.


------------
    
    
## Answer \#9

 Vote: 3

Created at 2022-01-27 02:25:47

------------

For me the issue went away as soon as I disconnect from my company's VPN.


------------
    
    
## Answer \#10

 Vote: 3

Created at 2022-11-18 18:02:54

------------

I have tried all the solutions above without success.
But I found this one : [https://github.com/npm/cli/issues/4163#issuecomment-1010037450](https://github.com/npm/cli/issues/4163#issuecomment-1010037450)
```
alias npm="node --dns-result-order=ipv4first $(which npm)"
```

Then npm work correctly


------------
    
    
## Answer \#11

 Vote: 2

Created at 2019-03-05 15:47:43

------------

I experienced this, and had removed my package-lock.json and found out that a resource wasn't available on the network I was on.


------------
    
    
## Answer \#12

 Vote: 2

Created at 2019-05-17 12:14:11

------------

I had same problem. 

When I tried yarn, I got this message:

> [1/4] Resolving packages... info There appears to be trouble with your
  network connection. Retrying...

One of the packages was not available actually!


------------
    
    
## Answer \#13

 Vote: 2

Created at 2021-05-02 12:22:33

------------

In my case, I check the config about registry again.
`npm get registry`
And check this IP is working.

---


cuz' I find that I was setting something else like [http://10.1.1.1](http://10.1.1.1)
--- the self-registry like Verdaccio who's proxy here, but it's shutdown.
and I set again `npm set registry https://registry.npmjs.org`,
then it's work.


------------
    
    
## Answer \#14

 Vote: 2

Created at 2021-08-14 20:45:54

------------

The only thing that helped me is commenting out the `proxy` & `https-proxy` lines at `.npmrc` file.
I don't even know how and when those two lines were set. Don't remember myself doing it.


------------
    
    
## Answer \#15

 Vote: 2

Created at 2022-05-02 11:37:33

------------

This was my issue.  I forget that I had previously setup an internal proxy for work to get Node modules.    To check whether you are behind a proxy and see your proxy settings do:
```
npm config list
```

Mine output was:
`https-proxy = "http://9.116.33.175/max*emphasized text*"`
To clear it do:
```
npm config delete proxy
npm config delete http-proxy
npm config delete https-proxy
```



------------
    
    
## Answer \#16

 Vote: 1

Created at 2020-04-29 08:01:06

------------

I had faced this issue too. Cause : proxy was not set.

Use these commands to set your proxy:
npm config set proxy [http://username:password@host:port](http://username:password@host:port) npm config set https-proxy [http://username:password@host:port](http://username:password@host:port).
proxy=[http://username:password@host:port](http://username:password@host:port) https-proxy=[http://username:password@host:port](http://username:password@host:port) https_proxy=[http://username:password@host:port](http://username:password@host:port)

Post this, npm install went through with no issues


------------
    
    
## Answer \#17

 Vote: 1

Created at 2020-07-31 17:00:08

------------

This is my process:

1. npm install --verbose => Understand better what got hung. Got stuck at loadAllDepsIntoIdealTree
2. Removed package-lock.json from the same location as package.json => got stuck at FetchMetadata
3. Do ssh-add, add in my key like this suggestion => SUCCESS




------------
    
    
## Answer \#18

 Vote: 1

Created at 2020-08-19 14:18:58

------------

I am able to solve it in 2 ways

1. Delete 'package-lock.json'
2. Remove proxy settings (http_proxy / https_proxy) from environment variables




------------
    
    
## Answer \#19

 Vote: 0

Created at 2021-11-10 14:58:31

------------

Had the same issue. For me it was caused by a [webpack](https://webpack.js.org/) watcher. Stopping the watcher instantly advances the process.


------------
    
    
## Answer \#20

 Vote: 0

Created at 2021-12-09 09:59:39

------------

if you still have trouble with this for windows users especially. U can start with re-install your npm in C:/yourfolder .


------------
    
    
## Answer \#21

 Vote: 0

Created at 2021-12-21 09:31:50

------------

I've tried removing the `package-lock.json` file but that did not work.
In the end updating node on my Mac to the latest version fixed the issue.


------------
    
    
## Answer \#22

 Vote: 0

Created at 2022-01-14 10:28:23

------------

I use my own npm repo for some modules, and when it was down, the `npm i` just hangs.


------------
    
    
## Answer \#23

 Vote: 0

Created at 2022-01-18 09:40:26

------------

In my case i had corrputed my npm config while trying a few other things.
i had added a cafile property
removed it using
```
npm config edit
```



------------
    
    
## Answer \#24

 Vote: 0

Created at 2022-02-02 20:08:11

------------

So for my case it was a bugy latest version of node with npm.
I rocomande you to always check if you are on a LTS (Long Time Support) version since this ones are daily maintained and stable version.
I was using node without nvm so my first step was to remove it (purge it) it's a simple topic and you can get lot of solution on google depending on your OS.
Then I had to install mvn, you can also use it tutorial of install who uses Curl: `sudo chmod +x myInstall.sh`
Once nvm installed you may get an issue where nvm is enable to find the remote repository, dont worry all you have to do is to add a file containing this line :
```
sudo nano ~/.curlrc    # use this or vim to create the source file 

-k   # put this as content and save
```

Now your nvm should be fine
To install a stable version use the following commande to search repos for LTS versions
```
nvm ls-remote | grep LTS
```

Choose any version you want for my case for example
```
nvm install v16.13.0
```

Now you will see that node got installed with a corresponding version of npm (I dont recommande updating npm in this case to avoid latest version issues )
Go ahaid and do your `npm install xxxx@xxxx` this should work just fine
Dont forget to read your project requirement and choose corresponding node and npm version.
nvm can switch current version of node and npm, go ahaid and read it documentation using  `nvm --help`


------------
    
    
## Answer \#25

 Vote: 0

Created at 2022-03-03 05:12:31

------------

Just execute the following code in your node js cmd:

1. git config --global http.sslVerify false
2. npm install


This worked for me.


------------
    
    
## Answer \#26

 Vote: 0

Created at 2022-07-18 07:55:26

------------

Faced a similar issue and in my case, I did upgrade the node version which fixed the issue also sometimes we need to try with or without a VPN. Please downgrade the node version if require and if that is compatible with your packages.


------------
    
    
## Answer \#27

 Vote: 0

Created at 2022-08-23 15:19:54

------------

For me (MacOs 12.5 m1 machine) it was the ssh-agent issue. This [comment](https://github.com/npm/npm/issues/17228#issuecomment-333857122) fixed the issue for me.
Run `ssh-add` and enter passphrase if any to add the current ssh-key to the ssh-agent. You can verify this using `ssh-add -l`
You can add the ssh-agent permanently to the keychain. To the `.ssh/config` file, add the following lines:
```
Host *
  UseKeychain yes
  AddKeysToAgent yes
  IdentityFile ~/.ssh/id_rsa
```



------------
    
    
## Answer \#28

 Vote: 0

Created at 2022-08-26 12:41:31

------------

NetBSD 9.3 solution - hardcode the `registry.npmjs.org` IP due to IPV6 problems.
edit: [source](https://github.com/npm/cli/issues/4237#issuecomment-1212141446) - thanks khorben!
There's  weird going on with IPV6, what  I don't know, but a quick fix is to insert `registry.npmjs.org` into your `/etc/hosts`.
Get the IP:
```
ping registry.npmjs.org
```

Copy the IP (should be IPV4).
Open up `/etc/hosts`, with `su` powers, and insert the following line:
```
<PASTE THE IP HERE> registry.npmjs.org
```

So it turns into something like this on the last line:
```
104.16.26.35 registry.npmjs.org
```

Save the file.
Now `npm` should work.


------------
    
    
## Answer \#29

 Vote: 0

Created at 2022-09-16 01:22:33

------------


mine was
```
idealTree:npm: sill idealTree buildDeps
```

[Download yarn here manually](https://github.com/yarnpkg/yarn/releases). If u r on windows download the .msi one, so ez


------------
    
    
## Answer \#30

 Vote: 0

Created at 2022-09-24 23:13:44

------------

run:
```
npm cache clear --force
npm config set registry https://registry.npmmirror.com
```

[Source : https://github.com/npm/cli/issues/4876#issuecomment-1174884230](https://github.com/npm/cli/issues/4876#issuecomment-1174884230)


------------
    
    
## Answer \#31

 Vote: 0

Created at 2022-10-13 13:19:25

------------

In my case - I was using the wrong node version (not the `lts` one), so I just used the [node version manager](https://github.com/nvm-sh/nvm), and installed the latest `lts` version:
```
nvm install --lts
```

Hope it helps ✌️


------------
    
    
## Answer \#32

 Vote: 0

Created at 2022-10-25 22:43:27

------------

`npm install` was hanging for me when Windows ransomware protection was blocking folder access to `node.exe`.
This can be checked in Windows Security:
Virus & threat protection > Ransomware protection > Allow an app through controlled folder access > Add an allowed app > Recently blocked apps


------------
    
    
## Answer \#33

 Vote: 0

Created at 2022-11-02 15:11:22

------------

Yet another solution!
I realized ip6tables was dropping ICMPv6 packets on `npm ping`. I allowed ICMPv6 packets through and it solved it.
Well, not exactly...
- - `ip6tables -P INPUT ACCEPT``npm ping``npm i`- `ip6tables -P INPUT DROP``ACCEPT`- 
So I can't exactly explain what happened. Check if your firewall drops any packets on an npm command, and take it from there.


------------
    
    
## Answer \#34

 Vote: -2

Created at 2022-08-07 03:28:51

------------

sometimes your internet speed  is slow that's why it take sometime to download the required files. just wi


------------
    
    