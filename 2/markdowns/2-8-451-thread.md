
# Post \#52266332 [Link](https://stackoverflow.com/questions/52266332/)

## Manually fetch dependencies from go.mod?

**Vote**: 35 (176/702) **Views**: 83052 (103/702) 

**Internal ID** \#2-8-451

Created at 2018-09-10 22:29:46

Tags: `go` `go-modules`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------

I'm using go 1.11 with module support. I understand that the go tool now installs dependencies automatically on build/install. I also understand the reasoning.

I'm using docker to build my binaries. In many other ecosystems its common to copy over your dependency manifest (package.json, requirements.txt, etc) and install dependencies as a separate stage from build. This takes advantage of docker's layer caching, and makes rebuilds much faster since generally code changes vastly outnumber dependency changes.

I was wondering if vgo has any way to do this?


----------
        
## Answer \#0

**Accepted** Vote: 79

Created at 2019-06-20 20:15:03

------------

It was an issue [#26610](https://github.com/golang/go/issues/26610), which is fixed now.
So now you can just use:
```
go mod download
```

For this to work you need just the `go.mod` / `go.sum` files.
For example, here's how to have a cached multistage Docker build: ([source](https://www.docker.com/blog/containerize-your-go-developer-environment-part-1/))
```
FROM golang:1.17-alpine as builder
RUN apk --no-cache add ca-certificates git
WORKDIR /build

# Fetch dependencies
COPY go.mod go.sum ./
RUN go mod download

# Build
COPY . ./
RUN CGO_ENABLED=0 go build

# Create final image
FROM alpine
WORKDIR /
COPY --from=builder /build/myapp .
EXPOSE 8080
CMD ["./myapp"]
```


---


Also see the article [Containerize Your Go Developer Environment – Part 2](https://www.docker.com/blog/containerize-your-go-developer-environment-part-2/), which describes how to leverage the  to speed up builds even further.


------------
    
    
## Answer \#1

 Vote: 24

Created at 2018-09-11 08:21:16

------------

You may use the `go mod vendor` command which will create a `vendor` folder in the main module's root folder, and copy all dependencies into it. After this you may pass the `-mod=vendor` param to the go tool, and then dependencies from the `vendor` folder will be used to build / compile / test your app.

So what you may do to speed up your builds:


1. Run the go mod vendor command to have an actual version of your dependencies.
2. Save / cache this vendor folder.
3. During builds, restore this vendor folder, and build / install your app by passing the -mod=vendor argument to the go tool, so no dependencies will be downloaded, but the content of the vendor folder will be used.



Quoting from `go help mod`:

> Modules and vendoringWhen using modules, the go command completely ignores vendor directories.By default, the go command satisfies dependencies by downloading modules
  from their sources and using those downloaded copies (after verification,
  as described in the previous section). To allow interoperation with older
  versions of Go, or to ensure that all files used for a build are stored
  together in a single file tree, 'go mod vendor' creates a directory named
  vendor in the root directory of the main module and stores there all the
  packages from dependency modules that are needed to support builds and
  tests of packages in the main module.To build using the main module's top-level vendor directory to satisfy
  dependencies (disabling use of the usual network sources and local
  caches), use 'go build -mod=vendor'. Note that only the main module's
  top-level vendor directory is used; vendor directories in other locations
  are still ignored.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-10-24 00:51:04

------------

I wanted to re-download all the dependencies using `go mod`, this is what I did:

1. Go to your GOROOT
2. sudo rm -rf pkg/mod/
3. Go to the directory where the go.mod file exists
4. go mod download




------------
    
    
## Answer \#3

 Vote: 0

Created at 2018-09-11 02:12:42

------------

You can use a package manager, There are many of them like [dep](https://github.com/golang/dep), [glide](https://github.com/Masterminds/glide), and [govendor](https://github.com/kardianos/govendor). `dep` is newer and is going to be integrated into go toolchain as official dependency management tool.

We also make docker images for go applications and we use [dind](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) to make those images and we prepared a CI/CD image with all dependencies preinstalled to make the builds faster. Though, it took a little bit of scripting to glue everything together.

Moreover, layering up the dependencies could result in big size of docker images. I suggest try [multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build/) which could help making images super lite.


------------
    
    