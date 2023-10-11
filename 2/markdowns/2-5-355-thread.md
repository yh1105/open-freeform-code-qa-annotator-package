
# Post \#50854359 [Link](https://stackoverflow.com/questions/50854359/)

## How to uninstall Eclipse?

**Vote**: 95 (65/702) **Views**: 330388 (18/702) 

**Internal ID** \#2-5-355

Created at 2018-06-14 09:41:40

Tags: `java` `eclipse` `uninstallation` `oomph` `eclipse-installer`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I want to uninstall Eclipse version Mars 2, but it doesn't appear in the "Programs and Features" area of the Windows Control Panel. The installer has installed start menu shortcuts and other artifacts, so it's not just a matter of deleting the installation folder.


----------
        
## Answer \#0

**Accepted** Vote: 121

Created at 2018-06-14 10:50:54

------------

There is no automated uninstaller.
You have to remove Eclipse manually by deleting some directories and files.
`~`

## Why is there no uninstaller?


According to this [discussion about uninstalling Eclipse](https://www.eclipse.org/forums/index.php/t/1079348/), the reasoning for not providing an uninstaller is that the Eclipse installer is supposed to just automate a few tasks that in the past had to be done manually (like downloading and extracting Eclipse and adding shortcuts), so they also can be undone manually. On Windows there is no entry in "Programs and Features" because the installer does not register installations in the system registry.

## How to to locate Eclipse installations


The default installation path is in `~/eclipse/.
The Eclipse installer also keeps track of installations. Launch the installer and then open the "Bundle Pool" dialog (in simple mode it's in the menu - in advanced mode it's a small button next to the bundle pool drop down). You'll see list of "profiles". Double click on a profile to see the path of the installation.
You could also search for "eclipse" files with any file search tool to locate installation folders. But be aware that Eclipse is a platform that other software can build upon, so there might be folders of Eclipse-based applications that look very much like Eclipse IDE installations but are actually software you might want to keep.

## How to quickly uninstall Eclipse


If you have installed Eclipse manually without the Eclipse installer, just delete the Eclipse directory and be done with it.
If you have installed Eclipse with the Eclipse installer, then delete the Eclipse directory and any desktop and start menu shortcuts. Then launch the Eclipse installer and open the "Bundle Pool" dialog and click on "Cleanup Agent" to remove unused bundles. This won't affect other Eclipse installations if you have any.
In my opinion this is generally enough and I would stop here

## How to completely uninstall Eclipse


If you really want to remove Eclipse without leaving any traces, you have to manually delete
- - `~/eclipse/photon/`- 
The installer has a "Bundle Pools" menu entry which lists the locations of all bundle pools. If you have other Eclipse installations on your system you can use the "Cleanup Agent" to remove unused bundles. If you don't have any other Eclipse installations you can delete the whole bundle pool directory instead (by default `~/p2/`).
If you want to completely remove the  too, delete the installer's executable and the `~/.eclipse/` directory.
Depending on what kind of work you did with Eclipse, there can be more directories that you may want to delete. If you used Maven, then `~/.m2/` contains the Maven cache and settings (shared with Maven CLI and other IDEs). If you develop Eclipse plugins, then there might be JUnit workspaces from test runs, next to you Eclipse workspace. Likewise other build tools and development environments used in Eclipse could have created similar directories.

## How to delete all projects


If you want to delete your projects and workspace metadata, you have to delete your workspace(s). The default workspace location is ´~/workspace/´. You can also [search for the .metadata directory to get all Eclipse workspaces on your machine](https://stackoverflow.com/a/54948948/897024).
If you are working with Git projects, these are generally not saved in the workspace but in the `~/git/` directory.

## Addendum: Windows Registry


Even though Eclipse isn't "installed" like a regular Windows application, there are still a few entries that get written to the system registry. If you search the Registry for "eclipse" you'll likely find  some entries for URL handlers named `eclipse+command`, `eclipse+mpc` or `eclipse+installer`. They are used e.g. for letting you install Eclipse plugins from the browser.
On my system I see a few other entries which I believe are created by Windows itself and not by Eclipse (e.g. entries in the list of recently used executables). Whether you want to delete those entries is up to you. Personally I wouldn't, but on the other hand it probably won't break anything if you do. Just be aware that registry "cleaning" is a controversial topic.


------------
    
    
## Answer \#1

 Vote: 4

Created at 2020-09-05 01:53:37

------------

The steps are very simple and it'll take just few mins.
1.Go to your  C drive and in that go to the 'USER' section.
2.Under 'USER' section go to your 'name(e.g-'user1') and then find ".eclipse" folder and
delete that folder
3.Along with that folder also delete "eclipse" folder and you can find that you're
work has been done completely.


------------
    
    
## Answer \#2

 Vote: 3

Created at 2020-02-28 06:32:14

------------

Right click on eclipse icon and click on open file location then delete the eclipse folder from drive(Save backup of your eclipse workspace if you want). Also delete eclipse icon. Thats it..


------------
    
    
## Answer \#3

 Vote: 1

Created at 2018-12-31 17:38:41

------------

Look for an installation subdirectory, likely named eclipse. Under that 
subdirectory, if you see files like eclipse.ini, icon.xpm and 
subdirectories like plugins and dropins, remove the subdirectory parent 
(the one named eclipse).

That will remove your installation except for anything you've set up 
yourself (like workspaces, projects, etc.).

Hope this helps.


------------
    
    
## Answer \#4

 Vote: 1

Created at 2021-05-31 17:53:36

------------

just search in widows bar for * ,
and delete all folders in parent location


------------
    
    