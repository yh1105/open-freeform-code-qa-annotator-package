
# Post \#61104708 [Link](https://stackoverflow.com/questions/61104708/)

## typeof in Java 8

**Vote**: 18 (275/702) **Views**: 93235 (91/702) 

**Internal ID** \#2-5-379

Created at 2020-04-08 15:47:09

Tags: `java` `java-8` `primitive` `object-type`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

If we want to check the datatype of variable in javascript, we can use `typeof` operator .


## Consider this snippet



```
var c = 'str' ;
console.log(typeof(c)); // string
c = 123 ;
console.log(typeof(c)); // number
c =  {} ;
console.log(typeof(c)) ; // object
```


I want to achieve the same functionality in `Java 8` . Java does not have typeof operator but there's the `instanceof` operator to check the types.

```
System.out.println("str" instanceof String);  // true 

Integer a  = 12 ;
System.out.println(a instanceof Integer);


Float f = 12.3f
System.out.println(f instanceof Float); // true
```


Can we do any better ? Plus `instanceof` does not support primitive types .

Is there any approaches in `java 8` ? Any relevant approaches will be appreciated. 


----------
        
## Answer \#0

**Accepted** Vote: 21

Created at 2020-04-08 18:02:47

------------

You can use the [getClass()](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html#getClass--) method to get the type of the object you are using:

```
Object obj = null;
obj = new ArrayList<String>();
System.out.println(obj.getClass());

obj = "dummy";
System.out.println(obj.getClass());

obj = 4;
System.out.println(obj.getClass());
```


This will generate the following output:

```
class java.util.ArrayList
class java.lang.String
class java.lang.Integer
```


As you see it will show the type of the object which is referenced by the variable, which might not be the same as the type of the variable (`Object` in this case).

For primitive types there is no solution available as the problem of knowing the type stored in a variable does not exist. A primitive type variable can hold  values of that type. As you have to define the variable (or parameter) somewhere you already know the type of it and the values it can hold. There is no "base" type for primitive values which you can use similar to the `Object` type, which is the base type for all objects in java.


------------
    
    
## Answer \#1

 Vote: 7

Created at 2020-04-09 07:26:22

------------

Thanks to @Progman for `getClass()` method .
```
class check{

    static Class typeof(Integer a)
    {
        return a.getClass();
    }
    static Class typeof(Character c)
    {
        return c.getClass();
    }
    static Class typeof(Float f)
    {
        return f.getClass();
    }
    static Class typeof(Double d)
    {
        return d.getClass();
    }
    static Class typeof(Long l)
    {
        return l.getClass();
    }
    static Class typeof(String s)
    {
        return s.getClass();
    }

}
```

So now we check both primitive and non- primitive types
> `check.typeof(12) ; // class java.lang.Integer``check.typeof(12.23f) ; // class java.lang.Float``check.typeof('c') ; // class java.lang.Character``check.typeof("str") ; // class java.lang.String`


------------
    
    
## Answer \#2

 Vote: 3

Created at 2022-03-06 14:27:14

------------

This is the closest functionality example I could find in Java (10 or higher) to your JavaScript example with the `typeof` operator and `var` (local variable declaration only in Java).
It deals with primitive data type and implies casting the primitive to Object first and then calling the `.getClass().getSimpleName()` method on the respective Object.
References: [Use getClass().getSimpleName() to Check the Type of a Variable in Java](https://www.delftstack.com/howto/java/how-to-check-type-of-a-variable-in-java/)
```
public class Main {
public static void main(String[] args) {
    var myNum = 72;
    System.out.println(((Object) myNum).getClass().getSimpleName()); // Integer
    var myInput = 10.52f;
    System.out.println(((Object) myInput).getClass().getSimpleName()); // Float
    var yetAnotherInput = 0.345678923;
    System.out.println(((Object) yetAnotherInput).getClass().getSimpleName()); // Double
    var otherInput = 1_234_567_764_456_211L;
    System.out.println(((Object) otherInput).getClass().getSimpleName()); // Long
    var myName = "John";
    System.out.println(((Object) myName).getClass().getSimpleName()); // String
    var myLetter = 'j';
    System.out.println(((Object) myLetter).getClass().getSimpleName()); // Character
    var myAnswer = true;
    System.out.println(((Object) myAnswer).getClass().getSimpleName()); // Boolean
}
```

}


------------
    
    