---
title: golangQA
date: 2023-05-05 14:50:21
tags: CS
---
## \# What does "range" mean in golang?

In each iterat ion of the loop, range produces a pair of values: the index and the value of the
element at that index.

## \# What is method in golang?

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

