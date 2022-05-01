---
title: "NahamCon CTF 2022 Ostrich Writeup"
date: 2022-05-01T09:41:01+08:00
description: "Solution to Nahamcon CTF 2022 problem Ostrich"
tags: [ctf-writeups, nahamcon-ctf, steganography]
---
## The Problem

![](https://s2.loli.net/2022/05/01/FrRS3XxNvnoicWt.png)
[nahamcon ctf page](https://ctf.nahamcon.com/challenges)

## Preprocessing
Downloading the three files, we can see that we have a python program `ostrich.py` which takes each character of the flag, make some modifications to `ostrich.jpg`,
and store it as a frame in `result.apng`.

First, extract the pictures of `result.apng` to a folder:
```python
from apng import APNG

im = APNG.open('result.apng')
for i, (png, control) in enumerate(im.frames):
	png.save(f"./extracted/{i}.png")

print(f"total of {len(im.frames)} images")
```
Results:
`total of 38 images`

## Observation

TLDR: [solution](#solution)

Looking at `ostrich.py`:
```python
import imageio
from PIL import Image, GifImagePlugin
from Crypto.Util.number import long_to_bytes as l2b, bytes_to_long as b2l
import random
from apng import APNG

filenames = []
flag = "REDACTED" 

orig_filename = "ostrich.jpg"
orig_image = Image.open(orig_filename)
pixels = orig_image.load()
width, height = orig_image.size
images = []

for i in range(len(flag)):
    new_filename = f'./images/ostrich{i}.png'
    new_image = Image.new(orig_image.mode, orig_image.size)
    new_pixels = new_image.load()
    for x in range(width):
        for y in range(height):
            new_pixels[x,y] = orig_image.getpixel((x, y))

    x = random.randrange(0,width)
    y = random.randrange(0,height)
    pixel = list(orig_image.getpixel((x, y)))
    while(pixel[2] == 0):
        x = random.randrange(0,width)
        y = random.randrange(0,height)
        pixel = list(orig_image.getpixel((random.randrange(0,width), random.randrange(0,height))))
    
    new_val = l2b(pixel[2]*ord(flag[i]))
    pixel[0] = new_val[0]
    if len(new_val) > 1:
        pixel[1] = new_val[1]
    pixel[2] = 0

    new_pixels[x, y] = (pixel[0], pixel[1], pixel[2])
    new_image.save(new_filename)
    filenames.append(new_filename)
    images.append(new_image)

APNG.from_files(filenames, delay=0).save("result.apng")
```

We can see that the pixel picking algorithm is a little weird. If the initially choosen pixel has a non-zero `b` value, the information would be stored in the same location as the pixel. Otherwise, the program will pick another pixel with a non-zero `b` value, however, the information would be stored in a totally different location.

In the latter case, it would be nearly impossible to recover the original flag, as we don't have the position of the original pixel, thus not knowing the original `pixel[2]` value. (If anyone knows how to solve the latter situation plz leave a comment)

However, I found that only 3605 out of the 584000 pixels has a zero `b` value, which means that there's only a 0.62% chance that we would encounter the second situation for each frame. Considering that there are 38 images to extract, there's a 21% chance that the second case happens at least once, so let's take the risk and assume that the second case doesn't execute at all.

Thus, we can take the images of each frame, find the pixel that was modified, and decode it with the information of the original pixel

## Solution

```python
from PIL import Image
from Crypto.Util.number import bytes_to_long as b2l
TOTAL_N = 38
base_im = Image.open('./ostrich.jpg')
wid, hei = base_im.size
images = []

for i in range(TOTAL_N):
	images.append(Image.open(f'./extracted/{i}.png'))


res = ""
for im in images:
	foo = False
	for x in range(wid):
		for y in range(hei):
			orig_pixel = list(base_im.getpixel((x,y)))
			new_pixel = list(im.getpixel((x,y)))
			if new_pixel[2]!=0 or orig_pixel[2]==0: #if the pixel is not modified
				continue
			# create list of bytes
			blist = []
			blist.append(new_pixel[0])
			if(new_pixel[1]!=orig_pixel[1]):
				blist.append(new_pixel[1])
			# decode list of bytes (see original program's encoding process)
			cur = int(b2l(bytes(blist))/orig_pixel[2])
			# print(chr(cur))
			res += chr(cur)
			foo = True
			break
		if foo:
			break

print(res)
``` 

After running(should take several seconds), the result is:
`flag{d3a5b80f96a3ce0dd0aedbefbc6b1fa1}`