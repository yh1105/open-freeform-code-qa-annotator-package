
# Post \#55503167 [Link](https://stackoverflow.com/questions/55503167/)

## go build can't find a revision

**Vote**: 22 (248/702) **Views**: 67459 (129/702) 

**Internal ID** \#2-8-459

Created at 2019-04-03 19:46:05

Tags: `go` `github` `module`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------

I developed a repo on computer A and created a go.mod/go.sum that I checked in.

I pull that repo with the go.mod/go.sum files on computer B, but when I try to build the program, the module constraints can't be satisfied.

```
$ go build
go: finding github.ibm.com/kms/key-protect-client v0.1.5
go: finding golang.org/x/tools v0.0.0-20180221164845-07fd8470d635
go: github.ibm.com/kms/key-protect-client@v0.1.5: unknown revision v0.1.5
go: error loading module requirements
```


The repo that is failing is a private repo, and for some reason it doesn't get downloaded to the module cache. On another computer, the dependencies are downloaded and the build succeeds. I am building another private repo in that same domain, so I know that my github credentials give me access to these repos. But for some reason, the go module system can't get to the dependent repo.

I cannot find more information how to debug this.


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2019-04-04 18:19:49

------------

Problem is related to cmd/go not being aware of git identities and assuming there is only one articulated in the .gitconfig file.

Work-around: have a set of git identities ready to replace global .gitconfig when you need to work with that git identity and are dealing with cmd/go tools that update the module file.


------------
    
    
## Answer \#1

 Vote: 31

Created at 2020-02-19 04:36:18

------------

Possible solutions for golang unknown revision if you are using private repository:


1. git config --global url."ssh://git@yourserver".insteadOf "https://yourserver"
2. Check your git repository permissions




------------
    
    
## Answer \#2

 Vote: 6

Created at 2021-03-03 20:38:33

------------

I had the same problem, please, visit [the link](https://golang.cafe/blog/how-to-fix-go-mod-unknown-revision.html) .
Make sure you have set GO111MODULES
```
go env -w GO111MODULE=on
```

Make sure your git configuration is appropriate for private repositories
```
git config --global url."ssh://git@github.com:acme-corporation".insteadOf "https://github.com/acme-corporation"
```

Make sure to create a personal access token if using 2FA
```
git config --global url."https://:x-oauth-basic@github.com:acme-corporation".insteadOf "https://github.com/acme-corporation"
```

in my case after setting:
- - 
I had to do the same with the ~ / .netrc file:
```
machine gitlab.url.com
    login <gitlab username>
    password <GITLAB_TOKEN>
```



------------
    
    
## Answer \#3

 Vote: 3

Created at 2022-03-25 13:07:34

------------

if nothing helps (see the former answers before), use this:
```
go get -u
```

> The -u flag instructs get to update modules providing dependencies
of packages named on the command line to use newer minor or patch
releases when available.
Furthermore, I recommend using a versions manager like [gobrew](https://github.com/cryptojuice/gobrew). You can easily change between the versions because sometimes it can be a version-related issue too.
If you do so, you can also set the compatibility version for your `tidy`, e.g:
```
go mod tidy -compat=1.17
```

Now you are ready to build:
```
go clean --modcache
go get -u
go build
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2019-07-21 10:14:24

------------

In case getting

> "Error": "unknown revision ..." 

or

> "Error": "invalid version ..."

Make sure that you did not specify  (pull request). Even a merged PR might be wrong. 

Look for the commit hash in the github repo 
under `/commits`, but not under `/pulls`. 

See more info here: [golang/go#31191](https://github.com/golang/go/issues/31191) explicitly filters out these kind of commits.


------------
    
    
## Answer \#5

 Vote: 1

Created at 2020-09-15 10:35:29

------------

Just in case, did you add `"github.ibm.com/kms"` to your environment variable `GOPRIVATE` on computer B? I've had this error occur to me due to exactly this reason.


------------
    
    
## Answer \#6

 Vote: 1

Created at 2022-05-05 18:01:24

------------

Usually the reason can be that the repository is private so you should be authorized at first. Create .netrc file and include there your credentials, the full description you can find here. [https://www.gnu.org/software/inetutils/manual/html_node/The-_002enetrc-file.html](https://www.gnu.org/software/inetutils/manual/html_node/The-_002enetrc-file.html)
if you want to build a docker image you can use this DockerFile example
```
ENV GOPRIVATE "gitlab.com/yourPrivateRepo"

ARG GO_GET_TOKEN_PASS
ARG GO_GET_TOKEN_LOGIN

RUN touch ~/.netrc && \
    chmod 600 ~/.netrc && \
    echo "machine gitlab.com login ${GO_GET_TOKEN_LOGIN} password ${GO_GET_TOKEN_PASS}" > ~/.netrc
```



------------
    
    
## Answer \#7

 Vote: 0

Created at 2019-09-16 02:46:24

------------

In line with some comments above -- I had this problem because my git was fried due to updating to Mojave... the highly upvoted response here (`xcode-select --install`) got me past this error message [Git is not working after macOS Mojave Update (xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools)](https://stackoverflow.com/questions/52522565/git-is-not-working-after-macos-mojave-update-xcrun-error-invalid-active-devel)

if you are having this problem make sure your `go status` etc are working as expected.


------------
    
    
## Answer \#8

 Vote: 0

Created at 2021-05-23 09:41:52

------------

In some cases, in addition to configuring an url-insteadOf configuration in git, you have to disable `GOPROXY` and `GOSUMDB`, as follows:
```
$ go env -w GOPROXY=direct GOSUMDB=off
```

Running `go env` before and after shows you the effect. Disabling them using the above command is establishing that this was the reason for the failure. As disabling checksum verification has some security concerns, you might want to keep `GOPROXY` and `GOSUMDB` for imported public modules. You can find some more information on how to achieve this here:
[https://jfrog.com/blog/why-goproxy-matters-and-which-to-pick/](https://jfrog.com/blog/why-goproxy-matters-and-which-to-pick/)


------------
    
    