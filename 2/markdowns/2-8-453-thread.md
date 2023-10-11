
# Post \#62291709 [Link](https://stackoverflow.com/questions/62291709/)

## linter err113: do not define dynamic errors, use wrapped static errors instead

**Vote**: 24 (233/702) **Views**: 13797 (375/702) 

**Internal ID** \#2-8-453

Created at 2020-06-09 20:56:51

Tags: `go`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------

I am using `err113` as part of `golangci-lint`. 

It  is complaining about ...

```
foo_test.go:55:61: err113: do not define dynamic errors, use wrapped static errors instead: "errors.New(\"repo gave err\")" (goerr113)
        repoMock.EXPECT().Save(gomock.Eq(&foooBarBar)).Return(nil, errors.New("repo gave err")),
                                                                  ^

foo_test.go:22:42: err113: do not define dynamic errors, use wrapped static errors instead: "errors.New(\"oops\")" (goerr113)
    repoMock.EXPECT().FindAll().Return(nil, errors.New("oops"))
                                            ^
```


What is best way to fix this ?

Quoting [https://github.com/Djarvur/go-err113](https://github.com/Djarvur/go-err113)

> Also, any call of errors.New() and fmt.Errorf() methods are reported
  except the calls used to initialise package-level variables and the
  fmt.Errorf() calls wrapping the other errors.

I am trying to get a idiomatic example for this.


----------
        
## Answer \#0

**Accepted** Vote: 17

Created at 2020-06-09 22:01:39

------------

Declare a package-level variables as suggested:

```
var repoGaveErr = errors.New("repo gave err")

 func someFunc() {
    repoMock.EXPECT().Save(gomock.Eq(&foooBarBar)).Return(nil, repoGaveErr)
 }
```


Every call to errors.New allocates a new unique error value. The application creates a single value representing the error by declaring the package-level variable.

There are two motivations for the single value:

- The application can compare values for equality to check for a specific error condition.- Reduce memory allocations (although probably not a big deal in practice)

The value [io.EOF](https://godoc.org/io#EOF) is a canonical example.


------------
    
    
## Answer \#1

 Vote: 11

Created at 2020-12-09 11:18:03

------------

Since  you can define a new error type, wrap it and use it.
for example, if you want to return an "operation not permitted" + the operation.
you need to implement something like
```
var OperationNotPermit = errors.New("operation not permitted")

func OperationNotFoundError(op string) error {
    return fmt.Errorf("OperationNotPermit %w : %s", OperationNotPermit, op)
}
```

then in your code, when you want to return the error,
```
return nil, OperationNotFoundError(Op)
```


first, define the custom error and the wapper
```
var repoError = errors.New("repositoryError")

func RepositoryError(msg string) error {
    return fmt.Errorf("%w: %s", repoError,msg)
}
```

then in your code,
```
repoMock.EXPECT().Save(gomock.Eq(&foooBarBar)).Return(nil, RepositoryError("YOUR CUSTOM ERROR MESSAGE"))
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2021-01-03 21:22:05

------------

Since it hasn't been said before, you . Given the idea is to wrap errors so they can be compared and unwrapped in the caller, returning a dynamic error in a test is fine as long as the purposes of your test are served.


------------
    
    