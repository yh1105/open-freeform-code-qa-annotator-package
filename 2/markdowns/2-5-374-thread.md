
# Post \#69486339 [Link](https://stackoverflow.com/questions/69486339/)

## NativeLibraryDarwin.java:64 - Failed to link the C library against JNA. Native methods will be unavailable

**Vote**: 5 (499/702) **Views**: 3327 (561/702) 

**Internal ID** \#2-5-374

Created at 2021-10-07 18:45:28

Tags: `macos` `java-8` `cassandra` `apple-m1`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

If you are working on a latest MacBook pro with M1 chip. Installing Cassandra and starting it might throw an error.
Steps
1: Installed Jdk8
2: Installed Cassandra 4.0.1
How to start: `/opt/homebrew/opt/cassandra/bin/cassandra -f`
Output:
```
ERROR [main] 2021-10-08 00:03:12,568 NativeLibraryDarwin.java:64 - Failed to link the C library against JNA. Native methods will be unavailable.
java.lang.UnsatisfiedLinkError: /opt/homebrew/Cellar/cassandra/4.0.1/tmp/jna9964159388012624603.tmp: dlopen(/opt/homebrew/Cellar/cassandra/4.0.1/tmp/jna9964159388012624603.tmp, 1): no suitable image found.  Did find:
    /opt/homebrew/Cellar/cassandra/4.0.1/tmp/jna9964159388012624603.tmp: no matching architecture in universal wrapper
    /opt/homebrew/Cellar/cassandra/4.0.1/tmp/jna9964159388012624603.tmp: no matching architecture in universal wrapper
    at java.base/java.lang.ClassLoader$NativeLibrary.load0(Native Method)
    at java.base/java.lang.ClassLoader$NativeLibrary.load(ClassLoader.java:2442)
    at java.base/java.lang.ClassLoader$NativeLibrary.loadLibrary(ClassLoader.java:2498)
    at java.base/java.lang.ClassLoader.loadLibrary0(ClassLoader.java:2694)
    at java.base/java.lang.ClassLoader.loadLibrary(ClassLoader.java:2627)
    at java.base/java.lang.Runtime.load0(Runtime.java:768)
    at java.base/java.lang.System.load(System.java:1837)
    at com.sun.jna.Native.loadNativeDispatchLibraryFromClasspath(Native.java:1018)
    at com.sun.jna.Native.loadNativeDispatchLibrary(Native.java:988)
    at com.sun.jna.Native.<clinit>(Native.java:195)
    at com.sun.jna.NativeLibrary.<clinit>(NativeLibrary.java:87)
    at org.apache.cassandra.utils.NativeLibraryDarwin.<clinit>(NativeLibraryDarwin.java:55)
    at org.apache.cassandra.utils.NativeLibrary.<clinit>(NativeLibrary.java:98)
    at org.apache.cassandra.service.CassandraDaemon.setup(CassandraDaemon.java:258)
    at org.apache.cassandra.service.CassandraDaemon.activate(CassandraDaemon.java:763)
    at org.apache.cassandra.service.CassandraDaemon.main(CassandraDaemon.java:887)
INFO  [main] 2021-10-08 00:03:12,596 MonotonicClock.java:199 - Scheduling approximate time conversion task with an interval of 10000 milliseconds
INFO  [main] 2021-10-08 00:03:12,597 MonotonicClock.java:335 - Scheduling approximate time-check task with a precision of 2 milliseconds
WARN  [main] 2021-10-08 00:03:12,597 StartupChecks.java:143 - jemalloc shared library could not be preloaded to speed up memory allocations
WARN  [main] 2021-10-08 00:03:12,597 StartupChecks.java:187 - JMX is not enabled to receive remote connections. Please see cassandra-env.sh for more info.
ERROR [main] 2021-10-08 00:03:12,597 CassandraDaemon.java:909 - The native library could not be initialized properly.
```



----------
        
## Answer \#0

**Accepted** Vote: 15

Created at 2021-10-07 18:58:00

------------

Solution:

1. Find where is jna-..*.jar located. you can do this using find /opt/homebrew/ -name '*.jar' | grep cassandra
2. Browse to https://search.maven.org/artifact/net.java.dev.jna/jna/5.8.0/jar and download the jar from top-right downloads button on the page.
3. Replace your jna-..*.jar file with the one you downloaded. In my case i replaced jna-5.6.0.jar. Remember to keep the file name as your previous file name. Example I moved jna-5.8.0.jar and renamed as jna-5.6.0.jar


`mv /Users/mansooralikhan/Downloads/jna-5.8.0.jar /opt/homebrew/Cellar/cassandra/4.0.1/libexec/jna-5.6.0.jar`

1. Restart cassandra




------------
    
    
## Answer \#1

 Vote: 4

Created at 2021-10-07 19:10:07

------------

To echo Mansoor's answer, I'll add that the version of JNA shipped with Apache Cassandra 4.0 unfortunately is not compatible with the M1 architecture.
To remedy this, head to the "downloads" section of the JNA GitHub repo, where you should find the latest version of the JNA jar file.
[https://github.com/java-native-access/jna#jna](https://github.com/java-native-access/jna#jna)
Download JNA version 5.8 or higher, and replace the existing `jna-5.6.0.jar` file in Cassandra's `lib/` directory (tarball-based installs).


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-10-08 06:33:54

------------

Just to add to the answers here, I wanted to note for posterity that the issue with arm64 on Apple M1 devices not supported by JNA 5.6.0 has been raised with the Apache Cassandra project previously.
More recently, it has been reported in [CASSANDRA-17019](https://issues.apache.org/jira/browse/CASSANDRA-17019). I brought it up with the project devs/contributors and hopefully it gets reviewed and resolved soon. Cheers!


------------
    
    