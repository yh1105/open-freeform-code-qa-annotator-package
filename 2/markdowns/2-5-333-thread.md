
# Post \#69823077 [Link](https://stackoverflow.com/questions/69823077/)

## How to remove object if one variable is false in list using java streams

**Vote**: 0 (696/702) **Views**: 1315 (636/702) 

**Internal ID** \#2-5-333

Created at 2021-11-03 10:12:26

Tags: `java` `java-stream`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I am using java 8
I have a model class like
```
class Student{
    String name;
    List<Subject> subjects1;
    List<Subject> subjects2;
    List<Subject> subjects3;
    // getters & setters
}

class Subject{
    String sub;
    Integer marks;
    boolean status;
    // getters & setters
}
```

`status` may be true or false.
Now if the status is false then I have to remove those objects from the subjects list
How to do it in Streams?
Thanks in advance.


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2021-11-03 10:31:50

------------

To remove objects, you can directly use removeIf method in java 8.
`student.getSubjects().removeIf(subject -> !subject.isStatus());`


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-11-03 10:15:40

------------

Here's how you do it:
```
List<Student> studentsWithTrueStatus = students
  .stream()
  .filter( s -> s.status )
  .collect(Collectors.toList());
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-11-03 11:53:26

------------

If there are multiple lists of `Subject` in `Student` class, they may be joined using `Stream.of` and then applying `removeIf` in `forEach`:
```
// Some utility class MyClass
public static void removeSubjects(Student student) {
    Stream.of(student.getSubjects1(), student.getSubjects2(), student.getSubjects3())
        .filter(Objects::nonNull) // filter out null lists if any
        .forEach(subjects -> subjects.removeIf(sub -> !sub.isStatus()));
}
```


---


If there is a list of students, then the reference to the above implemented method may be used for each student:
```
List<Students> students = Arrays.asList(...); // setup students list

students.forEach(MyClass::removeSubjects);
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-11-03 10:53:09

------------

First ensure the list of subjects is not null or empty. Then utilise  method.
```
List<Subject> subjectList = student.getSubjects();

if((null != subjectList) && (subjectList.size() > 0)) {
  subjectList.removeIf(sub -> !(sub.isStatus()));
}
```



------------
    
    