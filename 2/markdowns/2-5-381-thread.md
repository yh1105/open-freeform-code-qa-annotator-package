
# Post \#62837953 [Link](https://stackoverflow.com/questions/62837953/)

## Protocol Buffer imports not recognized in Intellij

**Vote**: 29 (199/702) **Views**: 27719 (243/702) 

**Internal ID** \#2-5-381

Created at 2020-07-10 15:51:36

Tags: `java` `gradle` `intellij-idea` `protocol-buffers` `grpc`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I am attempting to import one protocol buffer message into another, but the imports are not recognized. As long as I don't try to import one protobuf into another, the protobuf code is generated (in java), the code compiles and runs as expected.
I'm using:
- - - 
My gradle build file looks like this:
```
plugins {
    id 'java'
    id 'com.google.protobuf' version "0.8.8"
}

group 'tech.tablesaw'
version '1.0-SNAPSHOT'

sourceCompatibility = 9.0

def grpcVersion = '1.30.1' // CURRENT_GRPC_VERSION
def protobufVersion = '3.12.0'
def protocVersion = protobufVersion

repositories {
    mavenCentral()
}

test {
    useJUnitPlatform()
}

dependencies {
    implementation "io.grpc:grpc-protobuf:${grpcVersion}"
    implementation "io.grpc:grpc-stub:${grpcVersion}"
    compileOnly "org.apache.tomcat:annotations-api:6.0.53"

    // advanced - need this for JsonFormat
    implementation "com.google.protobuf:protobuf-java-util:${protobufVersion}"

    runtimeOnly "io.grpc:grpc-netty-shaded:${grpcVersion}"
    testImplementation "io.grpc:grpc-testing:${grpcVersion}"

    compile group: 'tech.tablesaw', name: 'tablesaw-core', version: '0.38.1'
    testCompile group: 'org.junit.jupiter', name: 'junit-jupiter-engine', version: '5.6.2'
    testImplementation "org.mockito:mockito-core:2.28.2"
}

protobuf {
    protoc { artifact = "com.google.protobuf:protoc:${protocVersion}" }
    plugins {
        grpc { artifact = "io.grpc:protoc-gen-grpc-java:${grpcVersion}" }
    }
    generateProtoTasks {
        all()*.plugins { grpc {} }
    }
}

// Inform IDEs like IntelliJ IDEA, Eclipse or NetBeans about the generated code.
sourceSets {
    main {
        java {
            srcDirs 'build/generated/source/proto/main/grpc'
            srcDirs 'build/generated/source/proto/main/java'
        }
    }
}

task TablesawServer(type: CreateStartScripts) {
    mainClassName = 'tech.tablesaw.service.TableServiceServer'
    applicationName = 'tablesaw-table-server'
    outputDir = new File(project.buildDir, 'tmp')
}

task TablesawClient(type: CreateStartScripts) {
    mainClassName = 'tech.tablesaw.service.TableServiceClient'
    applicationName = 'tablesaw-table-client'
    outputDir = new File(project.buildDir, 'tmp')
}
```

and my gradle info looks like this:
```
------------------------------------------------------------
Gradle 5.1.1
------------------------------------------------------------

Build time:   2019-01-10 23:05:02 UTC
Revision:     3c9abb645fb83932c44e8610642393ad62116807

Kotlin DSL:   1.1.1
Kotlin:       1.3.11
Groovy:       2.5.4
Ant:          Apache Ant(TM) version 1.9.13 compiled on July 10 2018
JVM:          9.0.4 (Oracle Corporation 9.0.4+11)
OS:           Mac OS X 10.13.5 x86_64
```

Here is an example protobuf. the import of the column_type.proto fails.
```
syntax = "proto3";

package tech.tablesaw.service.common;

import "tech/tablesaw/service/common/column_type.proto";

option java_multiple_files = true;
option java_package = "tech.tablesaw.service.common";
option java_outer_classname = "ColumnMetaProto";
option objc_class_prefix = "TSW";

// Proto file describing column metadata message.

// A column metadata object
message ColumnMetadata {

  string name = 1;
  int32 size = 2;
  ColumnTypeEnum.ColumnType column_type = 3;
}
```

And here is the file i'm trying to import:
```
syntax = "proto3";

package tech.tablesaw.service.common;

option java_multiple_files = true;
option java_package = "tech.tablesaw.service.common";
option java_outer_classname = "ColumnTypeEnum";
option objc_class_prefix = "TSW";

enum ColumnType {
  SHORT = 0;
  INTEGER = 1;
  LONG = 2;
  FLOAT = 3;
  BOOLEAN = 4;
  STRING = 5;
  DOUBLE = 6;
  LOCAL_DATE = 7;
  LOCAL_TIME = 8;
  LOCAL_DATE_TIME = 9;
  INSTANT = 10;
  TEXT = 11;
  SKIP = 12;
}
```

Finally, here's where the protobufs sit in the file system.
```
src > main > java
           > proto > tech > tablesaw > service > common > column_metadata.proto
                                                        > column_type.proto
```



----------
        
## Answer \#0

**Accepted** Vote: 48

Created at 2020-07-10 23:13:28

------------

Take a look at the [readme](https://github.com/jvolkman/intellij-protobuf-editor#path-settings) which describes how to add additional paths.
By default, `intellij-protobuf-editor` uses the project's configured source roots as protobuf import paths. If this isn't correct, you can override these paths in `Settings > Languages & Frameworks > Protocol Buffers`. Uncheck "Configure automatically" and add whichever paths you need. In your case, you'd add `.../src/main/java/proto` (where `...` means whatever your project's base path is).


------------
    
    
## Answer \#1

 Vote: 3

Created at 2020-07-10 17:22:21

------------

There was an answer to this question in the comments for another question: [Error using import in .proto file](https://stackoverflow.com/questions/21134066/error-using-import-in-proto-file)
> If you're using IntelliJ IDEA, go to Preferences -> Protobuf Support and add the path to your .proto file. This would resolve the error.
In a more modern version of Intellij, this is done in the plugin intellij-protobuf-editor. I added the path to the root of my proto source folder and all was well


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-06-24 11:41:49

------------

Want to describe what helped me, since I did not find an answer here.
My steps are for Maven, but I think it will be pretty much the same for Gradle.

1. Check that you have this plugin


```
<plugin>
        <groupId>org.xolstice.maven.plugins</groupId>
        <artifactId>protobuf-maven-plugin</artifactId>
        <version>0.6.1</version>

        <configuration>
          <protocArtifact>com.google.protobuf:protoc:3.12.2:exe:${os.detected.classifier}</protocArtifact>
        </configuration>

        <executions>
          <execution>
            <goals>
              <goal>compile</goal>
              <goal>test-compile</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
```


1. Check that you have this extension in your build section


```
<build>
    <extensions>
      <extension>
        <groupId>kr.motd.maven</groupId>
        <artifactId>os-maven-plugin</artifactId>
        <version>1.6.1</version>
      </extension>
    </extensions>
    ....
```


1. Run this maven goal


```
mvn protobuf:compile
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-08-20 08:43:02

------------

If you are using [protobuf-gradle-plugin](https://github.com/google/protobuf-gradle-plugin), you need to apply the `idea` plugin. After applying it, everything works like a charm. From their readme
```
apply plugin: 'idea'

clean {
    delete protobuf.generatedFilesBaseDir
}

idea {
    module {
        // proto files and generated Java files are automatically added as
        // source dirs.
        // If you have additional sources, add them here:
        sourceDirs += file("/path/to/other/sources");
    }
}
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-07-11 00:26:22

------------

I am using GoLand but the settings should be the same for IntelliJ:
Disable the `configure automatically`
Add all the paths that your projects is using.
[](https://i.stack.imgur.com/6Bhwl.png)


------------
    
    
## Answer \#5

 Vote: 0

Created at 2023-03-01 17:17:26

------------

This is sometimes caused by using imports in your proto files that are added to the project as a maven dependency.
To work around this, you can include a step in your maven build to unpack the proto files from the library (I'm using `proto-google-common-protos` as an example here) and add them to a location that is 'auto-detected' by IntelliJ.
```
<plugin>
  <artifactId>maven-dependency-plugin</artifactId>
  <executions>
    <execution>
      <!-- This is a workaround for https://youtrack.jetbrains.com/issue/IDEA-285012/Protobuff-In-case-path-to-.proto-files-not-specified-in-Protocol-Buffers-settings-on-run-grpc-request-I-get -->
      <id>unpack-google-common-protos</id>
      <phase>generate-sources</phase>
      <goals>
        <goal>unpack</goal>
      </goals>
      <configuration>
        <artifactItems>
          <artifactItem>
            <groupId>com.google.api.grpc</groupId>
            <artifactId>proto-google-common-protos</artifactId>
            <type>jar</type>
            <overWrite>true</overWrite>
            <outputDirectory>${project.build.directory}/generated-sources/protobuf/grpc-java</outputDirectory>
            <includes>**/*.proto</includes>
          </artifactItem>
        </artifactItems>
      </configuration>
    </execution>
  </executions>
</plugin>
```



------------
    
    