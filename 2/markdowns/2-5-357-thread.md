
# Post \#67961338 [Link](https://stackoverflow.com/questions/67961338/)

## Why people uses @Autowired for constructor injection

**Vote**: 1 (672/702) **Views**: 2627 (584/702) 

**Internal ID** \#2-5-357

Created at 2021-06-13 18:13:51

Tags: `java` `spring` `dependency-injection` `autowired`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I have seen many people using `@Autowired` annotation in constructor to inject dependencies like as shown below
```
@Service
public class Employee {

 private EmployeeService employeeService;
 
 @Autowired
 public Employee(EmployeeService employeeService) {
   this.employeeService = employeeService;
 }
 :
 :
}
```

as per my knowledge nowadays Spring is so advanced and the Spring constructor/setter DI works even without `@Autowired` like as shown below
```
@Service
public class Employee {

 private EmployeeService employeeService;

 public Employee(EmployeeService employeeService) {
   this.employeeService = employeeService;
 }
 :
 :
}
```

Like to know is there any reason why people annotate constructor with `@Autowired` annotation to inject dependencies.
Can someone please help me on this


----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2021-06-13 18:18:32

------------

There is no technical reason to do this (except if you have multiple constructors etc.). The most likely reason is, that people have learned to use it, when Spring didn't support the autodetection of the constructor and when it was still necessary, and as the saying goes, old habits die hard. Also, if you search for any examples, you still find a lot of tutorials, where the annotation is used, so that is another reason. It's the same as with the `@Repository` annotation on Spring Data interfaces. That annotation is just plain wrong there, but there are a lot of questions on this site, where people added this in their code.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-06-13 18:22:17

------------

Yes, you're right - you don't need use annotation `@Autowired` constructor with DI.
I think is couple of reasons:
- - - - 


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-06-13 18:30:26

------------

Personally, I use `@Autowired` annotation just to enhance code readability so that other developers can read and understand code more easily.


------------
    
    