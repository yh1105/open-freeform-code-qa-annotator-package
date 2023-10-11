
# Post \#61845013 [Link](https://stackoverflow.com/questions/61845013/)

## "package XXX is not in GOROOT" when building a Go project

**Vote**: 156 (32/702) **Views**: 304329 (21/702) 

**Internal ID** \#2-8-458

Created at 2020-05-16 23:35:31

Tags: `go` `goland`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------

I have a weird issue that arose when I took a break from this project. Upon starting up Goland, I'm riddled with errors when trying to run my project. 

The specific error, when building one of my packages, is: 
`start.go: package project/game is not in GOROOT (C:\Go\src\project\game)`

I have a folder structure as such under `C:\Users\username`

```
go
|-src
   |-project
        |-game
            |-entity
                 |-whatever.go
            |-game_stuff.go
        |-server
```


and my env vars are as such:

```
GOROOT=C:\Go 
GOPATH=C:\Users\ketchup\go
```


for each of the modules (project/game/entity, project/game, project/server), I did a `git mod init`. 

When building, Goland will try to run this:

```
C:\Go\bin\go.exe build -o C:\Users\ketchup\AppData\Local\Temp\___go_build_project_server.exe project/server
```


and return the error.

Can anyone help me with this issue? Kind of lost since Goland was working fine the last time I opened it. Also not even sure what direction to look at - I'm pretty new to Go and I'm not really sure what documentation to look at :\  Thank you everyone! 


----------
        
## Answer \#0

**Accepted** Vote: 43

Created at 2020-05-18 00:36:52

------------

A pretty dumb conclusion (mostly on my part) but my issue came from having done `go mod init` in each of the folders. after removing `go.mod` and `go.dep` from each of the folders I did `go mod init` in, I could build without issue (through terminal)
Also, my packages in GoLand were not being detected because I had Go Modules enabled in the Settings. I disabled it and GoLand was able to index the external packages and my own packages.


------------
    
    
## Answer \#1

 Vote: 85

Created at 2022-01-22 20:28:42

------------

In newer versions (post 1.13) of Go, you don't need to set environment variables like `GOPATH`, `GOBIN`, etc.
You also need to have a `go.mod` file at the project root. This will make the directory a Go module. This is also where the `.git/` is located. This means that only one `go.mod` is needed per repository. Inside the project root you could do a `go mod init remote-repo.com/username/repository`
I installed Go using Homebrew on macOS so `GOROOT` is `/opt/homebrew/Cellar/go/1.17.5/libexec`. This location contains the standard library and runtimes for Go.
`test` and `run` commands are run in the format `go COMMAND package_path/xxx`. Without specifying the package_path `./` and just running `go COMMAND xxx`, the compiler assumes that the module  is located in GOROOT, and throws error `package xxx is not in GOROOT (path/to/GOROOT/src/xxx)` because it doesn't exist.
This behavior is expected because the package we are working with is not part of the Go SDK, i.e., not in `GOROOT`. The package we are working with will either end up in the go workspace or in the current working directory. Running `go install` compiles and puts an executable binary in `$GOBIN` (a.k.a `$GOPATH/bin` - here `$GOPATH` is the Go workspace). Running `go build` from inside a package compiles and puts an execuatble in that directory.
You haven't listed the files inside the `server/` package and which file has the main function, so I'll emulate 3 workflows of a calculator each demonstrating more complexity. The last workflow is similar to your directory structure.

## Directory Structure



### Version 1:


- Getting started with packages- Basic functionality
```
calculatorv1
├── go.mod                      <- go mod init github.com/yourname/calculatorv1
└── basic/
    ├── add.go
    ├── add_test.go
    ├── main.go
    ├── multiply.go
    └── multiply_test.go
```


### Version 2:


- More functionality- Multiple packages
```
calculatorv2
├── go.mod                      <- go mod init github.com/yourname/calculatorv2
├── main.go
└── basic/
│   ├── add.go
│   ├── add_test.go
│   ├── multiply.go
│   └── multiply_test.go
└─── advanced/
     ├── square.go
     └── square_test.go
```


### Version 3:


- Even more functionality- Nested packages
```
calculatorv3
├── go.mod                      <- go mod init github.com/yourname/calculatorv3
├── main.go
└── basic/
│   ├── add.go
│   ├── add_test.go
│   ├── multiply.go
│   └── multiply_test.go
└─── advanced/
     ├── square.go
     ├── square_test.go
     └── scientific/
         ├── declog.go
         └── declog_test.go
```


---



## Workflow


 Substitute `xxx` with `basic`, `advanced`, or `advanced/scientific` depending on the version you're working with.
- Initialize Go module in the project directory (one of `calculatorv1`, `calculatorv2`, or `calculatorv3`) using `go mod init`- Run tests`go test -v ./...`      (from the project root, recursively execute all test suites)OR`go test -v ./xxx`      (from the project root, run the test suite in package "xxx")OR```
cd xxx/
go test -v            # (from inside the package)
```
- Compile and execute package`go run ./...`          (from the project root, recursively run all `.go` files except tests)OR`go run ./xxx`          (from the project root, run all `.go` files in "xxx" package except tests)OR```
cd xxx
go run .              # (from inside the package)
```
: Only files in the main package are executable, i.e., files having declaration `package main`. This means that `go run ./xxx` will only work with version1, and not versions 2 and 3. So instead for versions 2 and 3, run `go run main.go`

---



## Code




### Version 1



```
package main

func addition(x int, y int) int {
    return x + y
}
```


```
package main

import "testing"

func TestAdd(t *testing.T) {

    t.Run("adding two positive numbers", func(t *testing.T) {
        sum := addition(2, 2)
        expected := 4
        
        if sum != expected {
            t.Errorf("Expected %d; but got %d", expected, sum)
        }
    })
    
    t.Run("adding two negative numbers", func(t *testing.T) {
        sum := addition(-3, -4)
        expected := -7

        if sum != expected {
            t.Errorf("Expected %d; but got %d", expected, sum)
        }
    })

    t.Run("adding one positive and one negative integer", func(t *testing.T) {
        sum := addition(1, -3)
        expected := -2

        if sum != expected {
            t.Errorf("Expected %d; but got %d", expected, sum)
        }
    })
    
}
```


```
package main

import "fmt"

func main() {
    var num1 int = 1
    var num2 int = 2
    
    sum := addition(num1, num2)
    product := multiplication(num1, num2)

    fmt.Printf("The sum of %d and %d is %d\n", num1, num2, sum)
    fmt.Printf("The multiplication of %d and %d is %d\n", num1, num2, product)
}
```


### Version 2



```
package main

import (
    "fmt"
    "github.com/yourname/calculatorv2/basic"
    "github.com/yourname/calculatorv2/advanced"
)

func main() {
    var num1 int = 1
    var num2 int = 2
    
    product := basic.Multiplication(num1, num2)
    square := advanced.Square(num2)

    fmt.Printf("The product of %d and %d is %d\n", num1, num2, product)
    fmt.Printf("The square of %d is %d\n", num2, square)
}
```


```
package basic

func Multiplication(x int, y int) int {
    return x * y
}
```


```
package basic

import "testing"

func TestMultiply(t *testing.T) {

    t.Run("multiplying two positive numbers", func(t *testing.T) {
        sum := Multiplication(2, 2)
        expected := 4
        
        if sum != expected {
            t.Errorf("Expected %d; but got %d", expected, sum)
        }
    })
    
    t.Run("multiplying two negative numbers", func(t *testing.T) {
        sum := Multiplication(-3, -4)
        expected := 12

        if sum != expected {
            t.Errorf("Expected %d; but got %d", expected, sum)
        }
    })

    t.Run("multiplying one positive and one negative integer", func(t *testing.T) {
        sum := Multiplication(1, -3)
        expected := -3

        if sum != expected {
            t.Errorf("Expected %d; but got %d", expected, sum)
        }
    })
    
}
```


```
package advanced

func Square(x int) int {
    return x * x
}
```


### Version 3



```
package main

import (
    "fmt"
    "github.com/yourname/calculatorv3/basic"
    "github.com/yourname/calculatorv3/advanced"
    "github.com/yourname/calculatorv3/advanced/scientific"
)

func main() {
    var num1 int = 1
    var num2 int = 2
    var num3 float64 = 2
    
    product := basic.Multiplication(num1, num2)
    square := advanced.Square(num2)
    decimallog := scientific.DecimalLog(num3)

    fmt.Printf("The product of %d and %d is %d\n", num1, num2, product)
    fmt.Printf("The square of %d is %d\n", num2, square)
    fmt.Printf("The decimal log (base 10) of %f is %f\n", num3, decimallog)
}
```


```
package advanced

func Square(x int) int {
    return x * x
}
```


```
package scientific

import "math"

func DecimalLog(x float64) float64 {
    return math.Log10(x)
}
```


```
package scientific

import "testing"

func TestDecimalLog(t *testing.T) {

    t.Run("adding two positive numbers", func(t *testing.T) {
        sum := DecimalLog(100)
        expected := 2.0
        
        if sum != expected {
            t.Errorf("Expected %f; but got %f", expected, sum)
        }
    })
    
    t.Run("adding two negative numbers", func(t *testing.T) {
        sum := DecimalLog(10)
        expected := 1.0

        if sum != expected {
            t.Errorf("Expected %f; but got %f", expected, sum)
        }
    })
}
```



------------
    
    
## Answer \#2

 Vote: 62

Created at 2021-03-02 07:58:06

------------

You may have GO111MODULE set "on", which will be on the go mod. Turning off the GO111MODULE may resolve this problem.
```
go env -w GO111MODULE=off
```



------------
    
    
## Answer \#3

 Vote: 18

Created at 2020-10-20 13:01:49

------------

To anyone who  modules to work with GoLand after they have stopped doing so, make sure 'Enable Go modules integration' is checked in the Preferences as such:
[](https://i.stack.imgur.com/Yx4lE.png)


------------
    
    
## Answer \#4

 Vote: 15

Created at 2021-05-25 19:24:43

------------

So it looks like if you running go mod init 'xxx' the xxx is core name of your project. In there main packages complete name is 'xxx/main' so if you have your project root folder like this:
```
root -> go mod init xxx
 |- main.go -> package "main"
 |- tools
      |- helper.go -> package "tools"
```

and you want to import tools package from main.go you need to import this "xxx/tools"


------------
    
    
## Answer \#5

 Vote: 4

Created at 2021-03-14 06:05:31

------------

I made the mistake of changing the module name inside `go.mod` file manually. After fixing it, it worked fine.
```
└── hello
    ├── go.mod
    ├── go.sum
    ├── hello.go
    └── morestrings
        ├── reverse.go
        └── reverse_test.go
```

Excerpts of `hello.go`
```
import (
...
"hello/morestrings"
...
)
```

Running `go build` in hello directory was giving following error:
> hello.go:5:2: package hello/morestrings is not in GOROOT (/usr/local/go/src/hello/morestrings)


------------
    
    
## Answer \#6

 Vote: 3

Created at 2021-04-10 16:33:39

------------

While running command `go mod init` you need to add folder name, not main.go page name.
Like: `go mod init confirm_enrl`. `confirm_enrl` is project folder name


------------
    
    
## Answer \#7

 Vote: 3

Created at 2021-04-27 07:25:47

------------

I met the same error with the project layout
```
project
        |- pgk
        |- src
            |-module1
                 |- some.go
            |- main.go
```

some.go
```
package module1

...
```

main.go
```
import "project/module1"
```

Originally, I import module1 through `./module1`, the error `package module1 is not in GOROOT` comes up. I solve it through `import "project/module1"`.
PS: per [golang project layout](https://github.com/golang-standards/project-layout), `src` should NOT in golang project.


------------
    
    
## Answer \#8

 Vote: 3

Created at 2021-06-16 08:19:14

------------

I got the same error when I had a spelling mistake in the package name. Therefore, it might be the root cause behind this error. Maybe it will be useful for someone.


------------
    
    
## Answer \#9

 Vote: 3

Created at 2022-05-09 06:39:29

------------

For me it was because my main method wasn't inside package main
```
package main

func main() {
{

}
```



------------
    
    
## Answer \#10

 Vote: 2

Created at 2021-09-16 14:12:35

------------

For my case just updating my  to the latest solved the issue .
I [downloaded the latest binary](https://golang.org/doc/install) and installed it and boom all was sorted !


------------
    
    
## Answer \#11

 Vote: 2

Created at 2022-05-05 13:04:18

------------

It seems a little funny, but I faced same error because of running:
```
go run build
```

instead of
```
go build main.go
```

So it shows that everything after  or  should have a recognized path by go.


------------
    
    
## Answer \#12

 Vote: 1

Created at 2022-04-29 02:45:57

------------

In my case, it is cause by changing  goroot path.
GOROOT is a variable that defines where your Go SDK is located
it will work if you change you goroot to where go sdk you installed


------------
    
    
## Answer \#13

 Vote: 1

Created at 2022-06-09 06:31:55

------------

Had this problem, deleted my go.mod & go.sum files then rebuilt it using
```
go mod init
```

This solved my problem :)


------------
    
    
## Answer \#14

 Vote: 1

Created at 2022-06-22 10:41:07

------------

Windows PC user
For me the issue was in how I was attempting to run the project
While at the root folder of the project and my `main.go` file is in `/cmd/web/`
I faced the issue when I tried to run both
`go run cmd/web/.`
`go run /cmd/web/.`
It only worked when I used the
`go run ./cmd/web/.`
Notice the `.` at the start


------------
    
    
## Answer \#15

 Vote: 1

Created at 2022-10-25 08:05:43

------------

In my own case, it turned out that I was using `go run main` instead of `go run main.go`


------------
    
    
## Answer \#16

 Vote: 0

Created at 2021-06-28 10:55:42

------------

I came across the same like issue when i was following go getting started tutorial with goland default `awesomeProject`.
What i did after crating project run this command `go mod init example.com/hello` but my root directory name was different than `example.com/hello`.
After manually edit the go.mod file and replace `awesomeProject` with `example.com/hello` the build works successfully.


------------
    
    
## Answer \#17

 Vote: 0

Created at 2021-08-03 12:58:41

------------

I knew this is about one year ago. I just want to share same experience for those who have, have modules in local folders..
The problem is because you have not publish your module to a central location like github.
You have to run this command to tell go that the module is a local module
```
go mod edit --replace external_folder_module_name= ../pathItsLocated
```



------------
    
    
## Answer \#18

 Vote: 0

Created at 2021-08-13 06:51:20

------------

I was also faced similar error while building the docker image. The error was `#19 0.785 controllers/vasaprovider_controller.go:28:2: package vasa-operator/config/configs is not in GOROOT`.
After adding the go file location in Dockerfile, it worked for me. The lines I had added are:
```
COPY config/configs config/configs/

COPY --from=builder /workspace/config/configs config/configs/
```



------------
    
    
## Answer \#19

 Vote: 0

Created at 2021-10-14 14:47:33

------------

In my case, I was trying to build from the wrong directory. The old directory didn't even have any `.go` files.


------------
    
    
## Answer \#20

 Vote: 0

Created at 2021-11-25 16:55:25

------------

Make sure your import statement matches the module name that you defined in your go mod.


------------
    
    
## Answer \#21

 Vote: 0

Created at 2021-11-27 15:11:34

------------

just initialise the project with mod file and start working on on root folder.
```
go mod init <name-anything>
```



------------
    
    
## Answer \#22

 Vote: 0

Created at 2022-02-27 17:48:11

------------

We can solve this problem by writing this command on terminal: `go env -w GO111MODULE=off`
But after this command, you will be not able to use `go mod init` through terminal. You must create "go.mod" file manually.


------------
    
    
## Answer \#23

 Vote: 0

Created at 2023-01-11 14:48:04

------------

I don't like that everyone is giving an answer which uses github module naming conventions as if you have to name them in that way or to have your module on github for it to be used within a local environment, so here's my answer to — hopefully — help clarify a few moments I was confused about regarding the naming of packages and modules in Go (I will use deliberately unusual names for the files and folders so you could see that you don't have to name your files a specific way for your Go project to work (you have to structure them in a specific way though)):
So, Golang project conceptually has:
(this is optional for a Go project)
- 
(this is required for a Go project to compile)
- "Module" — this name, conceptually, in a Go project, is used to specify the program/library as a whole (meaning, it refers to its root, which is usually stored in some remote repository).- "Packages" — in a Go project, this name is applied to literal  within which the "package " are stored — It's a way of organizing code within a module.
In practice, the concepts from above imply that if you want to create a multi-package project, for it to compile, you need to do the following:
(step-by-step example:)
-- Create a folder that will serve as a root folder for the project and  into it:
```
$ mkdir some_amazing_project && cd some_amazing_project
```

-- A collection of Go code becomes a  when there's a valid go.mod file in its root directory, so use the following command to generate it:
```
$ go mod init my_amazing_module_lib_program
```

(The inside of your go.mod file looks like this):
```
module my_amazing_module_lib_program // this is your unique module identifier and you use this name as a relative root for your package names when you use "import"

go 1.19
```

-- Now create an entry point to your Go program which is a go file that  be placed in the root folder (where the go.mod file resides), but you could just as well place it in any subfolder within this module (the root folder), but no deeper than that (or you'll receive the "" error):
```
$ touch some_main_file.go
```

> (^^ you can call this file whatever you want, but the convention is "main.go")
-- Inside of that  file you  specify the  to be  and you  have a function within that file that is also called :
```
// some_main_file.go

package main

import "fmt"

func main() {
    fmt.Println("hello there")
}
```

(This is your project structure so far):
```
some_amazing_project/
    |- go.mod
    |- some_main_file.go
```

(Now, let's say you want to create a package within this module/program/library. You would need to do the following):
-- Create a new folder, call it "amazing_package" and create any .go file in it (the name of the file doesn't really matter as only the name of the containing folder is used to refer to the package from other .go files):
```
$ mkdir amazing_package && cd amazing_package && touch whatever_file.go
```

-- Inside of the  you use the  keyword to specify the name of this package (this package name will be used only when specifying the name of the function to invoke from within other .go files — you'll see in a bit):
```
// whatever_file.go

package wowow

import "fmt"

func PrintWowow() {
    fmt.Println("this is from wowow package")
}
```

> ^^ The variables/constants/functions/types/etc..(any "identifier" in Go) that starts with an uppercase letter is exported and is made available to be imported into other packages.
(For the sake of example, let's create another file inside of this package):
```
$ touch another_cool_file.go
```

-- Inside of the  you use the same  keyword to specify the name of this package as you've used for in the  (because these files are in the same "package" conceptually, meaning that in practice they are stored in the same folder (if you use a different name for the  keyword while storing the file in the same folder, you'll receive an error during compilation)):
```
// another_cool_file.go

package wowow

import "fmt"

var Some_var = 42 // this will also be automatically exported

func PrintAnotherWow() {
    fmt.Println("this is also from wowow package, but from a different file")
}
```

(This is your project structure so far):
```
some_amazing_project/
    |- go.mod
    |- some_main_file.go
    |- amazing_package/
            |- whatever_file.go
            |- another_cool_file.go
```

-- Now, to reference the "PrintWowow" and "PrintAnotherWow" function from  other .go file within this  module you use the unique module identifier from  file and a path relative to that identifier that specifies the location of a package folder to import (I'll use the  in this example. We re-write it like so):
```
// some_main_file.go

package main

import (
    "fmt"
    "my_amazing_module_lib_program/amazing_package"
)

func main() {
    fmt.Println("hello there")

    // now you use the name of the package specified inside of those files from *amazing_package* folder
    wowow.PrintWowow()
    wowow.PrintAnotherWow()
    fmt.Println("and this is Some_var from the same imported package:", wowow.Some_var)
}
```

And here is the result of running `go build . && go run .` while inside of  (root) folder:
```
hello there
this is from wowow package
this is also from wowow package, but from a different file
and this is Some_var from the same imported package: 42
```



------------
    
    
## Answer \#24

 Vote: 0

Created at 2023-01-21 15:17:20

------------

if you are getting package project/game is not in GOROOT (C:\Go\src\project\game), and you have a go.mod file...    have you checked the imports in my *.go files to confirm they are synching up properly. If not then perhaps you should start there.
I get this error every once in a while especially when I am setting up a new project.   first thing I do is confirm my imports and that usually fixes everything.   thank you goland ;)


------------
    
    
## Answer \#25

 Vote: -1

Created at 2022-07-19 00:52:06

------------

in my case the `handler` path in `serverless.yaml` for the function in `functions` was incorrect


------------
    
    