---
title: "TIL: How shodan takes screenshots of computers"
description: "Accessing login screens of RDP servers without Network Level Authentication"
date: 2022-12-11T19:20:46+08:00
tags: [TIL, OSINT]
---
Recently, I have been playing around with [shodan images](https://images.shodan.io/), a service provided by shodan that aggregates screenshots of accesible videofeeds over the internet of things, including a lot of windows login screens and webcams.

![CleanShot 2022-12-11 at 20.12.47.png](https://s2.loli.net/2022/12/11/YCHW8bwLdlkhxBU.png)

The catch is, while they provide the screenshot for the users, shodan provides absolutely no hints as of how they obtained the screenshot.

Shodan [claims](https://security.stackexchange.com/questions/244308/how-does-shodan-take-screenshots-from-webcams-which-have-authentication) that they grabbed those screenshots based as the RDP service is exposed to the internet, but when I tried to access the remote desktops with Microsoft Remote Desktop, it immediately prompted me for a username and password, which I do not have.

After some digging on the internet, I came across this post on [reddit](https://www.reddit.com/r/hacking/comments/w1dli6/how_does_shodan_grab_rdp_screenshots_of_windows), 
which says that the servers that have a login screen scraped by shodan doesn't have [Network Level Authentication](https://en.wikipedia.org/wiki/Network_Level_Authentication) enabled.
While Microsoft RDP client enforces Network Level Authentication by default on the client side, it could be bypassed if the server doesn't enforce it with a custom client.

While [rdesktop](http://www.rdesktop.org/) was suggested, it seems to be unmaintained and brew wouldn't let me install it.
Instead, I came across [freerdp](https://www.freerdp.com/), an actively maintained, fully featured, though less user friendly(CLI based) RDP client.

To access a RDP server without NLA, create a regular rdp file from the target machine's ip address, and add the following line to the file:
```
enablecredsspsupport:i:0
```

Or, you can paste the snippet below, and switch `<YOUR_IP_ADDRESS>` for the target machine's ip address

{{< gist JettChenT aac48ce540f001ad0754d42b640e8832>}}

After [installing freerdp](https://github.com/FreeRDP/FreeRDP/wiki/PreBuilds) (and [xquartz](https://www.xquartz.org) if you're on mac),
simply run the follwing command:
```
xfreerdp ./sample.rdp 
```

And voila, you should be able to access the login screen of the target machine just like shodan.

Yes, you still need the password in order to access the machine, but the ability to access the machine reveals all the user accounts 
so that hackers would only have to enumerate the passwords for that specific account, potentially revealing your identity information,
and opens up more possibilities for [further attacks](https://en.wikipedia.org/wiki/BlueKeep).