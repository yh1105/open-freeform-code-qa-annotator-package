
# Post \#61124946 [Link](https://stackoverflow.com/questions/61124946/)

## How to you use a specific version of Java in Azure Devops Agent without downloading?

**Vote**: 30 (193/702) **Views**: 24907 (264/702) 

**Internal ID** \#2-5-364

Created at 2020-04-09 15:44:15

Tags: `java` `azure-devops`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I am trying to run Maven using the Maven wrapper rather than the Maven task.  However, it's failing because it is using an older version of Java.  The JavaInstaller task seems to require a remote source for the JDK, I would rather avoid doing that and use the one that works with Maven task, but I can't find it documented anywhere.


----------
        
## Answer \#0

**Accepted** Vote: 50

Created at 2020-10-08 10:50:43

------------

You can [now](https://github.com/microsoft/azure-pipelines-tasks/pull/12918) also use the `JavaToolInstaller` task to activate one of the pre-installed Java versions, e.g.
```
- task: JavaToolInstaller@0
  inputs:
    versionSpec: '11'
    jdkArchitectureOption: 'x64'
    jdkSourceOption: 'PreInstalled'
```

See documentation at:
[https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/tool/java-tool-installer?view=azure-devops](https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/tool/java-tool-installer?view=azure-devops)
It will also set `JAVA_HOME` and prepend the `PATH`, see source:
[https://github.com/microsoft/azure-pipelines-tasks/blob/46cca412451ac4418d6332114fca8ef8c3095de1/Tasks/JavaToolInstallerV0/javatoolinstaller.ts#L80](https://github.com/microsoft/azure-pipelines-tasks/blob/46cca412451ac4418d6332114fca8ef8c3095de1/Tasks/JavaToolInstallerV0/javatoolinstaller.ts#L80)


------------
    
    
## Answer \#1

 Vote: 19

Created at 2020-07-28 01:36:36

------------

The Java version to be used can be set via `env` field of the task for Linux or macOS:
```
- script: |
    java -version
  env:
    JAVA_HOME: $(JAVA_HOME_8_X64)
    PATH: $(JAVA_HOME_8_X64)/bin:$(PATH)
```

and for Windows, change the colon in PATH to semicolon:
```
- script: |
    java -version
  env:
    JAVA_HOME: $(JAVA_HOME_8_X64)
    PATH: $(JAVA_HOME_8_X64)/bin;$(PATH)
```

Alternatives of Java version include:
- `JAVA_HOME_7_X64`- `vs2017-win2016``windows-2019`- `macos-10.14``macos-10.15`- `ubuntu-16.04``ubuntu-18.04`- `JAVA_HOME_8_X64`- `vs2017-win2016``windows-2019`- `macos-10.14``macos-10.15`- `ubuntu-16.04``ubuntu-18.04``ubuntu-20.04`- `JAVA_HOME_11_X64`- `vs2017-win2016``windows-2019`- `macos-10.14``macos-10.15`- `ubuntu-16.04``ubuntu-18.04``ubuntu-20.04`- `JAVA_HOME_12_X64`- `macos-10.14``macos-10.15`- `ubuntu-16.04``ubuntu-18.04`- `JAVA_HOME_13_X64`- `vs2017-win2016``windows-2019`- `macos-10.14``macos-10.15`- `JAVA_HOME_14_X64`- `macos-10.14``macos-10.15`


------------
    
    
## Answer \#2

 Vote: 12

Created at 2020-04-09 17:30:42

------------

Add the following `script` before you run Maven for Unix based agents

```
- script: |
    echo "##vso[task.setvariable variable=JAVA_HOME]$(JAVA_HOME_11_X64)"
    echo "##vso[task.setvariable variable=PATH]$(JAVA_HOME_11_X64)/bin:$(PATH)"
  displayName: "Set java version"
```


For Windows based agents

```
- script: |
    echo "##vso[task.setvariable variable=JAVA_HOME]$(JAVA_HOME_11_X64)"
    echo "##vso[task.setvariable variable=PATH]$(JAVA_HOME_11_X64)\bin;$(PATH)"
  displayName: "Set java version"
```


This part of the pipeline code shows how the JAVA_HOME value is selected:
[https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/Common/java-common/java-common.ts](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/Common/java-common/java-common.ts)


------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-05-20 07:06:13

------------

Just like already mentioned by [Martin Kreidenweis](https://stackoverflow.com/a/64260978/1183010), [JavaToolInstaller](https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/tool/java-tool-installer?view=azure-devops) can be used.
When this is used however on , Java needs to be installed on the agent(s) and the required environment variable needs to be set to point to the installation directory.
JavaToolInstaller uses an environment variable derived from its configuration. Convention:
```
JAVA_HOME_${versionSpec}_${jdkArchitectureOption}
```

The environment variable can we set in the agent's home directory in the `.env` file like this:
```
JAVA_HOME_17_x64=/usr/lib/jvm/temurin-17-jdk-amd64
```

After editing `.env`, the agent needs to be restarted to make the environment variable available for the pipeline. This can be done via :
```
./svc.sh stop
./svc.sh start
```

See [Azure documentation](https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/v2-linux?view=azure-devops#service-update-environment-variablesmark).
After that a step can be added like:
```
- task: JavaToolInstaller@0
 inputs:
   versionSpec: '17'
   jdkArchitectureOption: 'x64'
   jdkSourceOption: 'PreInstalled'
```



------------
    
    