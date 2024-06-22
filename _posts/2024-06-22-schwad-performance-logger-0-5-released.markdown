---
 layout: post
 title:  "schawad_performance_logger 0.5 released"
 date:   2024-06-21 07:34:09 +0000
 comments: false
---

In 2019 I authored the [schwad_performance_logger](https://github.com/Schwad/schwad_performance_logger) gem to solve a problem that seemingly only I had. I wanted a thin DSL for `get_process_mem`, `memory_profiler`, `benchmark-ips` and time performance.

I wanted to package up these tools together and have easy access to them. With one line of code I can get all the information I want.

This weekend I shipped an update.

## Better rendering

The current output of `#log_performance` used to be a sloppy mess. I didn't care because it was _my_ gem. Well I care now. Let's clean that up.

**Before v0.5**

{: style="text-align:center"}
![Favorite](https://i.imgur.com/PDL2Fwt.png)

**After v0.5**

{: style="text-align:center"}
![Favorite](https://i.imgur.com/RsuoVj3.png)

## Better UX

* `#log_performance` is a bit verbose if you're using it a lot. There's also now a `#lp` alias to save your fingers if you want.
* _Block syntax!_ I'm super excited about this. I often found I wanted to inspect the performance of a "chunk" of code along the way. But I'd have to do two logs - before *and* after the block. Now you can wrap that up neatly in a block and the logger will let you know performance details within the block.
* Cleanups. The raw logger used to always start memos with "Starting  #{your_memo}", now, it's just your memo. `CSV` now has headers. Logfile writing output is cleaned up a bit

**Before v0.5**

{: style="text-align:center"}
![Favorite](https://i.imgur.com/6MCMPCq.png)

**After v0.5**

{: style="text-align:center"}
![Favorite](https://i.imgur.com/E6Ib2v8.png)
