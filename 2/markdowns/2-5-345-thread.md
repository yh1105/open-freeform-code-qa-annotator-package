
# Post \#72683786 [Link](https://stackoverflow.com/questions/72683786/)

## Error while importing Springboot 2.7 projects in intellij Idea with maven 3.8.5

**Vote**: 17 (290/702) **Views**: 16771 (336/702) 

**Internal ID** \#2-5-345

Created at 2022-06-20 07:49:46

Tags: `java` `spring` `spring-boot` `maven` `intellij-idea`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

when Using start.spring.io
projects generated with springboot 2.7 comes with MavenProject 3.8.5 which when imported in intellij causes an error that is quite difficult to debug or not self speaking by itself.
The error
```
java.lang.RuntimeException: org.codehaus.plexus.component.repository.exception.ComponentLookupException: com.google.inject.ProvisionException: Unable to provision, see the following errors:

1) Error injecting constructor, java.lang.NoSuchMethodError: org.apache.maven.model.validation.DefaultModelValidator: method 'void <init>()' not found
  at org.jetbrains.idea.maven.server.embedder.CustomModelValidator.<init>(Unknown Source)
  while locating org.jetbrains.idea.maven.server.embedder.CustomModelValidator
  at ClassRealm[maven.ext, parent: ClassRealm[plexus.core, parent: null]] (via modules: org.eclipse.sisu.wire.WireModule -> org.eclipse.sisu.plexus.PlexusBindingModule)
  while locating org.apache.maven.model.validation.ModelValidator annotated with @com.google.inject.name.Named(value="ide")

1 error
      role: org.apache.maven.model.validation.ModelValidator
  roleHint: ide
```



----------
        
## Answer \#0

**Accepted** Vote: 25

Created at 2022-06-20 11:12:10

------------

That should have been fixed in 2022.1 in the scope of [this bug](https://youtrack.jetbrains.com/issue/IDEA-290419/Maven-385-NoSuchMethodError-orgapachemavenmodelvalidationDefaultModelValidator-method-void-init-not-found)
Please update your IDE


------------
    
    
## Answer \#1

 Vote: 21

Created at 2022-08-09 17:32:54

------------

Under Intellij  >  >  >  >  >  just point to your maven directory:
[](https://i.stack.imgur.com/qX26l.png)
Apply and reload project.


------------
    
    
## Answer \#2

 Vote: 6

Created at 2022-07-12 10:44:03

------------

I had the same error with stack: IDEA 2019.3, Maven 3.8.6, JDK 1.8.
So, problem was resolved by change Maven to version 3.6.1


------------
    
    
## Answer \#3

 Vote: 3

Created at 2022-07-27 19:53:30

------------

I've just ran into this problem 20mins ago with my friend's project. The solution is just perfect.
If you don't want to update your IDE (for any reason) or downgrade yor Maven version, you just need to change your Maven Home path: (File | Settings | Build,...etc. | Build Tools | Maven) for any listed variant except "...wrapper".
Generally, I use separated maven in C:/my-maven-folder/...wrapper, so I've never had such a problem. Hope it helps!


------------
    
    