
# Post \#63716587 [Link](https://stackoverflow.com/questions/63716587/)

## In WSL2: Ubuntu 20.04 for Windows 10 nodejs is installed but npm is not working

**Vote**: 16 (306/702) **Views**: 15576 (351/702) 

**Internal ID** \#0-0-40

Created at 2020-09-03 03:56:15

Tags: `node.js` `npm` `windows-10` `windows-subsystem-for-linux` `ubuntu-20.04`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am using  in my  operating system. I have installed `nodejs` using the command `sudo apt-get install -y nodejs` when I do `node -v` command I get `v12.18.3`

but when I do `npm -v` command I get this below command

I also do `whereis` command. Hope this will help to find solution.
 
I have tried almost all the stackoverflow solutions and github issues but nothing is worked for me.
Hope any kind soul has the solution to this problem. Thanks in advance.


----------
        
## Answer \#0

**Accepted** Vote: 33

Created at 2020-09-03 04:07:08

------------

Try this
```
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
sudo apt install npm
```



------------
    
    
## Answer \#1

 Vote: 7

Created at 2020-12-27 17:37:37

------------

Solution for following error:
`-bash: /mnt/c/Program Files/nodejs/npm: /bin/sh^M: bad interpreter: No such file or directory`
Edit `~/.bashrc`
Append at end of file:
```
# strip out problematic Windows %PATH%
PATH=$(echo "$PATH" | sed -e 's/:\/mnt.*//g')
```

Now `npm init` will work.


------------
    
    
## Answer \#2

 Vote: 7

Created at 2021-08-11 04:58:33

------------

A better way is configuring `/etc/wsl.conf` in your Windows User directory.
Adding this into the `/etc/wsl.conf`, so Windows Path will not take the precedence
```
[interop]
appendWindowsPath=false
```

For more config details check the Microsoft Dev Blog [here](https://devblogs.microsoft.com/commandline/automatically-configuring-wsl/).


------------
    
    
## Answer \#3

 Vote: 5

Created at 2022-06-09 17:18:04

------------

To install `nodejs` in WSL don't use `apt` follow Microsoft's guidance:
- [https://learn.microsoft.com/en-us/windows/dev-environment/javascript/nodejs-on-wsl](https://learn.microsoft.com/en-us/windows/dev-environment/javascript/nodejs-on-wsl)
See also how to remove `nodejs` if you installed it via `apt`:
- [https://askubuntu.com/questions/786015/how-to-remove-nodejs-from-ubuntu-16-04](https://askubuntu.com/questions/786015/how-to-remove-nodejs-from-ubuntu-16-04)
For `npm` to work under :
- `ipv6`[configure to prefer ipv4](https://askubuntu.com/a/1200257/368145)[hitting these issues](https://github.com/npm/cli/issues/3359)- [I disabled ipv6 on my main NIC](https://askubuntu.com/a/1013248/368145)`npm install`

---


:
 (e.g [with Cisco AnyConnect](https://github.com/microsoft/WSL/issues/4277)) - the fix works but may no longer be needed under AnyConnect (WSL2 on a VPN now works for me after a recent update @ end of July 2022)
I thought my WSL containers were running under WSL2 (I upgraded the WSL kernel with `wsl --update`) - while setting up Visual Studio with WSL I saw a WSL1 warning. You also have to upgrade containers:
```
wsl --set-version ubuntu-22.04 2
wsl --set-default-version 2
```

To get Visual Studio integration working properly with Ubuntu `22.04` in WSL you also currently have to upgrade `gzip` to install `VS Code Server for x64` in WSL (`code .:` in the Linux terminal):
```
wget http://ftp.debian.org/debian/pool/main/g/gzip/gzip_1.12-1_amd64.deb
sudo dpkg -i ./gzip_1.12-1_amd64.deb
```

Finally I upgraded `npm` & everything works (choose one of the following commands):
- `nvm install-latest-npm`- `npm install -g npm@latest`

If you use `nodejs` with Azure Active Directory there seems to be an issue with the `azure-cli` forgetting credentials under WSL1 / WSL2 & persistently telling you to `az login`. In this case you need to run your `local` node development instances on Windows.


------------
    
    
## Answer \#4

 Vote: 1

Created at 2020-09-18 18:14:52

------------

For all Unix/Linux/MacOS operating systems, I would always rather go with the "Node Version Manager". It normally works flawlessly on Linux and MacOS (and there's a Windows port for it as well) and enables a very simple way of installing `node` and `npm` correctly without the need of being `root`.
See here: [https://github.com/nvm-sh/nvm](https://github.com/nvm-sh/nvm)
I can confirm here on my machine that it also works on Ubuntu 20.04 on WSL2.


------------
    
    