---
title: FirstDraft
tags:
---

## <font color="green">\#</font> what i want to share on my blog

- about operation system 100 Q&A
- about computer language 100 Q&A
- about hard ware 100 Q&A
- git&docker 100 Q&A
- about erp 100 Q&A
- about mes 100 Q&A
- about plm 100 Q&A
- about archeticture 100 Q&A
- about chinese education 100 Q&A
- about chinese conturyside 100 Q&A
- about human language such as english and chinese 100 Q&A
- about mysql 100 Q&A
- how to use internet correctly
- how to use hexo
- the tips i usually use 100 Q&A
- the above subject i will explian Systematically.
- Information collation, information analysis,expression of opinion
- golang study note
-- start a go project
go command usually use

``` go
$ go mod init go_test/test
go: creating new go.mod: module go_test/test
```

"go mod init"  command create module,a go project can include many modules.we always do not publish my module,we can use this command to replace 
"go mod edit -replace example.com/greetings=../greetings"
 go mod tidy command to synchronize the example.com/hello module's dependencies.
 you can click this [link](https://go.dev/doc/tutorial/call-module-code) to see more detail.

The difference between "go run" "go build" and "go install" command .
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

``` go
 go list -f '{{.Target}}'
```

> <font color="gray"> *the limits of my language means the limits of my world* </font>
> As a user, people should adapt to the machine; as a developer, the machine should adapt to humans

## \# the difference between Compiled language and Interpreted language
  
- A compiled language is converted into machine code so that the processor can execute it.
- An interpreted language is a language in which the implementations execute instructions directly without earlier compiling a program into machine language.
  
## \# how to change markdown font color

``` markdown

<font color="green"> i am red </font>
```

<font color="green"> i am red </font>


:x:

## Hexo offical document is [here](https://hexo.io/docs/commands.html)

### \# This command is used to cerate draft\post and so on 

``` bash
hexo new [layout] <title>
```

### \# This command is used publish your draft

``` bash
hexo publish [layout] <filename>
```

### \# This command is used to generate your html page

``` bash
hexo generate
```

### \# This command is used to preview your blog site

``` bash
hexo server
```

### \# This command is used to list your post\tage\category and so on 

``` bash
hexo list <type>
```

### \# This command is used to delpoy to your site to github page.

``` bash
hexo clean && hexo deploy
```
