
# Post \#65156377 [Link](https://stackoverflow.com/questions/65156377/)

## Some conflicts were found in the installation area: jbr/bin/java is modified

**Vote**: 35 (176/702) **Views**: 8909 (432/702) 

**Internal ID** \#2-5-347

Created at 2020-12-05 11:26:30

Tags: `intellij-idea`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

On Ubuntu, can't update to IntelliJ IDEA 2020.3 because of this conflict. What's going on? How do I resolve it?
[](https://i.stack.imgur.com/0874E.jpg)
Image transcript:
> Some conflicts were found in the installation area.Some of the conflicts below do not have a solution, so the patch cannot be applied.
Please download this version from developer Web site and reinstall it from scratch.
Press 'Cancel' to exit.


----------
        
## Answer \#0

**Accepted** Vote: 40

Created at 2021-01-11 08:24:22

------------

update with the tool [https://www.jetbrains.com/toolbox-app/](https://www.jetbrains.com/toolbox-app/) worked for me


------------
    
    
## Answer \#1

 Vote: 6

Created at 2021-02-22 17:53:00

------------

As @paul-praet commented, the following seems to work:

1. close IDE and terminate all java/gradle processes
2. delete or move the jbr sub-directory from the idea-IU-*** directory
3. start the IDE
4. perform the update using "Check for Updates" menu entry




------------
    
    
## Answer \#2

 Vote: 4

Created at 2020-12-05 21:11:37

------------

I fought with this for a while. There's a [long thread here](https://youtrack.jetbrains.com/issue/IDEA-215497) that suggested several things, . In summary:
- - `Configure | Check for Updates`
None of that worked for me, so I eventually just downloaded a fresh full install instead of the patch :|


------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-04-19 15:30:27

------------

I did this before updating and it worked perfectly:
```
$ chmod 777 jbr/bin/java
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-07-29 07:58:26

------------

Had the same issue when updating to 2021.2 For me none of the answers worked.
The only thing that worked for me was to download Intellij from Jetbrains website and install it over the old one (replace)


------------
    
    