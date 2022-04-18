---
title: "dctf 2022 Problem Secure Creds Writeup"
date: 2022-04-18T08:49:05+08:00
description: "dctf 2022 Secure Creds Writeup"
---

## The problem
![](https://s2.loli.net/2022/04/18/5cRXj2wet7lZOEs.png)

[link to the problem(requires login)](https://dctf.dragonsec.si/challenges#Secure%20Creds-19)

## Solution

First, download the zip file, and unzip it.

You should see the file lsass.DMP

To check the filedump type, use the `file` command:
```sh
file lsass.DMP
```

output:
```lsass.DMP: Mini DuMP crash report, 16 streams, Sat Apr  9 02:47:27 2022, 0x421826 type```

After a google search, the term lsass stands for "Local Security Authority Server Service," and after searching the keywords "lsass minidump" together,
you would encounter a tool called [Mimikatz](https://github.com/ParrotSec/mimikatz)

Finally:
- open up a windows vm if you're not on windows
- disable windows defender
- download&extract [mimikatz_trunk](https://github.com/gentilkiwi/mimikatz/releases/download/2.2.0-20210810/mimikatz_trunk.zip)
- move `lsass.DMP` to `mimikatz_trunk` folder
- cd to `x64` folder and execute `mimikatz.exe`
- execute the commands:
```
cd ../
sekurlsa::minidump lsass.aDMP
sekurlsa::logonPasswords
```

The results:
![](https://s2.loli.net/2022/04/18/KFgP87CSmJonEkY.png)

Inside tspkg, we can see that the password(aka. the flag) is `dctf{n0_ant1v1ru5_l0l}`