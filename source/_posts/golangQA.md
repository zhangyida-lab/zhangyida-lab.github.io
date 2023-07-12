---
title: golangQA
date: 2023-05-05 14:50:21
tags: softwareDevelopment
---

> 好读书，不求甚解，毎有会意，便欣然忘食。——《五柳先生传》

## \# What does "range" mean in golang?

In each iterat ion of the loop, range produces a pair of values: the index and the value of the
element at that index.

## \# What is method in golang?
<!--more-->
An object-oriented program is one that uses methods to
express the properties and operations of each data structure so that clients need not access the
object’s representation directly.

An object is simply a value or variable that has methods, and a method is a function
associated with a particular type.

## \# What can go command do in golang?

|GO COMMAND|USAGE|
|---|---|
|build| compile packages and dependencies|
|doc |show documentation for package or symbol|
|env |print Go environment information|
|fmt |run gofmt on package sources|
|get |download and install packages and dependencies|
|install| compile and install packages and dependencies|
|list| list packages|
|run |compile and run Go program|
|test| test packages|
|vers|ion print Go version|
|vet |run go tool vet on packages|
|clean| remove object files|

## \# What's the difference between "go run" "go build" and "go install" command?

While the go run command is a useful shortcut for compiling and running a program when you're making frequent changes, it doesn't generate a binary executable
The go build command compiles the packages, along with their dependencies, but it doesn't install the results.You've compiled the application into an executable so you can run it. But to run it , your prompt needs to be in the executable's directory.
if I go to the parent folder ,there will be an error like this;

``` go
$ ./hellozhangyi
bash: ./hellozhangyi: No such file or directory
```

if I run the command in current folder ,it will be ok,like this:

``` go
$ ./hellozhangyi
Hello, World!
```

The go install command compiles and installs the packages.you'll install the executable so you can run it without specifying its path.
what does "install" mean? it means you can use your go program everywhere,if you  add the Go install directory to your system's shell path.
in your go project run this command ,to find out your  install path.

## \# What does GOPATH do in golang?

The only configuration most users ever need is the GOPATH environment variable, which specifies the root of the workspace. 

## \# What does "import path" do in golang?

Each package is identified by aunique string called its import pa h. Import paths are the
strings that appear in import declarations.
When using the go tool, a package’s import path indicates not only where to find it in the local
workspace, but where to find it on the Internet so that go get can retrieve and update it.

## \# Where go get download file?

go get module caches the module in GOPATH/pkg/mod.

## \# How to use the resource by the golang book?

You can download, build, and run the programs with the following commands:

```go

$ export GOPATH=$HOME/gobook            # choose workspace directory
$ go get gopl.io/ch1/helloworld         # fetch, build, install
$ $GOPATH/bin/helloworld                # run
Hello, 世界
```

## \# What CS language consisit of normally?

A language typically consists of the language specifications (syntax), standard library, a runtime environment, and a compiler.

## \# What is go workspace?

A workspace is Go’s way to facilitate project management. A workspace, in a nutshell, is a directory on your system where Go looks for source code files, manages dependency packages and build distribution binary files.

Whenever a Go program encounters an import statement, it looks for the package in the Go’s standard library ($GOROOT/src). If the package is not available there, then Go refers to the system's environment variable GOPATH which is the path to Go workspace directory and looks for packages in $GOPATH/src directory.

> **You can have as many workspaces as you want, as long as you keep GOPATH environment variable pointed to the current working workspace directory whenever you are working on the given project.**


> Similar to $GOROOT, $GOPATH by default points to $HOME/go directory in UNIX and %USERPROFILE%\go on windows. Hence, it is not absolutely necessary to setup GOPATH environment variable.

## \# What about interface?

Any data type that implements an interface can also be represented as a type of that interface (polymorphism)



## \# What is utf8?

 UTF-8 character can be defined in memory size from 1 byte (ASCII compatible) to 4 bytes. Hence in Go, all characters are represented in int32 (size of 4 bytes) data type.
  A code unit is the number of bits an encoding uses for one single unit cell. So UTF-8 uses 8 bits and UTF-16 uses 16 bits for a code unit, that means UTF-8 needs minimum 8 bits or 1 byte to represent a character.

  As character represented in single quotes in Go is rune and rune can be compared because they represent Unicode code points (int32 values).



## \# What is popcount?

  popcount（population count），也叫 sideways sum，是计算一个整数的二进制表示有多少位是1


## \# How does go code organization?

 Go programs are organized into packages. A package is a collection of source files in the same directory that are compiled together. Functions, types, variables, and constants defined in one source file are visible to all other source files within the same package.

A repository contains one or more modules. A module is a collection of related Go packages that are released together. A Go repository typically contains only one module, located at the root of the repository. A file named go.mod there declares the module path: the import path prefix for all packages within the module. The module contains the packages in the directory containing its go.mod file as well as subdirectories of that directory, up to the next subdirectory containing another go.mod file (if any).

Note that you don't need to publish your code to a remote repository before you can build it. A module can be defined locally without belonging to a repository. However, it's a good habit to organize your code as if you will publish it someday.

Each module's path not only serves as an import path prefix for its packages, but also indicates where the go command should look to download it. For example, in order to download the module golang.org/x/tools, the go command would consult the repository indicated by https://golang.org/x/tools (described more here).

An import path is a string used to import a package. A package's import path is its module path joined with its subdirectory within the module. For example, the module github.com/google/go-cmp contains a package in the directory cmp/. That package's import path is github.com/google/go-cmp/cmp. Packages in the standard library do not have a module path prefix.


## \# Is slices  like interface?

From the slices lesson, we learned that a slice holds the reference to an array. Similarly, we can say that an interface also works in a similar way by dynamically holding a reference to the underlying type.
Interfaces are very useful in case of functions and methods where you need argument of dynamic types.

--------

There are some golang offical document you can click:[goDoc](https://go.dev/doc/),[glang playground](https://go.dev/play/),[the tour of golang](https://go.dev/tour/welcome/1)