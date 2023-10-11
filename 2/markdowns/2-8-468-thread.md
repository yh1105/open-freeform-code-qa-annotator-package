
# Post \#67733156 [Link](https://stackoverflow.com/questions/67733156/)

## convert int to ascii - more than one character in rune literal

**Vote**: 0 (696/702) **Views**: 5318 (513/702) 

**Internal ID** \#2-8-468

Created at 2021-05-28 05:02:45

Tags: `go`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------

How do I convert into to its ASCII?
In java it's `System.out.println((char)(49)); //gives 1`
I tried
```
a := '42'
fmt.Println(rune(a))
```

I get `more than one character in rune literal`


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2021-05-28 05:19:44

------------

The reason you're getting this error is in this variable:
```
a := '42'
```

A byte literal may only contain one character, use this instead;
```
a := byte(42)
```

Edit:
Use  `string(a)` to get the expected results, like boo said.


------------
    
    
## Answer \#1

 Vote: 4

Created at 2021-05-28 05:31:46

------------

Use a [string conversion](https://golang.org/ref/spec#Conversions_to_and_from_a_string_type) to convert an ASCII numeric value to a string containing the ASCII character:
```
fmt.Println(string(49)) // prints 1
```

The `go vet` command warns about the `int` to `string` conversion in the this code snippet because the conversion is commonly thought to create a decimal representation of the number. To squelch the warning, use a `rune` instead of an `int`:
```
fmt.Println(string(rune(49))) // prints 1
```

[Run it on the playground](https://play.golang.org/p/eyDZNATLGPS).
This works for any rune value, not just the ASCII subset of runes.
Another option is to create a slice of bytes with the ASCII value and convert the slice to a string.
```
b := []byte{49}
fmt.Println(string(b))  // prints 1
```

[Run it on the playground](https://play.golang.org/p/LyCS9S6Kk5b)
A variation on the previous snippet that works on all runes is:
```
b := []rune{49}
fmt.Println(string(b))  // prints 1
```



------------
    
    
## Answer \#2

 Vote: 3

Created at 2022-05-27 11:39:26

------------

```
fmt.Printf("%c", 49)
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-04-21 05:52:37

------------

I suppose to do like this, in a very simple term explaining.
```
i := 1
fmt.Println("number value", i)

s := strconv.Itoa(i)
fmt.Println("string value", s)

a := []rune(s)[0]
fmt.Println("ascii value", a)

n := string(a)
fmt.Println("back to string value", n)
```

and output is following:
```
number value 1
string value 1
ascii value 49
back to string value 1
```

I know this is not the best one answer, but still it works fine without any issue.


------------
    
    