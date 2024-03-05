---
title: EverythingAboutLinux
tags: operatingSystem
---

> The kernel does this by providing a software layer to manage the limited resources of a computer.

## Tasks performed by the kernel

- Process scheduling
- Memory management
- Provision of a file system
- Creation and termination of processes
- Access to devices
- Networking
- Provision of a system call application programming interface
- Provision of virtual private computer

## linux command 

| command | ful name | remark |
| ---- | ---- | ---- |
| SAR | System Activity Reporter | sudo apt-get install sysstat |
|LDD |List Dynamic Dependencies|

## how to use proxy on your wsl system

- create a file named .wslconfig in this folder:

  ``` shell
  C:\Users\ %USERPROFILE%\.wslconfig 
  ```

- the .wslconfig content is this:

``` shell
[experimental]
autoMemoryReclaim=gradual  # gradual  | dropcache | disabled
networkingMode=mirrored 
dnsTunneling=true
firewall=true
autoProxy=true

```

- shut down your wsl system using this command
- use wget command test your proxy



## monunt disk

- lsblk 
  list the block device

- mount the device to the directory you create

```shell
sudo mount dev/sdb2 /media/mydrivename
```



## LINUX SHORT NAME

|fullName|shortName|description|---|---|
|----|---|---|---|---|
|Teletypewriter |tty|---|---|---|