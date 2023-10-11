
# Post \#48882691 [Link](https://stackoverflow.com/questions/48882691/)

## Force retesting or disable test caching

**Vote**: 217 (18/702) **Views**: 144910 (55/702) 

**Internal ID** \#2-8-465

Created at 2018-02-20 10:10:53

Tags: `testing` `go`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------



When I run the same go test twice the second run is not done at all. The results are the cached ones from the first run.

```
PASS    
ok      tester/apitests    (cached)
```




I already checked [https://golang.org/cmd/go/#hdr-Testing_flags](https://golang.org/cmd/go/#hdr-Testing_flags) but there is no cli flag for that purpose.






----------
        
## Answer \#0

**Accepted** Vote: 342

Created at 2018-02-20 10:19:12

------------

There are a few options as described in the [testing flags docs](https://golang.org/cmd/go/#hdr-Testing_flags):

- `go clean -testcache`- `-count=1`

That said, changes in your code or test code will invalidate the cached test results (there's extended logic when using local files or environment variables as well), so you should not need to invalidate the test cache manually.


------------
    
    
## Answer \#1

 Vote: 95

Created at 2019-04-04 22:12:10

------------

In Go11, I couldn't disable cache using `GOCACHE` with modules, I used `-count=1` instead: 

```
go test -count=1
```


Prior to Go11: 

```
GOCACHE=off go test
```


Or, clean test cache and run test again: 

```
go clean -testcache && go test
```



------------
    
    
## Answer \#2

 Vote: 21

Created at 2018-05-15 07:07:11

------------

There's also `GOCACHE=off` mentioned [here](https://github.com/golang/go/issues/22758#issuecomment-344921828).


------------
    
    
## Answer \#3

 Vote: 10

Created at 2018-11-05 20:41:12

------------

The way that I fixed this (I'm using Visual Studio Code on macOS):

Code > Preferences > Settings

Click `...` on the right hand side of the settings page

Click `Open settings.json`

Either:


1. Add the following snippet to your settings.json file "go.testEnvVars": {
    "GOCACHE": "off"
}
2. Change the value of go.testEnvVars to include the following: "GOCACHE": "off"




------------
    
    
## Answer \#4

 Vote: 9

Created at 2022-08-25 15:50:23

------------



1. Open VSCode's settings.json. To open settings.json, press Ctrl + , (or Cmd+, on Mac), then click the Open JSON button shown below. Optionally, if you don't want to set this globally, you can create a .vscode/settings.json file at the project root.
2. Set the go.testFlags value in settings.json:  {    
     "go.testFlags": ["-count=1"]
 }
3. Save and enjoy.


Note: these steps ensure test cache will be skipped . If you want a one-time fix, then run `go clean -testcache` in the terminal, as Marc's most-voted answer says.


------------
    
    