
# Post \#63852397 [Link](https://stackoverflow.com/questions/63852397/)

## Arrays without fixed length in golang

**Vote**: 6 (473/702) **Views**: 8487 (448/702) 

**Internal ID** \#2-8-452

Created at 2020-09-11 18:24:11

Tags: `go`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------

I have recently started with `golang` and was working with `arrays` and came across a situation where I did not have the number of elements. Is there a way to initialize a array without a size and then append elements in the end? Something like there in other programming languages like `c++`, `javascript` where there are `vectors` and `arrays` that can be used and we can add elements by functions like `push_back` or `push`. Is there a way that we can do this or is there a library in which we can do this? Thank you!


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2020-09-11 18:32:18

------------

You can use `slice` for your purpose.
```
array := make([]int, 0)
array = append(array, 1)
array = append(array, 2)
```

Here, array is a slice of `int` type data and initially of size `0`. You can append `int` type data by `append(array, <int-type-data>)`.


------------
    
    
## Answer \#1

 Vote: 11

Created at 2020-09-11 18:30:28

------------

```
a := []int{}
a = append(a, 4)
fmt.Println(a)
```



------------
    
    
## Answer \#2

 Vote: 3

Created at 2020-09-11 18:28:44

------------

With Golang, Arrays always have a fixed length:
> In Go, an array is a numbered sequence of elements of a specific length.
(Source: [https://gobyexample.com/arrays](https://gobyexample.com/arrays))
If you want the flexibility of a variable length, you'll probably want to use a Slice instead:
> Slices are a key data type in Go, giving a more powerful interface to sequences than arrays.
(Source: [https://gobyexample.com/slices](https://gobyexample.com/slices))
[This post on the go blog](https://blog.golang.org/slices-intro) (though old) has a nice overview of the two data types.


------------
    
    