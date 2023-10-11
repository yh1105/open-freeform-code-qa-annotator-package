
# Post \#68871952 [Link](https://stackoverflow.com/questions/68871952/)

## How to use Jpackage to make a distribution format for JavaFX applications

**Vote**: 1 (672/702) **Views**: 4849 (525/702) 

**Internal ID** \#2-5-386

Created at 2021-08-21 09:57:41

Tags: `java` `javafx` `javafx-11` `jpackage`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

since .jar is not anymore the best format to use to distribute our JavaFX project, I'm willing to use the tool JPackage for that instead, but after reading this post : [https://stackoverflow.com/a/68823040/4262407](https://stackoverflow.com/a/68823040/4262407), I ended up having multiple questions, but first of all, I just wanna make sure I ve well understood the process that I should follow :
the process :
1-create a modular project
2-package the project in a .jar format
3-use the tool Jlink to create a customized run-time image (to reduce the size of the output)
4-use the tool jpackage (it takes 2 and 3 as inputs)
I'm also wondering if I can rely on Intellij artifacts to create the .jar file (the 2 step) is it better to use a specific plugin ?
The last thing that is not clear is do we need to include the dependencies in the .jar file ? since I believe they will be included in the customized run time, won't they?


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2021-08-21 10:05:16

------------

If you take a look [here](https://openjfx.io/openjfx-docs/), there is an explanation on how to create JavaFX projects starting from a template that includes Maven plugins to easily pack the application using jlink.
Doing it like this will allow you to completely rely on IntelliJ, making things much easier.
Once you have your runtime image, you can pack it with `jpackage`. There was already a discussion about this topic [here](https://stackoverflow.com/a/60533660/16413431).
And here is a fast link to [the article mentioned there](https://walczak.it/blog/distributing-javafx-desktop-applications-without-requiring-jvm-using-jlink-and-jpackage). The article there shows it using Gradle, but you can do the same with Maven, just use the appropriate plugin (jpackage-maven-plugin, as stated in [another answer](https://stackoverflow.com/a/68878985/16413431) here).
Also, from what I can read in your comments, it seems that you have both modular and non-modular dependencies. In that case, you can use `jdeps` to achieve what you need. There is a [nice article here](https://medium.com/azulsystems/using-jlink-to-build-java-runtimes-for-non-modular-applications-9568c5e70ef4) about having mixed dependencies, that specifically uses JavaFX as an example of modular library within a non-modular application.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-08-21 15:59:25

------------

Just have look here [https://github.com/dlemmermann/JPackageScriptFX](https://github.com/dlemmermann/JPackageScriptFX) for a tutorial with a working example. Its especially useful for non-modular projects.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-08-22 06:19:41

------------

I used the jpackage maven plug-in to build the native executable([https://search.maven.org/artifact/org.panteleyev/jpackage-maven-plugin](https://search.maven.org/artifact/org.panteleyev/jpackage-maven-plugin)).
You will first have to use maven-javafx-plugin to create a jlink runtime image([https://github.com/openjfx/javafx-maven-plugin](https://github.com/openjfx/javafx-maven-plugin)).
You will then use that runtime image within jpackage plugin(... ).should create an executable successfully then.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-08-22 01:15:11

------------

The `jpackage` tool does not require a modular project. Though if your project is not modular then your project will be mixed due to JavaFX being modular. In that case, you'll add the JavaFX modules to the custom run-time image while telling `jpackage` to treat your code as non-modular (i.e. it will configure the executable to place your code on the class-path).
You can also combine steps 3 and 4 into a single step. The `jpackage` tool can generate the custom run-time image. It will use `jlink` "behind the scenes".
As for what format your code needs to be in, packaging it in a JAR file will be, in my opinion, the most straightforward. But I'm not sure that's strictly .
Any dependencies of your application will of course need to be included with the final application. If those dependencies are modular then you can have them included via the custom run-time image. But if you want them on the class-path then you can include them the same way as your code (i.e. `--input`).
Note the `jpackage` tool has a user guide: [https://docs.oracle.com/en/java/javase/16/jpackage/packaging-overview.html](https://docs.oracle.com/en/java/javase/16/jpackage/packaging-overview.html)


------------
    
    