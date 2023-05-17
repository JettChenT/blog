---
title: "Why archive.org can't prove the authenticity of their snapshots"
description: "... and why you should probably use them anyways"
date: 2023-05-15T00:47:56+08:00
tags: [TIL, Security]
---

Recently, a federal judge ruled against the Internet Archive in their mission to preserve ebooks. While this doesn't have anything to do with the WayBack Machine, it led me to wonder: why do we need a centralized platform to take and share website snapshots in the first place? Why not let users take them themselves and share them around the internet?

The answer to this question seems obvious at first glance. Any user can simply open up a text editor, modify the HTML, and share it with others. This would make it impossible to tell if the shared snapshot had been tampered with. But can't we leverage the little lock on top of our URL bars ‒ the one that assures us that the data we're receiving is from a trusted server ‒ to ensure the authenticity of website snapshots as well?

Well... it’s not that simple. While HTTPS(the protocol behind that little lock) this provides asymmetric encryption during the server's key exchange phase to ensure that the data sent is not fabricated by a client, the actual data transmission between client and server happens via symmetric encryption. This means that clients can "pretend" to a third party that a server sent something to them, and there's no way to disprove this claim. Similarly, there's no way to prove that data a client received was sent by the server. Therefore, HTTPS does not provide non-repudiation ‒ a feature needed to ensure that data cannot be denied during or after a transaction.

Thus, while HTTPS establishes trust between client and server, it doesn't establish trust for third party notaries. While cryptographic timestamping can prevent repudiation to a certain degree, tampering is still possible during snapshotting. This means that we can only trust website snapshots as much as the ones who took the snapshot, which is why we still rely trusted third parties like [archive.org](http://archive.org) and [archive.is](http://archive.is) to maintain some sense of truth in a digital world that inherently allows for fabrication.