
# Post \#71376627 [Link](https://stackoverflow.com/questions/71376627/)

## In Go generics, how to use a common method for types in a union constraint?

**Vote**: 9 (409/702) **Views**: 6741 (488/702) 

**Internal ID** \#2-8-463

Created at 2022-03-07 05:05:35

Tags: `go` `generics`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------

I'm trying to understand the usage of the type union constraint in Go generics (v1.18). Here is the code I tried:
```
type A struct {
}

type B struct {
}

type AB interface {
    *A | *B
}

func (a *A) some() bool {
    return true
}

func (b *B) some() bool {
    return false
}

func some[T AB](x T) bool {
    return x.some()   // <- error
}
```

The compiler complains:
> `x.some` undefined (type `T` has no field or method some)
Why is that? If I cannot use a shared method of type `*A` and `*B`, what's the point of defining types union `*A | *B` at all?
(Apparently I can define an interface with the shared method and directly use that. But in my particular use case I want to restrict to the certain types explicitly.)


----------
        
## Answer \#0

**Accepted** Vote: 9

Created at 2022-03-07 08:40:33

------------

Add the method to the interface constraint, without forgoing generics:
```
type AB interface {
    *A | *B
    some() bool
}

func some[T AB](x T) bool {
    return x.some()   // works
}
```

This restricts `T` to types that are either `*A` or `*B` and declare `some() bool` method.
However, as you already found out, this is a workaround. You are right that it should work with the type union alone. It's a limitation of Go 1.18. The confusing part is that the language specifications still seem to support your theory ([Method sets](https://tip.golang.org/ref/spec#Method_sets)):
> The method set of an interface type is the intersection of the method sets of each type in the interface's type set (the resulting method set is usually just the set of declared methods in the interface).
This limitation appears to be documented only in the [Go 1.18 release notes](https://tip.golang.org/doc/go1.18):
> The current generics implementation has the following limitations:[...]
The Go compiler currently only supports calling a method `m` on a value `x` of type parameter type `P` `m``P`. [...] even though `m` might be in the method set of `P` by virtue of the fact that all types in `P` implement `m`. We hope to remove this restriction in Go 1.19.
The relevant issue in the Go tracker is [#51183](https://github.com/golang/go/issues/51183), with [Griesemer's confirmation](https://github.com/golang/go/issues/51183#issuecomment-1049181719) and the decision to leave the language specifications as is, and document the restriction.


------------
    
    
## Answer \#1

 Vote: 4

Created at 2022-03-07 06:53:19

------------

Change the declaration of `AB` to
```
type AB interface {
    *A | *B
    some() bool
}
```

In Generic Go, constraints are interfaces. A type argument is valid if it implements its constraints.
Please watch the Gophercon videos on Generics for a better understanding:
- [Gophercon 2021: Robert Griesemer & Ian Lance Taylor - Generics!](https://youtu.be/Pa_e9EeCdy8)- [Gophercon 2020: Robert Griesemer - Typing [Generic] Go](https://youtu.be/TborQFPY2IM)
To ensure that I understood your question please run the code snippet below in [Go Playground in “Go Dev branch” mode](https://go.dev/play/?v=gotip):
```
// You can edit this code!
// Click here and start typing.
package main

import "fmt"

type A struct {
}

type B struct {
}

type C struct{}

type AB interface {
    *A | *B
    some() bool
}

func (a *A) some() bool {
    return true
}

func (b *B) some() bool {
    return false
}

func (c *C) some() bool {
    return false
}

func some[T AB](x T) bool {
    return x.some()
}

func main() {
    p := new(A)
    fmt.Println(some(p))

    //uncomment the lines below to see that type C is not valid
    //q := new(C)
    //fmt.Println(some(q))

}
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-03-07 06:26:41

------------

I think the old interface{} is enough to do this.
Like this:
```
type AB interface {
    some() bool
}
```

But if you want to use generic, you must change the type first.
Like this:
```
func some[T AB](x T) bool {
    if a, ok := interface{}(x).(*A); ok {
        return a.some()
    }

    return (*B)(x).some()
}
```



------------
    
    