---
title: "Corctf 2022 Whack a Frog Writeup"
description: "Corctf 2022 Whack a Frog Writeup"
date: 2022-08-08T19:38:51+08:00
tags: [corctf, ctf-writeups]
---
# Corctf 2022 whack-a-frog Writeup

## Problem
![](https://s2.loli.net/2022/08/08/MbjphB42ng5FqXa.png)

## Analysis
Analyzing the pcap file, we shall see that there are a lot of `/anticheat?x={x_coordinate}?y={y_coordinate}` requests.

Given the amount of coordinate data, it would make sense to plot these.

## Solution

Required python modules:
- `parse`: The opposite of format, used as a convinent replacement of regex.
- `numpy`: Numerica processing
- `matplotib`: Plotting

### Data processing
```python
from parse import *
with open('whacking-the-froggers.pcap', 'rb') as f:
    lines = f.readlines()

valids = []
for l in lines:
    if b'anticheat' in l:
        valids.append(l)    

pat = "x={:d}&y={:d}"
coords = []

for v in valids:
    res = search(pat, str(v))
    coords.append((res[0], res[1]))
```

The `coords` list should now contain a list of (x,y) coordinate pairs

### Visualization

Displaying the coordinate data in a heatmap with matplotlib

```python
import matplotlib.pyplot as plt
import numpy as np
a = np.zeros((525, 70))

for l in coords:
    a[l]+=1

plt.imshow(a.transpose(), cmap='hot')
plt.show()
```

## The result
![](https://s2.loli.net/2022/08/08/B32fjVKHxAZysIN.png)

From the image we should be able to infer that the message says `LILYXOX`

Thus, the flag is `corctf{LILYXOX}`
