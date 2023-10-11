
# Post \#55041915 [Link](https://stackoverflow.com/questions/55041915/)

## Multiple modules within the same project

**Vote**: 29 (199/702) **Views**: 37671 (198/702) 

**Internal ID** \#2-8-454

Created at 2019-03-07 10:46:05

Tags: `go` `module`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------

I've been playing with Go modules and I was wondering what the best practice is in terms of the following directory structure:

```
project
├── go.mod
├── main.go
└── players
    ├── go.mod
    ├── players.go
    └── players_test.go
```


I was having problems importing the `players` package into my root project at first, but I noticed I could do this in the root `go.mod` file

```
module github.com/<name>/<project>

require (
    github.com/<name>/players v0.0.0
)

replace github.com/<name>/players => ./players
```


This then allows me to do `import "github.com/<name>/players"` in my `main.go` file.

Now this approach works and was taken from [here](https://github.com/golang/go/wiki/Modules#can-i-work-entirely-outside-of-vcs-on-my-local-filesystem) but I'm not sure if that's the correct approach for this or whether this approach is just meant for updating a local package temporarily while it's outside version control.

Another option, that seems a little overkill, is to make every module its own repository?

TL;DR; - What's the best practice approach to having multiple modules within the same repository and importing them in in other modules / a root `main.go` file?


----------
        
## Answer \#0

**Accepted** Vote: 17

Created at 2019-03-07 11:58:47

------------

In general a module should be a collection of packages.
But still you can create modules of single packages. As [Volker](https://stackoverflow.com/users/1266756/volker) said, this might only make sense, if you want these packages to have a different lifecycle. It could also make sense, when you want to import these modules from another project and don't want the overhead of the whole collection of packages.
In General:
> A module is a collection of related Go packages that are versioned together as a single unit.
> Modules record precise dependency requirements and create reproducible builds.
> Most often, a version control repository contains exactly one module defined in the repository root. (Multiple modules are supported in a single repository, but typically that would result in more work on an on-going basis than a single module per repository).
> Summarizing the relationship between repositories, modules, and packages:
> 
1. A repository contains one or more Go modules. 2. Each module contains one or more Go packages. 3. Each package consists of one or more Go source files in a single directory.


Source of the Quote: [https://github.com/golang/go/wiki/Modules#modules](https://github.com/golang/go/wiki/Modules#modules)

## To answer the question:


You can do it the way you have shown in your approach


------------
    
    
## Answer \#1

 Vote: 12

Created at 2022-04-23 22:45:24

------------

I understand this is an old question, but there are some more details that are worth mentioning when managing multiple modules in one repository, with or without `go.work`.

## TL;DR


Each approach has pros and cons, but if you are working on a large code base with many modules, I'd suggest sticking to use version handling based on commits or tags, and use Go Workspace for your day to day development.

# Go Module Details



## replace Directive with No Versioning


When you use `replace` directive pointing to a local directory, you will find the version of the dependency module as `v0.0.0-00010101000000-000000000000`. Essentially you get no version information.
With the main `go.mod` defined with `github.com/name/project` module path, `github.com/name/project` module cannot make a reproducible build, because the dependency target for `replace` directive may have had its content updated. This can be especially problematic if the dependency target of `github.com/name/project/players` is used by many modules. Any change in such a common package can result in a behaviour change for all the dependents, all at the same time.
If that's not your concern, `replace` directive should work absolutely fine. In such a setup, `go.work` may be a layer you don't really need.

## With Versioning


If you want to ensure version setup works for reproducible and deterministic build for multiple modules, you can take a few different approaches.

#### One go.mod, one repository


This is probably the easiest approach. For each module, there is a clear commit history and versioning. As long as you refer to the module via remote repository, this is probably the easiest setup to start with, and dependency setup is very clear.
However, note that this approach would mean you'd need to manage multiple repositories, and making `go.work` to help is going to require appropriate local directory mapping, which can be difficult for someone new to the code base.

#### Commit based versioning


It is still possible to deterministically define dependency with version information so that you can build your code, within a single repository. Commit based approach requires least step, and still works nicely. There are some catches to be noted, though.
- `github.com/name/project``github.com/name/project/players``github.com/name/project``github.com/name/project/players``v0.1.1-0.20220418015705-5f504416395d`[details of "pseudo-version"](https://go.dev/ref/mod#pseudo-versions)- `github.com/name/project``/src/mymodule/``github.com/name/project/src/mymodule``github.com/name/project.git`- `go.sum``GOPRIVATE=github.com/name/project`

#### Tag based versioning


Instead of using the commit SHA, you can use Git tags.
But because there could be many modules in one repository, Go Module needs to find which tag maps to which. For example, with the following directory structure:
```
# All assumed to be using `github.com/name/project` prefix before package name
mypackage/          # v1.0.0
anotherpackage/     # v0.5.1
nested/dependency/  # v0.8.3
```

You will need to create tags in `github.com/name/project`, named exactly to match the directory structure, such that:
```
mypackage/v1.0.0
anotherpackage/v0.5.1
nested/dependency/v0.8.3
```

This way, each tag is correctly referenced by Go Module, and your dependency can be kept deterministic.

## go.work Behaviour


If you have `go.work` on a parent directory with `go work use github.com/name/project/players`, etc., that takes precedence and uses the local files. This is even when you have a version specified in your `go.mod`.
For local development, which spans across multiple projects, Go Workspace is a great way to work on multiple things at once, without needing to push the code change for the dependency only first. But at the same time, actual release will still require broken up commits, so that first commit can be referenced later in other code change.
`go.work` is said to be a file you rarely need to commit to the repository. You must be aware of what the impact of having `go.work` in parent paths would be, though.
--
References:
- [https://go.dev/doc/modules/managing-source](https://go.dev/doc/modules/managing-source)- [https://go.dev/ref/mod](https://go.dev/ref/mod)
Side Note:
I have given a talk about this at Go Conference, hosted in Japan - you can find some demo code, slides, etc. [here](https://github.com/rytswd/gocon2022-spring) if you are curious to know more with examples.


------------
    
    
## Answer \#2

 Vote: 10

Created at 2021-10-21 06:19:43

------------

In 2022, the best practice approach to having multiple modules within the same repository and importing them in other modules.
This is supported with a new "go module ".
Released with [Go 1.18 and the new go work command](https://go.dev/doc/go1.18#go-work).
See "[Proposal: Multi-Module Workspaces in cmd/go](https://go.googlesource.com/proposal/+/master/design/45713-workspace.md)" and [issue 45713](https://github.com/golang/go/issues/45713):
> The presence of a `go.work` file in the working directory or a containing directory will put the go command into workspace mode.
The `go.work` file specifies a set of local modules that comprise a workspace.
When invoked in workspace mode, the `go` command will always select these modules and a consistent set of dependencies.
go.work file:
```
go 1.18

directory (
    ./baz // foo.org/bar/baz
    ./tools // golang.org/x/tools
)

replace golang.org/x/net => example.com/fork/net v1.4.5
```

You now have [CL 355689](https://go-review.googlesource.com/c/go/+/355689/)
> 
## cmd/go: add GOWORK to go env command

`GOWORK` will be set to the `go.work` file's path, if in workspace mode
or will be empty otherwise.


------------
    
    