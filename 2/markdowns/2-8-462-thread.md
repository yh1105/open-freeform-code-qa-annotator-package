
# Post \#69573113 [Link](https://stackoverflow.com/questions/69573113/)

## How can I instantiate a non-nil pointer of type argument with generic Go?

**Vote**: 16 (306/702) **Views**: 6071 (502/702) 

**Internal ID** \#2-8-462

Created at 2021-10-14 15:09:03

Tags: `go` `pointers` `generics`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------

Now that type parameters are available on `golang/go:master`, I decided to give it a try. It seems that I'm running into a limitation I could not find in the [Type Parameters Proposal](https://go.googlesource.com/proposal/+/refs/heads/master/design/43651-type-parameters.md). (Or I must have missed it).
I want to write a function which returns a slice of values of a generic type with the constraint of an interface type. If the passed type is an implementation with a pointer receiver, how can we instantiate it?
```
type SetGetter[V any] interface {
    Set(V)
    Get() V
}

// SetGetterSlice turns a slice of type V into a slice of type T,
// with T.Set() called for each entry in values.
func SetGetterSlice[V any, T SetGetter[V]](values []V) []T {
    out := make([]T, len(values))

    for i, v := range values {
        out[i].Set(v) // panic if T has pointer receiver!
    }

    return out
}
```

When calling the above `SetGetterSlice()` function with the `*Count` type as `T`, this code will panic upon calling `Set(v)`. ([Go2go playground](https://go2goplay.golang.org/p/h-xUzVDIy0p)) To no surprise, as basically the code created a slice of `nil` pointers:
```
// Count implements SetGetter interface
type Count struct {
    x int
}

func (c *Count) Set(x int) { c.x = x }
func (c *Count) Get() int  { return c.x }

func main() {
    ints := []int{1, 2, 3, 4, 5}

    sgs := SetGetterSlice[int, *Count](ints)
    
    for _, s := range sgs {
        fmt.Println(s.Get())
    }
}
```


## Variations of the same problem


This ideas won't work, and I can't seem to find any simple way to instantiate the pointed value.

1. out[i] = new(T) will result in a compile failure, as it returns a *T where the type checker wants to see T.
2. Calling *new(T), compiles but will result in the same runtime panic because new(T) returns **Count in this case, where the pointer to Count is still nil.
3. Changing the return type to a slice of pointer to T will result in a compile failure:


```
func SetGetterSlice[V any, T SetGetter[V]](values []V) []*T {
    out := make([]*T, len(values))

    for i, v := range values {
        out[i] = new(T)
        out[i].Set(v) // panic if T has pointer receiver
    }

    return out
}

func main() {
    ints := []int{1, 2, 3, 4, 5}

    SetGetterSlice[int, Count](ints)
    // Count does not satisfy SetGetter[V]: wrong method signature
}
```


## Workaround


The only solution I found until now, is to require a [constructor function](https://go2goplay.golang.org/p/-FEF-zpzBqT) to be passed to the generic function. But this just feels wrong and a bit tedious. Why would this be required if `func F(T interface{})() []T` is perfectly valid syntax?
```
func SetGetterSlice[V any, T SetGetter[V]](values []V, constructor func() T) []T {
    out := make([]T, len(values))

    for i, v := range values {
        out[i] = constructor()
        out[i].Set(v)
    }

    return out
}

// ...
func main() {
    ints := []int{1, 2, 3, 4, 5}

    SetGetterSlice[int, *Count](ints, func() *Count { return new(Count) })
}
```


## Summary


My questions, in order of priority:

1. Am I overlooking something obvious?
2. Is this a limitation of generics in Go and this is as good as it gets?
3. Is this limitation known or should I raise an issue at the Go project?




----------
        
## Answer \#0

**Accepted** Vote: 16

Created at 2021-10-14 18:33:12

------------

Basically you have to add one more type parameter to the constraint to make `T` convertible to its pointer type. In its most basic form, this technique looks like the following (with an anonymous constraint):
```
func Foo[T any, PT interface { *T; M() }]() {
    p := PT(new(T))
    p.M() // calling method on non-nil pointer
}
```

Playground: [https://go.dev/play/p/L00tePwrDfx](https://go.dev/play/p/L00tePwrDfx)

---



Your constraint `SetGetter` already declares a type param `V`, so we slightly modify the example above:
```
// V is your original type param
// T is the additional helper param
type SetGetter[V any, T any] interface {
    Set(V)
    Get() V
    *T
}
```

Then you define the `SetGetterSlice` function with the type parameter `T any`, whose purpose is just to instantiate the constraint `SetGetter`.
You will then be able to convert the expression `&out[i]` to the pointer type, and successfully call the method on the pointer receiver:
```
// T is the type with methods with pointer receiver
// PT is the SetGetter constraint with *T
func SetGetterSlice[V any, T any, PT SetGetter[V, T]](values []V) []T {
    out := make([]T, len(values))

    for i, v := range values {
        // out[i] has type T
        // &out[i] has type *T
        // PT constraint includes *T
        p := PT(&out[i]) // valid conversion!
        p.Set(v)         // calling with non-nil pointer receiver
    }

    return out
}
```

Full program:
```
package main

import (
    "fmt"
)

type SetGetter[V any, T any] interface {
    Set(V)
    Get() V
    *T
}

func SetGetterSlice[V any, T any, PT SetGetter[V, T]](values []V) []T {
    out := make([]T, len(values))

    for i, v := range values {
        p := PT(&out[i])
        p.Set(v)
    }

    return out
}

// Count implements SetGetter interface
type Count struct {
    x int
}

func (c *Count) Set(x int) { c.x = x }
func (c *Count) Get() int  { return c.x }

func main() {
    ints := []int{1, 2, 3, 4, 5}

    // instantiate with base type
    sgs := SetGetterSlice[int, Count](ints)

    for _, s := range sgs {
        fmt.Println(s.Get()) // prints 1,2,3,4,5 each in a newline
    }
}
```

This becomes more verbose because `SetGetterSlice` now requires three type parameters: the original `V` plus `T` (the type with pointer receivers) and `PT` (the new constraint). However when you call the function, you can omit the third one – with type inference, both type params `V` and `T` required to instantiate `PT SetGetter[V,T]` are already known:
```
SetGetterSlice[int, Count](ints)
```

Playground: [https://go.dev/play/p/gcQZnw07Wp3](https://go.dev/play/p/gcQZnw07Wp3)


------------
    
    
## Answer \#1

 Vote: 2

Created at 2022-11-12 23:43:21

------------

Spent a few hours to understand it.
So decided to add my example.
```
package main

import (
    "fmt"
)

type User struct {
    FullName string
    Removed  bool
}

type Account struct {
    Name    string
    Removed bool
}

type Scanner[T User | Account] interface {
    Scan()
    *T
}

type Model interface {
    User | Account
}

func (user *User) Scan() {
    user.FullName = `changed in scan method`
    user.Removed = true
}

func (account *Account) Scan() {
    account.Name = `changed in scan method`
    account.Removed = true
}

func setRemovedState[T Model, PT Scanner[T]](state bool) *T {
    var obj T

    pointer := PT(&obj)
    pointer.Scan() // calling method on non-nil pointer

    return &obj
}

func main() {
    user := setRemovedState[User](true)
    account := setRemovedState[Account](true)

    fmt.Printf("User: %v\n", *user)
    fmt.Printf("Account: %v\n", *account)
}
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-10-14 21:44:37

------------

you can also try to attack the problem slighty differently, to keep it simple.
```
package main

import (
    "fmt"
)

func mapp[T any, V any](s []T, h func(T) V) []V {
    z := make([]V, len(s))
    for i, v := range s {
        z[i] = h(v)
    }
    return z
}

func mappp[T any, V any](s []T, h func(T) V) []V {
    z := make([]V, 0, len(s))
    for _, v := range s {
        z = append(z, h(v))
    }
    return z
}

// Count implements SetGetter interface
type Count struct {
    x int
}

func (c *Count) Set(x int) { c.x = x }
func (c *Count) Get() int  { return c.x }

func FromInt(x int) *Count {
    var out Count
    out.x = x
    return &out
}

func main() {
    ints := []int{1, 2, 3, 4, 5}

    sgs := mapp(ints, FromInt)
    fmt.Printf("%T\n",sgs)

    for _, s := range sgs {
        fmt.Println(s.Get())
    }

    fmt.Println()

    sgs = mappp(ints, FromInt)
    fmt.Printf("%T\n",sgs)

    for _, s := range sgs {
        fmt.Println(s.Get())
    }
}
```

[https://go2goplay.golang.org/p/vzViKwiJJkZ](https://go2goplay.golang.org/p/vzViKwiJJkZ)
It is like your `func SetGetterSlice[V any, T SetGetter[V]](values []V, constructor func() T) []T` but without the complex verbosity. It also gave me zero pain to solve.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-10-14 16:47:19

------------

Edit: see [blackgreen's answer](https://stackoverflow.com/a/69575720/1256452), which I also found later on my own while scanning through the same documentation they linked.  I was going to edit this answer to update based on that, but now I don't have to. :-)
There is probably a better way—this one seems a bit clumsy—but I was able to work around this with `reflect`:
```
if reflect.TypeOf(out[0]).Kind() == reflect.Ptr {
    x := reflect.ValueOf(out).Index(i)
    x.Set(reflect.New(reflect.TypeOf(out[0]).Elem()))
}
```

I just added the above four lines to your example.  The temporary variable is left over from some debug and obviously can be removed.  [Playground link](https://go2goplay.golang.org/p/6yqGNgWTWR2)


------------
    
    