
# Post \#72113314 [Link](https://stackoverflow.com/questions/72113314/)

## Fortify's denial of service problem in stringbuilder in java

**Vote**: 3 (575/702) **Views**: 1361 (633/702) 

**Internal ID** \#2-5-359

Created at 2022-05-04 12:51:55

Tags: `java` `memory` `stringbuilder` `fortify`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

Fortify client gives the error and recommendations for string builder,

```
StringBuilder sb=new StringBuilder();    
sb.append(request.getParameter("id"));
sb.append(request.getParameter("name"));
sb.append(request.getParameter("question"));
sb.append(request.getParameter("answer"));
```


User-controlled data is appended to a StringBuilder instance initialized with the default constructor
Appending user-controlled data to a StringBuilder or StringBuffer instance initialized with the default backing character array
size (16) can cause the application to consume large amounts of heap memory while resizing the underlying array to fit user's
data. When data is appended to a StringBuilder or StringBuffer instance, the instance will determine if the backing character
array has enough free space to store the data. If the data does not fit, the StringBuilder or StringBuffer instance will create a new
array with a capacity of at least double the previous array size, and the old array will remain in the heap until it is garbage
collected. Attackers can use this implementation detail to execute a Denial of Service (DoS) attack.

Initialize the StringBuilder or StringBuffer with an initial capacity of the expected appended data size to reduce the number of
times the backing array is resized. Check the size of the data before appending it to a StringBuilder or StringBuffer instance.
```
...
private final int BUFFER_CAPACITY = 5200;
StringBuilder sb = new StringBuilder(BUFFER_CAPACITY);
...
final String lineSeparator = System.lineSeparator();
String[] labels = request.getParameterValues("label");
for (String label : labels) {
if (label.length() + lineSeparator.length() + sb.length()<= sb.capacity()) {
sb.append(label).append(lineSeparator);
} else {
// Handle error
}
}
```



1. If the garbage collectors free it's memory , how the attacker can cause denial of service attack? does it applicable ?
2. My requirement to store the dynamic data range of 0 to 12000 max characters in string builder , so if i use 12000 characters to initialize the string builder , then if my input has only 100 character string means , remaining 11900 length of memory is not required . so in that case do i really needs to set the max of characters in String Builder capacity ? or i can go with length validation for my input parameters only with default constructor?


what will be the better solution for this problem?


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2022-05-13 09:45:35

------------

Answering the two direct questions:
> "If the garbage collectors free it's memory , how the attacker can cause denial of service attack? does it applicable ?"

1. Realistically the attacker can't. Something will almost certainly break first if you are accepting arbitrarily large input data. Garbage collectors in the JVM are very fast and great at cleaning up short-lived data.
2. You could set the initial buffer size to 12K, but that would also put you at risk from an attacker. Suppose they send many very tiny requests, knowing that you will always allocate 12K for each request. Further more, let's say that they can slow down sending the many, tiny requests, so that you can't free up the buffers until the request completes. This DOES put you at risk of OOMing, because the buffers can't be freed until all the data is present.


Without knowing more about the attack vector, fortify is being overly cautious here.   If I were you, I would silence the warning on this method, or just pick a buffer size equals to the StringBuilder default of 16.  The main reason to pick a larger value is if you normally get larger values.


------------
    
    
## Answer \#1

 Vote: 5

Created at 2022-05-07 18:45:42

------------

This is nonsense.
There might be related issue in that, for instance:
- - - 
`StringBuilder` will at least double capacity whenever the current buffer is exhausted to amortise the cost to O(n). The `StringBuilder` may well be larger than the input data. Partly this may be an overallocation, perhaps twice the length of this original, if it is not turned into a `String`. Also the original is typically 1-byte per character, but Java has 2-byte `char`s. If the input is compressed, the explosion in size can be surprising, particularly if multiply compressed.
I recommend having a go at DoSing your own application, although I have seen surprisingly little evidence that application-level DoS attacks are as yet common in the wild.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-05-06 17:13:36

------------

For q2 - guard the size of incoming strings - parameter values here - and like the code snippet in Fortify's suggestion, reject too long input strings. For an expected input of 0 to 12000 chars, I would assign, say, 100 in the sb constructor, and add that suggested check of input string length.
```
void append(StringBuilder sb, String text, int limit) {
  if (sb.length() + text.length() <= limit) {
    sb.append(text);
    return;
  }
  handleError(...);
}

int LIMIT = 12000;

StringBuilder sb=new StringBuilder(100);    
append(sb,request.getParameter("id"), LIMIT);
...
```



------------
    
    