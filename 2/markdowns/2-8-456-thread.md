
# Post \#67125307 [Link](https://stackoverflow.com/questions/67125307/)

## Using multiple interfaces in Golang

**Vote**: 2 (631/702) **Views**: 3058 (566/702) 

**Internal ID** \#2-8-456

Created at 2021-04-16 12:33:11

Tags: `go` `design-patterns` `interface`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------

I'm learning Golang and as an exercise in using interfaces I'm building a toy program. I'm having some problem trying to use a type that "should implement" two interfaces - one way to solve that in C++ and Java would be to use inheritance(there are other techniques, but I think that is the most common). As I lack that mechanism in Golang, I'm not sure how to proceed about it. Below is the code:
```
var (
    faces = []string{"Ace", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine", "Ten", "Jack", "Queen", "King"}

    suits = []string{"Hearts", "Diamonds", "Spades", "Clubs"}
)

type Card interface {
    GetFace() string
    GetSuit() string
}

type card struct {
    cardNum int
    face    string
    suit    string
}

func NewCard(num int) Card {
    newCard := card{
        cardNum: num,
        face:    faces[num%len(faces)],
        suit:    suits[num/len(faces)],
    }

    return &newCard
}

func (c *card) GetFace() string {
    return c.face
}

func (c *card) GetSuit() string {
    return c.suit
}

func (c *card) String() string {
    return fmt.Sprintf("%s%s ", c.GetFace(), c.GetSuit())
}
```

What I'm trying to achieve:
- - 
The problem comes when I'm trying to use a new card though the "Card" interface and also trying to get the string representation. I cannot pass the interface as the parameter of the implementation of the  method as there is a compiler error which is related to the addressability of an interface at the core language level(still digging through that documentation). The very simple example of testing exposes the issue:
```
func TestString(t *testing.T) {
    card := NewCard(0)
    assert.EqualValues(t, "AceHearts ", card.String(), " newly created card's string repr should be 'AceHearts '")
}
```

The compiler tells me, for good reason, that . I could just add the  method to my  interface, but I do not find that to be clean: I have other entities implemented with the same model and I would have to add that redundancy everywhere; there is already an interface with that method.
What would be a good solution for the above issue that I'm having?
(to address some of the very good comments)
- - - - - 


----------
        
## Answer \#0

**Accepted** Vote: 9

Created at 2021-04-17 00:40:08

------------

I should go over some prefacing points first:
- - - 

---



## From your question:


> I would like to have the Card interface to hide away implementation details and for the clients to program against the interface and not against the concrete type
This is the root of your other problems.
As mentioned in the comments, I see this in multiple other packages and regard it as a particularly pesky anti-pattern.  First, I will explain the reasons why this pattern is "anti" in nature.
- - 
Interfaces are Go's mechanism of polymorphism.  The usage of interfaces in parameters makes your code more versatile. Think of the ubiquitous `io.Reader` and `io.Writer`. They are fantastic examples of interfaces.  They are the reason why you can patch two seemingly unrelated libraries together, and have them just work.  For example, you can log to stderr, or log to a disk file, or log to an http response.  Each of these work exactly the same way, because [log.New](https://golang.org/pkg/log/#New) takes an `io.Writer` parameter, and a disk file, stderr, and http response writer all implement `io.Writer`.  To use interfaces simply to "hide implementation details" (I explain later why this point fails), does not add any flexibility to your code.  If anything, it is an abuse of interfaces by leveraging them for a task they weren't meant to fulfill.

## Point / Counterpoint



1. "Hiding my implementation provides better encapsulation and safety by making sure all the details are hidden."


- 

1. "Struct values are dirty and raw and I don't feel good about passing them around."


- 

1. "Isn't it confusing if my package exports package.Struct, but clients have to always use *package.Struct? What if they make a mistake? It's not safe to copy my struct value; things will break!"


- `*package.Struct``:=``package.Struct``*package.Struct`

1. "It helps to decouple the client code from the package code"


- [image](https://golang.org/pkg/image/)

## When is it actually useful?


The main realistic case where making a premature interface  returning it  help clients, is this:
- - `:=`- 
Note that this wouldn't be a breaking API change even if you weren't returning the premature interface, as you're only  a new type and constructor.
If you decided to only  this premature interface, and still return concrete types  (as done in the `image` package), then all the client would likely need to do to remedy this is spend a couple minutes using their IDE's refactor tool to replace `*package.Struct` with `package.Interface`.

## It significantly hinders the usability of package documentation


Go has been blessed with a useful tool called Godoc. Godoc automatically generates documentation for a package from source.  When you export a type in your package, Godoc shows you some useful things:
- - 
Once you bubble-wrap your struct into an interface, your Godoc representation is hurt. The methods of your type are no longer shown in the package index, so the package index is no longer an accurate overview of the package as it is missing a lot of key information.  Also, each of the methods no longer has its own dedicated space on the page, making it's documentation harder to both find and read. Finally it also means that you no longer have the ability to click the method name on the doc page to view the source code. It's also no coincidence that in many packages that employ this pattern, these de-emphasized methods are most often left without a doc comment, even when the rest of the package is well documented.

## In the wild


[https://pkg.go.dev/github.com/zserge/lorca](https://pkg.go.dev/github.com/zserge/lorca)
[https://pkg.go.dev/github.com/googollee/go-socket.io](https://pkg.go.dev/github.com/googollee/go-socket.io)
In both cases we see a misleading package overview, along with a majority of interface methods being undocumented.
(Please note I have nothing against any of these developers; obviously every package has it's faults and these examples are cherry picked.  I'm also not saying that they had no justification to use this pattern, just that their package doc is hindered by it)

## Examples from the standard library


If you are curious about how interfaces are "intended to be used", I would suggest looking through the docs for the standard library and taking note of where interfaces are declared, taken as parameters, and returned.
[https://golang.org/pkg/net/http/](https://golang.org/pkg/net/http/)
[https://golang.org/pkg/io/](https://golang.org/pkg/io/)
[https://golang.org/pkg/crypto/](https://golang.org/pkg/crypto/)
[https://golang.org/pkg/image/](https://golang.org/pkg/image/)
Here is the only standard library example I know of that is comparable to the "interface hiding" pattern.  In this case, reflect is a very complex package and there are several implementations of `reflect.Type` internally.  Also note that in this case, even though it is necessary, no one should be happy about it because the only real effect for clients is messier documentation.
[https://golang.org/pkg/reflect/#Type](https://golang.org/pkg/reflect/#Type)

# tl;dr


This pattern will hurt your documentation, while accomplishing nothing in the process, except you might make it slightly quicker in very specific cases for clients to use parallel implementations of this type that you may or may not introduce in the future.
These interface design principles are meant for the benefit of the client, right?  Put yourself in the shoes of the client and ask: what have I really gained?


------------
    
    
## Answer \#1

 Vote: 1

Created at 2021-04-16 13:06:26

------------

Not entirely sure if this is what you are looking for but you could try embedding the other interface in Card interface as shown below.
```
type Printer interface {
    String() string
}

type Card interface {
    Printer // embed printer interface
    GetFace() string
    GetSuit() string
}
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-04-16 21:33:18

------------

Interface `Card` hasn't method `String`, it doesn't matter, that underlying type `card` have it, because method is hidden from you (unless you access it via reflection).
Adding `String() string` method to `Card` will solve problem:
```
type Card interface {
    GetFace() string
    GetSuit() string
    String()  string
}
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-04-18 20:56:56

------------

The go language does not have [subtype polymorsphism](https://en.wikipedia.org/wiki/Subtyping). Therefore, the pattern you want to achieve is not  by the very foundations of the language. You may achieve this undesirable pattern by composing structs and interfaces, though.


------------
    
    