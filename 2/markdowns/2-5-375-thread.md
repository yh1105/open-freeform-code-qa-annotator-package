
# Post \#72520878 [Link](https://stackoverflow.com/questions/72520878/)

## Java trim() : Cleanest way to check null string before trimming?

**Vote**: 4 (532/702) **Views**: 4337 (537/702) 

**Internal ID** \#2-5-375

Created at 2022-06-06 16:43:55

Tags: `java` `string` `spring-boot` `string-formatting` `trim`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I use `trim()` method in order to trim leading and trailing whitespaces in some of the string field.
```
siteRequest.getName().trim();
```

However, when string field is null, it throws exception as expected. I can check the values before trimming as shown below:
```
siteRequest.getName() ? siteRequest.getName() : siteRequest.getName().trim();
```

However, if it is possible I would prefer a cleaner way so that several people had already faced this problem. Any suggestion with a smarter approach?


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2022-06-06 16:51:54

------------

I like the idea from @Sebastiaan van den Broek but would prefer not to use the library and therefore look up its [implementation](https://commons.apache.org/proper/commons-lang/javadocs/api-3.5/src-html/org/apache/commons/lang3/StringUtils.html):
```
// Trim
//-----------------------------------------------------------------------
/**
 * <p>Removes control characters (char &lt;= 32) from both
 * ends of this String, handling {@code null} by returning
 * {@code null}.</p>
 *
 * <p>The String is trimmed using {@link String#trim()}.
 * Trim removes start and end characters &lt;= 32.
 * To strip whitespace use {@link #strip(String)}.</p>
 *
 * <p>To trim your choice of characters, use the
 * {@link #strip(String, String)} methods.</p>
 *
 * <pre>
 * StringUtils.trim(null)          = null
 * StringUtils.trim("")            = ""
 * StringUtils.trim("     ")       = ""
 * StringUtils.trim("abc")         = "abc"
 * StringUtils.trim("    abc    ") = "abc"
 * </pre>
 *
 * @param str  the String to be trimmed, may be null
 * @return the trimmed string, {@code null} if null String input
 */
public static String trim(final String str) {
    return str == null ? null : str.trim();
}
```

From my point of view there is no better way to implement it. Using Optionals is not an option. Therefore, the original solution idea in the question is confirmed.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2022-06-06 16:46:39

------------

Typically this is done with the Apache Commons library's [trim](https://commons.apache.org/proper/commons-lang/apidocs/org/apache/commons/lang3/StringUtils.html#trim-java.lang.String-) method. This library is so commonplace that it's basically part of Java. It will return `null` for `null` Strings. E.g. `StringUtils.trim("myString ");` would return `"myString"`


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-06-06 17:00:54

------------

Unfortunately Java still lacks a null-safe navigation operator.  The closest way to replicate it would be with `Optional`:
```
String trimmed = Optional.ofNullable(untrimmed).map(String::trim).orElse(null);
```

However it's easy to see this does not beat a simple ternary most of the time. Where it does shine is if you have a long chain of potential nullables, and want to avoid nested ternaries:
```
String trimmed = Optional.ofNullable(request)
 .map(Request::getName)
 .map(String::trim)
 .orElse(null);
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-06-06 16:58:41

------------

```
String name = siteRequest.getName();

name == null ? name : 
name.trim();
```

or
```
String name = siteRequest.getName();

if(name != null)
  name.trim();
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-06-06 18:16:39

------------

As answered above simple use a util method to do this. Ideally, [null conditional](https://stackoverflow.com/questions/4390141/java-operator-for-checking-null-what-is-it-not-ternary)  operator would have made it better, but that doesn't exist in java
But create a util method. flavor it as you need e.g from the above answers
you can do
```
public static String trim(final String str) {
    return str == null ? null : str.trim();
}
```

or if you want to return a default values
```
public static String trim(final String str) {
    return str == null ? "default_value" : str.trim();
}
```

or
```
public static String trim(final String str, String defaultValue) {
    return str == null ? defaultValue : str.trim();
}
```

or if you want to use options
```
public static Option<String> trim(final String str) {
    return str == null ? Optional.nullable() : Optional.of(str.trim());
}
```

It purely depends on what you need and how your source code has been written.


------------
    
    