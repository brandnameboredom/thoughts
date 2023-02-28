---
title: Runtime Broker
description: Runtime Broker
date: 2016-01-04
tags:
  - Runtime Broker
  - Windows
---

I ran into an issue today with my Windows 10 machine.  I noticed the CPU usage was extremely high and it was, as you would expect, causing the machine to choke.  So, I launched Task Manager and found a little thing called "Runtime Broker" that was causing roughly 60% of the CPU usage.  After a quick search for "Runtime Broker" online I found [this] gem on Reddit.  Shockingly, the simple steps described there fixed the issue immediately and dropped my CPU usage down to a normal percentage.  For anyone too lazy to [click the link to the original post]:

> I found the fix (at least for me) by going to Settings -> System -> Notifications & actions and unselecting "Show me tips about Windows."

<img src="https://cloud.githubusercontent.com/assets/3187885/12092996/6f06ffc2-b2ce-11e5-97f5-8aea14fcd902.PNG" alt="Windows Tips" />

[this]: https://www.reddit.com/r/Windows10/comments/3fe1lx/runtime_broker_cpu_usage_fix/
[click the link to the original post]: https://www.reddit.com/r/Windows10/comments/3fe1lx/runtime_broker_cpu_usage_fix/
