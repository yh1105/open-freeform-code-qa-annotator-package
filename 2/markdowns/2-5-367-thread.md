
# Post \#57624495 [Link](https://stackoverflow.com/questions/57624495/)

## JUnit test class order

**Vote**: 25 (228/702) **Views**: 20709 (297/702) 

**Internal ID** \#2-5-367

Created at 2019-08-23 10:32:48

Tags: `java` `maven` `junit` `junit4` `junit5`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I have a java app with maven.
Junit for tests, with failsafe and surefire plugins.
I have more than 2000 integration tests.
To speed up the test running, I use failsafe jvmfork to run my tests parallel.
I have some heavy test class, and they typically running at end of my test execution and it is slows down my CI verify process.
The filesafe runorder:balanced would be a good option for me, but i cant use it because the jvmfork.
To rename the test classes or move to another package and run it alpahabetical is not an option.
Any suggestion how can I run my slow test classes at the begining of the verify process?


----------
        
## Answer \#0

**Accepted** Vote: 34

Created at 2021-02-12 08:47:03

------------

In  (from version [5.8.0](https://junit.org/junit5/docs/5.8.0/release-notes/) onwards) test classes can be ordered too.
:
```
# ClassOrderer$OrderAnnotation sorts classes based on their @Order annotation
junit.jupiter.testclass.order.default=org.junit.jupiter.api.ClassOrderer$OrderAnnotation
```

Other Junit built-in class orderer implementations:
```
org.junit.jupiter.api.ClassOrderer$ClassName
org.junit.jupiter.api.ClassOrderer$DisplayName
org.junit.jupiter.api.ClassOrderer$Random
```

[JUnit 5 user guide](https://junit.org/junit5/docs/current/user-guide/#running-tests-config-params)
You can also provide your own orderer. It must implement `ClassOrderer` interface:
```
package foo;
public class MyOrderer implements ClassOrderer {
    @Override
    public void orderClasses(ClassOrdererContext context) {
        Collections.shuffle(context.getClassDescriptors());
    }
}
```

```
junit.jupiter.testclass.order.default=foo.MyOrderer
```


### Note that @Nested test classes cannot be ordered by a ClassOrderer.


Refer to JUnit 5 [documentations](https://junit.org/junit5/docs/snapshot/user-guide/index.html#writing-tests-test-execution-order) and ClassOrderer [API docs](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/ClassOrderer.html) to learn more about this.


------------
    
    
## Answer \#1

 Vote: 5

Created at 2019-09-06 13:18:35

------------

I gave the combination of answers I found a try:

- [Running JUnit4 Test classes in specified order](https://stackoverflow.com/a/19177717)- [Running JUnit Test in parallel on Suite Level](https://stackoverflow.com/a/10174630/11514534)

The second answer is based on these [classes](https://github.com/centic9/commons-test/tree/master/src/test/java/com/mycila/sandbox/junit/runner) of this [github](https://github.com/centic9/commons-test/) project, which is available under the BSD-2 license.

I defined a few test classes:

```
public class LongRunningTest {

    @Test
    public void test() {

        System.out.println(Thread.currentThread().getName() + ":\tlong test - started");

        long time = System.currentTimeMillis();
        do {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
            }
        } while(System.currentTimeMillis() - time < 1000);

        System.out.println(Thread.currentThread().getName() + ":\tlong test - done");
    }
}
```


```
@Concurrent
public class FastRunningTest1 {

    @Test
    public void test1() {
        try {
            Thread.sleep(250);
        } catch (InterruptedException e) {
        }

        System.out.println(Thread.currentThread().getName() + ":\tfrt1-test1 - done");
    }

    // +7 more repetions of the same method
}
```


Then I defined the test suites:
(FastRunningTest2 is a copy of the first class with adjusted output)

```
@SuiteClasses({LongRunningTest.class, LongRunningTest.class})
@RunWith(Suite.class)
public class SuiteOne {}

@SuiteClasses({FastRunningTest1.class, FastRunningTest2.class})
@RunWith(Suite.class)
public class SuiteTwo {}

@SuiteClasses({SuiteOne.class, SuiteTwo.class})
@RunWith(ConcurrentSuite.class)
public class TopLevelSuite {}
```


When I execute the `TopLevelSuite` I get the following output:

`TopLevelSuite-1-thread-1: long test - started FastRunningTest1-1-thread-4: frt1-test4 - done FastRunningTest1-1-thread-2: frt1-test2 - done FastRunningTest1-1-thread-1: frt1-test1 - done FastRunningTest1-1-thread-3: frt1-test3 - done FastRunningTest1-1-thread-5: frt1-test5 - done FastRunningTest1-1-thread-3: frt1-test6 - done FastRunningTest1-1-thread-1: frt1-test8 - done FastRunningTest1-1-thread-5: frt1-test7 - done FastRunningTest2-2-thread-1: frt2-test1 - done FastRunningTest2-2-thread-2: frt2-test2 - done FastRunningTest2-2-thread-5: frt2-test5 - done FastRunningTest2-2-thread-3: frt2-test3 - done FastRunningTest2-2-thread-4: frt2-test4 - done TopLevelSuite-1-thread-1: long test - done TopLevelSuite-1-thread-1: long test - started FastRunningTest2-2-thread-5: frt2-test8 - done FastRunningTest2-2-thread-2: frt2-test6 - done FastRunningTest2-2-thread-1: frt2-test7 - done TopLevelSuite-1-thread-1: long test - done`

Which basically shows that the `LongRunningTest` is executed in parralel to the `FastRunningTests`. The default value of threads used for parallel execution defined by the `Concurrent` Annotation is `5`, which can be seen in the output of the parallel execution of the `FastRunningTests`.

The downside is that theses `Threads` are not shared between `FastRunningTest1` and `FastRunningTest2`.


---



This behavious shows that it is "somewhat" possible to do what you want to do (so whether that works with your current setup is a different question). 

Also I am not sure whether this is actually worth the effort,

- `TestSuites`- `threads`


---



As this basically shows that it is possible to define the execution order of classes and trigger their parallel execution, it should also be possibly to get the whole process to only use one `ThreadPool` (but I am not sure what the implication of that would be).

As the whole concept is based on a ThreadPoolExecutor, using a `PriorityBlockingQueue` which gives long running tasks a higher priority you would get closer to your ideal outcome of executing the long running tests first.


---



I experimented around a bit more and implemented my own custom suite runner and junit runner. The idea behind is to have your JUnitRunner submit the tests into a queue which is handeld by a single `ThreadPoolExecutor`. Because I didn't implement a blocking operation in the `RunnerScheduler#finish` method, I ended up with a solution where the tests from all classes were passed to the queue before the execution even started. (That might look different if there a more test classes and methods involved).

At least it proves the point that you can mess with junit at this level if you really want to.

The code of my poc is a bit messy and to lengthy to put it here, but if someone is interested I can push it into a github project.


------------
    
    
## Answer \#2

 Vote: 4

Created at 2019-08-23 10:50:14

------------

In out project we had created a few marker interfaces (
example 

```
public interface SlowTestsCategory {}
```


)

and put it into the @Category annotation of JUnit in the test class with slow tests.

```
@Category(SlowTestsCategory.class)
```


After that we created some special tasks for Gradle to run tests by category or a few categories by custom order:

```
task unitTest(type: Test) {
  description = 'description.'
  group = 'groupName'

  useJUnit {
    includeCategories 'package.SlowTestsCategory'
    excludeCategories 'package.ExcludedCategory'
  }
}
```


This solution is served by Gradle, but maybe it'll be helpful for you.


------------
    
    
## Answer \#3

 Vote: 4

Created at 2019-09-08 21:24:54

------------

Let me summarize everything before I will provide a recommendation.


1. Integration tests are slow. This is fine and it's natural.
2. CI build doesn't run tests that assume deployment of a system, since there is no deployment in CI. We care about deployment in CD process. So I assume your integration tests don't assume deployment.
3. CI build runs unit tests first. Unit tests are extremely fast because they use only RAM. We have good and quick feedback from unit tests.



At this moment we are sure we don't have a problem with getting a quick feedback. But we still want to run integration tests faster.
I would recommend the following solutions:


1. Improve actual tests. Quite often they are not effective and can be speed up significantly.
2. Run integration tests in background (i.e. don't wait for real time feedback from them). It's natural for them to be much slower than unit tests.
3. Split integration tests on groups and run them separately if you need feedback from some of them faster.
4. Run integration tests in different JVMs. Not different threads within the same JVM! In this case you don't care about thread safety and you should not care about it.
5. Run integration tests on different machines and so on.



I worked with many different projects (some of them had CI build running for 48 hours) and first 3 steps were enough (even for crazy cases). Step #4 is rarely needed having good tests. Step #5 is for very specific situations.

You see that my recommendation relates to the process and not to the tool, because the problem is in the process.
Quite often people ignore root cause and try to tune the tool (Maven in this case). They get cosmetic improvements but with high maintenance cost of created solution.


------------
    
    
## Answer \#4

 Vote: 4

Created at 2021-07-06 10:53:07

------------

There is a solution for that from version  of junit.
Basically you need to create your own orderer. I did something like that.
Here is an annotation which you will use inside your test classes:
```
@Retention(RetentionPolicy.RUNTIME)
public @interface TestClassesOrder {
    public int value() default Integer.MAX_VALUE;
}
```

Then you need to create class which will implement org.junit.jupiter.api.ClassOrderer
```
public class AnnotationTestsOrderer implements ClassOrderer {
    @Override
    public void orderClasses(ClassOrdererContext context) {
        Collections.sort(context.getClassDescriptors(), new Comparator<ClassDescriptor>() {
            @Override
            public int compare(ClassDescriptor o1, ClassDescriptor o2) {
                TestClassesOrder a1 = o1.getTestClass().getDeclaredAnnotation(TestClassesOrder.class);
                TestClassesOrder a2 = o2.getTestClass().getDeclaredAnnotation(TestClassesOrder.class);
                if (a1 == null) {
                    return 1;
                }

                if (a2 == null) {
                    return -1;
                }
                if (a1.value() < a2.value()) {
                    return -1;
                }

                if (a1.value() == a2.value()) {
                    return 0;
                }

                if (a1.value() > a2.value()) {
                    return 1;
                }
                return 0;
            }
        });
    }
}
```

To get it working you need to tell junit which class you would use for ordering descriptors. So you need to create file "" it should be in resources folder. In that file you just need one line with your orderer class:
```
junit.jupiter.testclass.order.default=org.example.tests.AnnotationTestOrderer
```

Now you can use your orderer annotation like Order annotation but on class level:
```
@TestClassesOrder(1)
class Tests {...}

@TestClassesOrder(2)
class MainTests {...}

@TestClassesOrder(3)
class EndToEndTests {...}
```

I hope that this will help someone.


------------
    
    
## Answer \#5

 Vote: -6

Created at 2019-09-10 02:19:07

------------

You can use annotations in Junit 5 to set the test order you wish to use: 

From Junit 5's user guide:
[https://junit.org/junit5/docs/current/user-guide/#writing-tests-test-execution-order](https://junit.org/junit5/docs/current/user-guide/#writing-tests-test-execution-order)

```
import org.junit.jupiter.api.MethodOrderer.OrderAnnotation;
import org.junit.jupiter.api.Order;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.TestMethodOrder;

@TestMethodOrder(OrderAnnotation.class)
class OrderedTestsDemo {

    @Test
    @Order(1)
    void nullValues() {
        // perform assertions against null values
    }

    @Test
    @Order(2)
    void emptyValues() {
        // perform assertions against empty values
    }

    @Test
    @Order(3)
    void validValues() {
        // perform assertions against valid values
    }

}
```


Upgrading to Junit5 can be done fairly easily and the documentation on the link in the beginning of the post contains all the information you might need. 


------------
    
    