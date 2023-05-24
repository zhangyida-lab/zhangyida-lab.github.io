---
layout: blog
title: ComputerSystem
date: 2023-05-24 13:24:32
tags: softwareDevelopment
---

## \# What are Environment Variables?

Environment variables are global system variables accessible by all the processes/users running under the Operating System (OS), such as Windows, macOS and Linux. Environment variables are useful to store system-wide values, for examples,

PATH: the most frequently-used environment variable, which stores a list of directories to search for executable programs.
OS: the operating system.
COMPUTENAME, USERNAME: stores the computer and current user name.
SystemRoot: the system root directory.
(Windows) HOMEDRIVE, HOMEPATH: Current user's home directory.

## \# How to set Windows Environment Variables

 Set/Unset/Change an Environment Variable for the "Current" CMD Session
 ```

set varname  Display the value of the variable
set varname=value Set or change the value of the variable (Note: no space before and after '=')
set varname=    Delete the variable by setting to empty string (Note: nothing after '=')
set   Display ALL the environment variables





// Append a directory in front of the existing PATH
set PATH=c:\myBin;%PATH%
PATH=c:\myBin;[existing entries]
 ```

## \# How to Add or Change an Environment Variable "Permanently"

   Launch "Control Panel"


## \# How to set (macOS/Linux) Environment Variables


Most of the Unixes (Ubuntu/macOS) use the so-called Bash shell. Under bash shell:

To list all the environment variables, use the command "env" (or "printenv"). You could also use "set" to list all the variables, including all local variables.
To reference a variable, use $varname, with a prefix '$' (Windows uses %varname%).
To print the value of a particular variable, use the command "echo $varname".
To set an environment variable, use the command "export varname=value", which sets the variable and exports it to the global environment (available to other processes). Enclosed the value with double quotes if it contains spaces.
To set a local variable, use the command "varname=value" (or "set varname=value"). Local variable is available within this process only.
To unset a local variable, use command "varname=", i.e., set to empty string (or "unset varname").

## \# How to Set an Environment Variable Permanently in Bash Shell

You can set an environment variable permanently by placing an export command in your Bash shell's startup script "~/.bashrc" (or "~/.bash_profile", or "~/.profile") of your home directory; or "/etc/profile" for system-wide operations. Take note that files beginning with dot (.) is hidden by default. To display hidden files, use command "ls -a" or "ls -al".

For example, to add a directory to the PATH environment variable, add the following line at the end of "~/.bashrc" (or "~/.bash_profile", or "~/.profile"), where ~ denotes the home directory of the current user, or "/etc/profile" for ALL users.

```
// Append a directory in front of the existing PATH
export PATH=/usr/local/mysql/bin:$PATH
// Refresh the bash shell
source ~/.bashrc
// or
source ~/.bash_profile
source ~/.profile
```