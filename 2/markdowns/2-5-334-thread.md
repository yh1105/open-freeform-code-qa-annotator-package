
# Post \#67691575 [Link](https://stackoverflow.com/questions/67691575/)

## When should you use @Configuration instead of @Service/@Controller/@Component for defining beans

**Vote**: 3 (575/702) **Views**: 3177 (563/702) 

**Internal ID** \#2-5-334

Created at 2021-05-25 15:57:58

Tags: `java` `spring` `spring-boot`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I am trying to understand the Spring Framework better and my question is basically:
What is the benefit of the @Configuration annotation compared to @Service/@Controller/@Repository (or @Component if it is none of the other 3)
As I understand the @Configuration is meant to be a source of beans, but by just marking my classes with for example @Service it becomes a bean and will be picked up by the component scanning?
As I understand it I could either have a @Configuration class that has methods that create a couple of beans (say some services) or just have the classes annotated with @Service directly.
What would be the benefit of having the @Configuration class? Or is the @Configuration mostly useful for some edge cases that I have missed?
I read somewhere that the @Value annotation can be used in a @Configuration annotated class, but I can see in our codebase that we use it in other places, that are not @Configuration marked, so that does not seem to be required
I've been told there is a thread here:
[What is the difference between @Configuration and @Component in Spring?](https://stackoverflow.com/questions/39174669/what-is-the-difference-between-configuration-and-component-in-spring)
But I feel that it only starts explaining what the difference is between the annotations, not WHEN/What the benefit of using one over the other is.
Thanks!


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2021-05-26 04:45:36

------------


### short answer


- - 

### long answer


In spring all is related to dependency injection. Spring allow us to register our awesome classes manually or automatically.
So if you have a java class (Foo.java) with an awesome method (doSomething) that you need to use in any part of your spring project and you want to avoid the manually instantation, you need to annotate this java class with .
```
@Component
public class Foo {
....
}
```

After that you can use Foo.java in any class of you your spring project:
```
public class Bar { 
....
@Autowire
Foo foo;

public void hello( ) {
  foo.doSomething
}
```

But if Foo.java is inside of a library, is not compatible with spring, is an old library or just a simple instantation is not enough,  and  comes to rescue
```
@Configuration
public class Application { 
....
@Bean
public Foo configureFoo() {
  //load some crazy configuration
  //some crazy algorithm
  return new Foo(crazyConfig, param1,param2,etc)
}
```



------------
    
    
## Answer \#1

 Vote: 4

Created at 2021-05-26 03:39:08

------------

I'm answering based on my experience at work, so here it goes

### @Configuration


When you utilize a 3rd party library (most of the time we do) and we need to have that library as a singleton, then we declare it as bean using `@Bean` inside our `@Configuration`, because we can't annotate a 3rd party library using `@Component` right?

### @Component (and other stereotype annotations)


Using this annotation make your codes more readable and simple. Because there are some cases that your class will have many dependencies, defining it using `@Bean` inside `@Configuration` class will need you to inject those dependencies "manually" one by one through -for example- constructor.
In my opinion, as long as you can achieve your objective by marking your class with `@Component` or other stereotype annotation, you should do that for the sake of readability and maintainability.


------------
    
    
## Answer \#2

 Vote: 3

Created at 2021-05-25 16:12:23

------------

> What would be the benefit of having the @Configuration class? Or is the @Configuration mostly useful for some edge cases that I have missed?
I think what you're asking is the difference between `@Bean` annotated methods, and annotating a `@Component` class. In this case, [Spring: @Component versus @Bean](https://stackoverflow.com/questions/10604298/spring-component-versus-bean) should really help clarify that. The main difference is `@Bean` allows you to decouple the bean definition from the class itself. Your class can be a simple POJO with no Spring annotation/code in it. It can also be a third-party class.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-05-26 04:02:49

------------

Using @Configuration makes the project much more manageable - because you can always quickly refer to the one (or several) dedicated places to figure out the whole picture of dependencies between your beans, instead of hopping back and forth between classes marked with @Component-family annotations.
It is not a big difference for simple projects, but in case of complex projects and complex beans it helps a lot, especially when you have a non-trivial logic of instantiation.
Plus, you might narrow component scan scope (so startup time) using a limited number of well-know configuration classes.
Additionally, @Component family beans most often are injected as @Autowired,
, while @Configuration is dealing mostly with constructor injection
Short, but very good related article:
[https://dzone.com/articles/spring-di-patterns-the-good-the-bad-and-the-ugly](https://dzone.com/articles/spring-di-patterns-the-good-the-bad-and-the-ugly)


------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-05-26 04:25:02

------------

I had the same question in my mind before and searched a lot about it. According to my searches and own experiences, I think below link can help you.
[https://www.javahabit.com/2018/12/30/spring-bean-vs-component/](https://www.javahabit.com/2018/12/30/spring-bean-vs-component/)

A simple way to decide between `@Component` and `@Bean` is that
- `@Bean`- - `@Bean`
Hope to be helpful.


------------
    
    