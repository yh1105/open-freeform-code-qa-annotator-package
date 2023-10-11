
# Post \#61182996 [Link](https://stackoverflow.com/questions/61182996/)

## Flipper cannot be opened because the developer cannot be verified in macOS Catalina

**Vote**: 45 (139/702) **Views**: 27002 (248/702) 

**Internal ID** \#0-0-114

Created at 2020-04-13 07:06:15

Tags: `macos` `react-native` `flipper`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

`Flipper` is a platform for debugging iOS, Android and React Native apps.
I have downloaded the `Flipper` app from [https://fbflipper.com/](https://fbflipper.com/), but I can't run it.
[](https://i.stack.imgur.com/eDtz1.png)
macOS version: `Catalina 10.15.3`


----------
        
## Answer \#0

**Accepted** Vote: 80

Created at 2020-04-13 08:14:48

------------

Just . Or alternatively, open System Preferences, Security & Privacy, General tab, and Click 'Open Anyway'. [Guide on support.apple.com](https://support.apple.com/en-gb/guide/mac-help/mh40616/mac)
- `xattr -d com.apple.quarantine /Applications/Flipper.app`

---



## Problem


- This is a general problem (security feature called [Gatekeeper](https://en.wikipedia.org/wiki/Gatekeeper_(macOS))) with apps on macOS where the developer is not registered with Apple (Paying $99/year) and so has not notarized their application. Specifically, Gatekeeper saves a quarantine flag on a downloaded file. So alternatively, you can remove this flag for your specific file, or disable Gatekeeper entirely so you never have this issue again.- A nice explanation by the Terraform team who faced the same issue:> Indeed the new version of MacOS is stricter about running software you've downloaded that isn't signed with an Apple developer key. Terraform distributions are signed with a HashiCorp key with signatures distributed out of band on releases.hashicorp.com, but MacOS Catalina is requiring participation in Apple's developer program specifically, and Terraform is currently not built to support that. [source](https://github.com/hashicorp/terraform/issues/23033)-  Applications on macOS need a `Developer ID` [certificate](https://developer.apple.com/developer-id/) to verify that we are trusted developers. This also provides access to macOS capabilities: e.g. CloudKit, Apple Push Notifications. If you're making an app, you can need to go into developer.apple.com to create an Apple Developer ID certificate, or create on in XCode (in some cases).


------------
    
    
## Answer \#1

 Vote: 39

Created at 2020-09-17 12:09:22

------------

You can run following command to remove the warning and allow un-authorized app to run on macOS Catalina:
```
xattr -d com.apple.quarantine /Applications/Flipper.app
```



------------
    
    
## Answer \#2

 Vote: 14

Created at 2020-09-20 04:21:14

------------

Go to System preference, Security and Privacy and choose Opneanyway:
[](https://i.stack.imgur.com/YKsz3.png)


------------
    
    
## Answer \#3

 Vote: 3

Created at 2020-08-24 02:57:39

------------

An alternative is to either [run or build from source](https://github.com/facebook/flipper#building-from-source).
Helpful if you're on a work computer with tighter application security settings.
Prerequisites:
- [https://nodejs.org/en/](https://nodejs.org/en/)- [https://yarnpkg.com](https://yarnpkg.com)

```
git clone https://github.com/facebook/flipper.git
cd flipper/desktop
yarn
yarn start
```


```
yarn build --mac --version $buildNumber
```

For Building from source, you should do it in the following steps:
```
git clone https://github.com/facebook/flipper.git
cd flipper/desktop
yarn
yarn build --mac --version $buildNumber
```

Note: I've noticed building from source as of Aug 2020 `0.53.0` has some issues for me, so would recommend running from source if you need to use this.


------------
    
    
## Answer \#4

 Vote: 2

Created at 2021-08-20 08:10:20

------------


and after that you'll see message 'flipper was blocked from ... '
press 


------------
    
    