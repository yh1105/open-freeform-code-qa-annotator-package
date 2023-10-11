
# Post \#61586021 [Link](https://stackoverflow.com/questions/61586021/)

## Is it impossible to install R 4.0 on Ubuntu 18.04.4 LTS because r-base-core requires a libc6 version >= 2.29?

**Vote**: 17 (290/702) **Views**: 14675 (362/702) 

**Internal ID** \#1-4-313

Created at 2020-05-04 06:01:35

Tags: `r` `linux`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `r`)

----------

**Notepad**


----------

I am trying to install R 4.0 on Ubuntu 18.04.4 LTS but I keep getting errors of 

```
> sudo apt install r-base-core
Reading package lists... Done
Building dependency tree
Reading state information... Done
Some packages could not be installed. This may mean that you have
requested an impossible situation or if you are using the unstable
distribution that some required packages have not yet been created
or been moved out of Incoming.
The following information may help to resolve the situation:

The following packages have unmet dependencies:
 r-base-core : Depends: libc6 (>= 2.29) but 2.27-3ubuntu1 is to be installed
               Depends: libicu66 (>= 66.1-1~) but it is not installable
               Depends: libreadline8 (>= 6.0) but it is not installable
               Recommends: r-recommended but it is not going to be installed
               Recommends: r-base-dev but it is not going to be installed
               Recommends: r-doc-html but it is not going to be installed
E: Unable to correct problems, you have held broken packages.
```


when trying to install. I have removed and deleted R 3.6.3 and have added 

```
deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran40/
```


in my `/etc/apt/sources.list` file. Note that the above requires `libc6 (>= 2.29)` yet from what I have seen, it is NOT AVAILABLE for 18.04. Does this mean I have to upgrade to 19.04, or 20.04 in order to run R 4.0?


----------
        
## Answer \#0

**Accepted** Vote: 18

Created at 2020-11-16 22:58:56

------------

I am using Ubuntu 18.04, for this there should be a single source
`deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran40/` in /etc/apt/sources.list
Unfortunately, I added `deb https://cloud.r-project.org/bin/linux/ubuntu focal-cran40/` by mistake which I removed to install R 4.0
R is successfully installed now.


------------
    
    
## Answer \#1

 Vote: 9

Created at 2020-08-04 21:42:30

------------

The problem may to be with your `/etc/apt/sources.list`. Please check whether you have disabled all other repositories for R in there, e.g. it should not contain lines like `deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran35/`


------------
    
    
## Answer \#2

 Vote: 5

Created at 2020-05-06 04:11:46

------------

Question: Is your system otherwise current to 18.04?  If so, the upgrade .  I wrote a blog post (prior to the release) about testing R 4.0.0 on 18.04 along with a video:

- [http://dirk.eddelbuettel.com/blog/2020/04/11#024_rocker_r_4_0_0_testing](http://dirk.eddelbuettel.com/blog/2020/04/11#024_rocker_r_4_0_0_testing)- [http://dirk.eddelbuettel.com/papers/r4_r400_binaries.pdf](http://dirk.eddelbuettel.com/papers/r4_r400_binaries.pdf)- [https://youtu.be/aa9lZJ7ZzKM](https://youtu.be/aa9lZJ7ZzKM)

Two more recent (related) posts at [http://dirk.eddelbuettel.com/blog/code/r4/](http://dirk.eddelbuettel.com/blog/code/r4/) as well.

In short, on a system that is otherwise well (i.e. no other packages forced or conflicts or what have you), one just adds the new repo (shown in slides and video), updates the `apt` index and upgrades.  I have done the same for my 19.10 Ubuntu system in another video.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-04-18 14:41:08

------------


sudo nano /etc/apt/sources.list
Remove each line which is similar of the below given path
deb [https://cloud.r-project.org/bin/linux/ubuntu](https://cloud.r-project.org/bin/linux/ubuntu) bionic-cran40/
save it and exit from editor
Then try to install R 4.1.3 as per the given way from the below link
[https://cloud.r-project.org/bin/linux/ubuntu/](https://cloud.r-project.org/bin/linux/ubuntu/)


------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-08-29 13:56:58

------------

I have 18.04 and want R 4.x
I had to edit the etc/apt/sources.list to

1. not have R 3.5 in the sources
2. instead of having '... focal-cran40/' to have '... bionic-cran40/'.


Then I used
```
sudo apt update
sudo apt install r-base
```

and it all worked


------------
    
    
## Answer \#5

 Vote: -1

Created at 2021-03-23 09:38:31

------------

I just met the same problems while install R 4.0 on Ubuntu 20.04
here is my solutions.
first I just want install R instead of r-base-core and so on. it didn't work that install all the r-base-core and r-base-dev so on.
read the official document [https://cran.r-project.org/bin/linux/ubuntu/#install-r](https://cran.r-project.org/bin/linux/ubuntu/#install-r)
carefully.
if the problems still exist, try renew `/etc/apt/sources.list`
use the complete document or cope the backup ones


------------
    
    