---
title: "Introducing ProgressBars in QuickFetch"
datePublished: Sat Jun 29 2024 00:20:25 GMT+0000 (Coordinated Universal Time)
cuid: clxzdloe8000i09ldd2yb2z8v
slug: introducing-progressbars-in-quickfetch
tags: rust

---

Many improvements are being made in QuickFetch `v0.3.0`, while some are making the library more robust, others are nice to haves, such as setting the notification method you'd like, such as the regular logging method, we also introduce progress bars!!!

A demo can be seen below:

[![asciicast](https://asciinema.org/a/Ndm5wD4MG5dM0rCMG1IZi0CuE.svg align="left")](https://asciinema.org/a/Ndm5wD4MG5dM0rCMG1IZi0CuE)

It is important to note that, for obvious reasons, this notification method is only available for `Chunk` and `ByteStream` response methods.

There is still much further work being done, such as trying to see if I can develop a `WatchFetcher` type, which will replace the channel functionality and be used when watching a config file and updating the database accordingly. This will either come in `v0.3.0` or become the main focus on `v0.4.0`.