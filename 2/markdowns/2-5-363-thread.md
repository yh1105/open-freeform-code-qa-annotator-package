
# Post \#57822861 [Link](https://stackoverflow.com/questions/57822861/)

## What is the difference between @RequiredArgsConstructor(onConstructor = @__(@Inject)) and @RequiredArgsConstructor?

**Vote**: 15 (318/702) **Views**: 21605 (285/702) 

**Internal ID** \#2-5-363

Created at 2019-09-06 13:21:23

Tags: `java` `spring` `lombok` `inject`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

What is the difference between Lombok's 

```
@RequiredArgsConstructor
```


and 

```
@RequiredArgsConstructor(onConstructor = @__(@Inject))
```


I know that `RequiredArgsConstructor` injects all the final dependencies in constructor only.


----------
        
## Answer \#0

**Accepted** Vote: 19

Created at 2019-09-06 13:26:31

------------

```
@RequiredArgsConstructor
class MyClass {
  private final DependencyA a;
  private final DependencyB b;
}
```

will generate
```
public MyClass(DependencyA a, DependencyB b) {
  this.a = a;
  this.b = b;
}
```

while
```
@RequiredArgsConstructor(onConstructor = @__(@Inject))
class MyClass {
  private final DependencyA a;
  private final DependencyB b;
}
```

will generate
```
@Inject
public MyClass(DependencyA a, DependencyB b) {
  this.a = a;
  this.b = b;
}
```

From JDK 8 onwards, the syntax `@RequiredArgsConstructor(onConstructor_ = {@Inject})` is also accepted.
> I know `RequiredArgsConstructor` injects all the final dependencies.
All  dependencies which consists of `final` and `@NonNull` fields.


------------
    
    
## Answer \#1

 Vote: 7

Created at 2020-09-14 00:17:50

------------

The answers given have explained clearly what is the difference as asked by the OP. But i also feel that knowing  `@RequiredArgsConstructor(onConstructor = @__(@Inject))` instead of `@RequiredArgsConstructor`? is also important. If you are interested, read on...
In short, when Spring construct your beans (the classes annotated with @Component or related @Controller, @Service, @Repository - they all have @Component + extra functionality), Spring will need to look at the class constructor, to construct it. If you have only 1 constructor in your class, fine, no confusion, you only need `@RequiredArgsConstructor`.
What if you have 2 or more constructors? Which one does Spring use to construct your bean? Enter [Lombok's](https://projectlombok.org/features/constructor) `@RequiredArgsConstructor(onConstructor = @__(@Inject))` or  `@RequiredArgsConstructor(onConstructor = @__(@Autowired))`. As the annotation's attribute , it puts     to tell Spring to use that constructor at construction time.
That's it!
P.S I recommend [this article](https://www.baeldung.com/spring-injection-lombok) if you want to read more about it.


------------
    
    
## Answer \#2

 Vote: 6

Created at 2019-09-06 13:25:55

------------

The second one will put the annotations you mention on the generated constructor.

For example, this: `@RequiredArgsConstructor(onConstructor = @__(@Inject))`
will generate a constructor annotated with `@Inject`


------------
    
    
## Answer \#3

 Vote: 1

Created at 2020-09-09 17:09:08

------------

I will explain those annotations with simple examples


```
public class Item {
    private final String price;
    public Item(String price) {
        this.price=price;
    }
}
```

If you have a field or fields which is final (not null) then go with @RequiredArgsConstructor

```
@RequiredArgsConstructor
public class Item {
    private final String price;
}
```



```
public class Item {
    private final Shop shop;
    public Item(Shop shop) {
        this.shop=shop;
    }
}
```

If you have a field with your own type and bean instance is created in the configuration class. Then you can inject that bean instance to the shop filed of Item class using constructor injection with the help of  @RequiredArgsConstructor(onConstructor = @__(@Inject))
But my recommendation is don't use  @RequiredArgsConstructor(onConstructor = @__(@Inject))
when you are working with Spring. you can overcome it in 2 ways.
1.Using @Autowired Annotation
```
public class Item {
    private final Shop shop;
    @Autowired
    public Item(Shop shop) {
        this.shop=shop;
    }
}
```

2.Using @AllArgsConstructor Annotation
```
@AllArgsConstructor
public class Item {
    private final Shop shop;
}
```

I will add an extra point for someone's assist


```
public class Item {
    private String price;
    public Item(String price) {
        this.price=price;
    }
}
```

if you have a field or Fields which is not final (nullable) then go with @AllArgsConstructor.

```
@AllArgsConstructor
public class Item {
    private String price;
}
```


 can be used with both final and non-final fields.
 can not be used with the non-final field.


------------
    
    