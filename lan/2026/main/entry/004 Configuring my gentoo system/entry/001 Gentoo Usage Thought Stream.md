---
context_type: entry
---

Parent: [[lan/2026/main/entry/004 Configuring my gentoo system/004 Configuring my gentoo system]]

Spawned by: [[lan/2026/main/entry/004 Configuring my gentoo system/entry/000 Spawns for Configuring my gentoo system]]

Spawned in: [[lan/2026/main/entry/004 Configuring my gentoo system/entry/000 Spawns for Configuring my gentoo system#^spawn-entry-078816|^spawn-entry-078816]]

# Journal

2026-07-26 Wk 30 Sun - 20:59 +03:00

Right now my workflow with switching is often `win+s window_name`, but then I have to keep fullscreening that again, so `win+s window_name win+f`.

At first I thought I will modify `sws` so that it fullscreens by default, but I am not yet sure if I always want to be fullscreened. I probably *do* want to make use of my two monitors so that they host more than just 2 windows, so that will eventually be disruptive.

One possible way to deal with this is to have it automatically full screen but only if we are in the tabs layout. In other layouts, I actually do want the spatial configuration, and it shouldn't be automatically disrupted. If it happens that I do want the tabbed layout after all, I will undo the fullscreen with win+f. It is a tradeoff, I believe I will want to switch to a tab full screen more often than to make use of the tabbed layout.

In general personally I prefer not to cycle through more than 3 tabs max, since it ends up being a noticable search.

Spawn [[lan/2026/main/entry/004 Configuring my gentoo system/task/000 Modify sway switcher to automatically fullscreen on swap if in tab layout]] ^spawn-task-5511da

2026-07-27 Wk 31 Mon - 11:02 +03:00

I kind of like just using `./start_sway.sh` at boot instead of automating it. The login can remain purely at the TTY1 level. 