
# Post \#69343900 [Link](https://stackoverflow.com/questions/69343900/)

## Cannot run debug Go using VSCode on Mac M1

**Vote**: 15 (318/702) **Views**: 15967 (343/702) 

**Internal ID** \#2-8-457

Created at 2021-09-27 08:58:44

Tags: `go` `visual-studio-code` `vscode-debugger` `apple-m1`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------

I found a topic that encounter the same problem ([Can't debug Golang in vscode apple m1](https://stackoverflow.com/questions/67637872/cant-debug-golang-in-vscode-apple-m1)) but I'm not sure it's an old solution or not because I'm using the Go version
```
go1.17.1 darwin/arm64
```

with
```
dlv version 1.7.2
```

and
```
VSCode version 1.60.2 (arm64)
```

on
```
Mac M1 BigSur (11.6)
```

when I run debug (`fn+f5`) a Debug console shows:
```
Starting: /Users/username/go/bin/dlv-dap dap --check-go-version=false --listen=127.0.0.1:53115 --log-dest=3 from /Users/username/go/src/project-name
DAP server listening at: 127.0.0.1:53115
```

and there is a Pop-up error:
```
Failed to launch: could not launch process: can not run under Rosetta, check that the installed build of Go is right for your CPU architecture
```

I have tried downgrade go version to 1.16.8 arm64 with dlv 1.6.1 but still got the same error.
I can use go build successfully on both versions.


----------
        
## Answer \#0

**Accepted** Vote: 23

Created at 2021-09-27 22:37:34

------------


1. Ensure your VSCode uses the arm64 version. (it can use a different go version from the system)
2. Run Go: install/update tools. It will rebuild all tools with the arm64 go version.




------------
    
    
## Answer \#1

 Vote: 27

Created at 2022-03-07 13:30:55

------------

Here is how I resolved this issue.

1. Uninstall Golang(AMD-64 version).
2. Install Golang(ARM-64). Confirm it with go env GOARCH
3. Remove(tools that VS-Code uses) binaries from $GOPATH/bin (Important)
4. Restart your VS-Code and install tools that VS-Code needed(if not installed vscode will complain and will ask you to install them)


: I accidentally installed `amd-64` version instead of `arm-64` version in my `Apple Mac M1`.
Thanks to [Painhardcore](https://stackoverflow.com/a/69353947/4408364) answer for pointing me in right direction.


------------
    
    
## Answer \#2

 Vote: 8

Created at 2022-02-09 07:47:33

------------

What worked for me:

1. Check for "chip" your mac is using, you can check that from "About This Mac" > "Overview" > "Chip"
2. If Apple chip is used, then download "arm64" type of files, such as "go-darwin-arm64.pkg", "vscode-arm64" etc. You get my point.
3. If Intel chip is used, then download with "amd" type.
4. Download and install "DELVE" from https://github.com/go-delve/delve/tree/master/Documentation/installation
5. Run your debugger.


Let me know, if you want the "launch.json" file config too, to debug.

Sharing the launch.json
```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Debug",
            "type": "go",
            "request": "launch",
            "mode": "auto",
            "program": "${workspaceFolder}/main.go",
            "args": [],
        }
    ]
}
```



------------
    
    
## Answer \#3

 Vote: 2

Created at 2022-07-27 10:24:14

------------

This answer worked for me. Installed ARM64 and tried again.
[https://github.com/go-delve/delve/issues/2604#issuecomment-1069740132](https://github.com/go-delve/delve/issues/2604#issuecomment-1069740132)


------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-06-13 07:02:39

------------

Download and install `DELVE` from [https://github.com/go-delve/delve/tree/master/Documentation/installation](https://github.com/go-delve/delve/tree/master/Documentation/installation)
It worked for me


------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-10-16 06:12:39

------------

Writing this so that anyone facing same problem while migrating to M1 mac can get all the required information at one place.
I faced this issue after migrating data from my intel mac to M1(using Apple Migration Assistant).
So basically, the go binaries and vscode for amd64 were copied to M1 mac(arm64)
Hence, i had to uninstall (removed all go related files i.e located at `/home/<my_user>/go` and go executables located at `/usr/local/go`) and reinstall go and vscode for arm64.
Post that, i was getting the error `could not launch process: EOF` while running the debugger in vscode using delve, to solve that i referred to this github thread - [https://github.com/go-delve/delve/issues/2794](https://github.com/go-delve/delve/issues/2794)
and performed below steps:
```
sudo rm -rf /Library/Developer/CommandLineTools

xcode-select --install
```

After that, i was able to use delve to debug code in vscode


------------
    
    