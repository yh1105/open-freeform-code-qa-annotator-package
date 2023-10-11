
# Post \#64791380 [Link](https://stackoverflow.com/questions/64791380/)

## Can Homebrew run on Apple ARM processors?

**Vote**: 6 (473/702) **Views**: 9251 (426/702) 

**Internal ID** \#1-3-216

Created at 2020-11-11 17:36:26

Tags: `python` `node.js` `macos` `homebrew` `apple-silicon`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `javascript` `python`)

----------

**Notepad**


----------

I ordered a MacBook Pro equipped with an M1 ARM processor. Will I be able to run [Homebrew](https://brew.sh/) and install dev tools like Python, Node etc..?


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2021-02-05 16:06:32

------------

Yes. Now Homebrew fully supports Apple Silicon [https://brew.sh/2021/02/05/homebrew-3.0.0/](https://brew.sh/2021/02/05/homebrew-3.0.0/)


------------
    
    
## Answer \#1

 Vote: 3

Created at 2020-11-11 17:43:10

------------

As said [here](https://github.com/Homebrew/brew/pull/9075), They will do more support on ARM CPU's
But, as said [here](https://github.com/Homebrew/brew/issues/9066) and more specifically [here](https://github.com/Homebrew/brew/blob/f5b71af6de4103de37364aec10c7be524760cd51/docs/Homebrew-on-Linux.md):
> Homebrew can run on 32-bit ARM (Raspberry Pi and others) and 64-bit ARM (AArch64), but no binary packages (bottles) are available. Support for ARM is on a best-effort basis. Pull requests are welcome to improve the experience on ARM platforms.
You may need to install your own Ruby using your system package manager, a PPA, or rbenv/ruby-build as we no longer distribute a Homebrew Portable Ruby for ARM.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-12-12 15:47:01

------------

Yes, it's supported but using Rosetta.
Just put this prefix before the install homebrew command
> arch -x86_64
the final command should look like this (from [https://brew.sh](https://brew.sh))
> arch -x86_64 /bin/bash -c "$(curl -fsSL [https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh](https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh))"
And every time you use brew you would have to use it with the prefix above.
Option 2:
There is an alternative, opening your terminal using Rosetta. I have not tried it but should give the same results without using the prefix.
[](https://i.stack.imgur.com/kPi3N.png)


------------
    
    
## Answer \#3

 Vote: 0

Created at 2020-12-11 06:59:40

------------

partial support for packages and more coming with the
most recent instructions I've found at [https://github.com/mikelxc/Workarounds-for-ARM-mac](https://github.com/mikelxc/Workarounds-for-ARM-mac) Homebrew [issue 7857](https://github.com/Homebrew/brew/issues/7857) mentioned above has a list of compatible packages that appears to be updating as more info comes; it appears that yes it does and there is steady progress to bring improved compatibility over the coming days, weeks, months, etc. I'm guessing the pace will accelerate over time as they integrated this into their CI workflow just a few days ago


------------
    
    