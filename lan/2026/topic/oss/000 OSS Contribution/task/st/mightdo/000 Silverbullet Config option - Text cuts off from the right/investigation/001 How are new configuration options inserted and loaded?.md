---
context_type: investigation
status: done
---

Parent: [[lan/2026/topic/oss/000 OSS Contrib/task/st/mightdo/000 SB Option to only have base name in title/000 SB Option to only have base name in title]]

Spawned by: [[lan/2026/topic/oss/000 OSS Contrib/task/st/mightdo/000 SB Option to only have base name in title/000 SB Option to only have base name in title]]

Spawned in: [[lan/2026/topic/oss/000 OSS Contrib/task/st/mightdo/000 SB Option to only have base name in title/000 SB Option to only have base name in title#^spawn-invst-e3e809|^spawn-invst-e3e809]]

Overview: [[001 Overview SB Option to only have base name in title]]


---

# Problem

We would like to be able to insert a new config option in accordance with https://github.com/silverbulletmd/silverbullet/issues/2016. But how can we do this in the source?

# Solution

For the project itself. In `libraries/Library/Std/Config.md`, you can add configuration under a given category. For example here in category `Editor`:

```lua
config.define("onlyShowPageNameForPageTitle", {
  description = "Page titles by default show the entire path to the page. This shows the name only.",
  type = "boolean",
  default = false,
  ui = { category = "Editor", label = "Only show name for page title", priority = 1 },
})
```

Reading and writing values is done through syscalls. New categories can also be defined that way. See `plug-api/syscalls/config.ts`.

---

# Journal

## How do I add a new configuration option for the core project?

2026-06-08 Wk 24 Mon - 16:55 +03:00

In `plugs/configuration-manager/configuration-manager.plug.yaml`,

```ts
  openConfiguration:
    path: ./configuration.ts:openConfiguration
    command:
      name: "Configuration: Open"
```

CONFIG mentions:

```
This page holds configuration for your SilverBullet space. See [[^Library/Std/Config]] for all options and defaults.
```

`libraries/Library/Std/Config.md`

We are able to add new configuration options to the schema through the lua in `libraries/Library/Std/Config.md`. For this PR:

```lua
config.define("onlyShowPageNameForPageTitle", {
  description = "Page titles by default show the entire path to the page. This shows the name only.",
  type = "boolean",
  default = false,
  ui = { category = "Editor", label = "Only show name for page title", priority = 1 },
})
```
