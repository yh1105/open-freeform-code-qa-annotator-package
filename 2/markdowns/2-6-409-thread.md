
# Post \#54137356 [Link](https://stackoverflow.com/questions/54137356/)

## How to fix 'Value cannot be null. Parameter name: Key' error in c#

**Vote**: 7 (449/702) **Views**: 42590 (186/702) 

**Internal ID** \#2-6-409

Created at 2019-01-10 21:46:26

Tags: `c#`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I was using Dictionary in C# and wanted to display a Key inputted by the user and display that key and its corresponding value if that key is present in the dictionary. Also, continue reading the lines until there is no more input.

```
using System;
using System.Collections.Generic;
using System.IO;

class Solution 
{
    static void Main(String[] args) 
    {
        string number = Console.ReadLine();
        int n;
        Int32.TryParse(number, out n);

        var phoneBook = new Dictionary<string, string>();

        for(int i = 0; i < n; i++)
        {
            string name = Console.ReadLine();
            string phoneNumber = Console.ReadLine();
            phoneBook.Add(name, phoneNumber);
        }

        foreach (var pair in phoneBook)
        {
            string name = pair.Key;
            string phoneNumber = pair.Value;
        }


        string x = Console.ReadLine();
        if(!phoneBook.ContainsKey(x))
        {
            Console.WriteLine("Not Found");
        }
        else
        {
            string result = phoneBook[x];
            Console.Write(x);
            Console.Write("=");
            Console.Write(result);
        }
    }
}


Error message:

    Unhandled Exception:
    System.ArgumentNullException: Value cannot be null.
    Parameter name: key
      at System.Collections.Generic.Dictionary2[TKey,TValue].FindEntry (TKey key) <0x7fb28d7c9320 + 0x0023c> in <3833a6edf2074b959d3dab898627f0ac>:0 
      at System.Collections.Generic.Dictionary2[TKey,TValue].ContainsKey (TKey key) <0x7fb28d7c8cd0 + 0x00009> in <3833a6edf2074b959d3dab898627f0ac>:0 
      at Solution.Main (System.String[] args) [0x00096] in solution.cs:30 
    [ERROR] FATAL UNHANDLED EXCEPTION: System.ArgumentNullException: Value cannot be null.
    Parameter name: key
      at System.Collections.Generic.Dictionary2[TKey,TValue].FindEntry (TKey key) <0x7fb28d7c9320 + 0x0023c> in <3833a6edf2074b959d3dab898627f0ac>:0 
      at System.Collections.Generic.Dictionary2[TKey,TValue].ContainsKey (TKey key) <0x7fb28d7c8cd0 + 0x00009> in <3833a6edf2074b959d3dab898627f0ac>:0 
      at Solution.Main (System.String[] args) [0x00096] in solution.cs:30
```



----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2019-01-12 14:44:36

------------

```
using System;
using System.Collections.Generic;
using System.IO;
class Solution 
{
    static void Main(String[] args) 
    {   
        var phoneBook = new Dictionary<string, string>();

        int n = Convert.ToInt32(Console.ReadLine());

        for(int i = 0; i < n; i++)
        {
            string[] record = Console.ReadLine().Split();
            string name = record[0];
            string phoneNumber = record[1];
            phoneBook.Add(name, phoneNumber); 
        }

        string x;
        while((x = Console.ReadLine()) != null)
        {
            if(phoneBook.ContainsKey(x))
            {
                Console.WriteLine(x + "=" + phoneBook[x]);
            }
            else
            {
                Console.WriteLine("Not found");
            }
        }
    }
}
```


This is the final solution which worked for me.
Changes:
1. Added a `while` loop condition to avoid null search string.
2. Removed the `foreach` loop which was not needed.
3. In `for` loop for taking dictionary input used the array to store two strings in each record/entry of the dictionary.


------------
    
    
## Answer \#1

 Vote: 6

Created at 2019-01-10 21:56:39

------------

Change the line: 

```
if(!phoneBook.ContainsKey(x))
```


to:

```
if(x == null || !phoneBook.ContainsKey(x))
```


The error is pointing to that line of code, I believe. Please add a comment that points to the faulting line of code (to make it easier for us), and please reformat your exception text as code (with the back-ticks removed) so that the stack is readable.


------------
    
    
## Answer \#2

 Vote: 3

Created at 2019-01-10 22:00:08

------------

The short answer is that one of your objects is `null`. Most likely one of the variables being set from a call to `Console.ReadLine`.

It is a common belief that `Console.ReadLine` cannot return `null`. This is false. It will return `null` if `Control-Z` is entered at the console.

As such, you need to check for `null` before interacting with the `Dictionary` (e.g. calls to `Add` or `ContainsKey`). Alternatively, change your `ReadLine` calls from:

```
string x = Console.ReadLine();
```


to:

```
string x = Console.ReadLine() ?? string.Empty;
```


to ensure the variable  be `null`.

`TryGetValue``ContainsKey``[]`


------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-04-04 01:43:43

------------

I had this exception when trying to  my dbContext on ef core and asp.net core version 2.2. I was running my project on visual studio 2022.
finally, I found out I should install  and . and my problem was solved!


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-09-05 12:57:10

------------

This error can sometimes be experienced because of the relationships of its related tables. To avoid this, typing [jsonignore] above the relation lines may solve your problem.


------------
    
    