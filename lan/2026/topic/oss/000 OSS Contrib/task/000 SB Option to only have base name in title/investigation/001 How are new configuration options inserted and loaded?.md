---
context_type: investigation
status: done
---

Parent: [[000 SB Option to only have base name in title]]

Spawned by: [[000 SB Option to only have base name in title]]

Spawned in: [[000 SB Option to only have base name in title#^spawn-invst-e3e809|^spawn-invst-e3e809]]

# Problem

We would like to be able to insert a new config option in accordance with https://github.com/silverbulletmd/silverbullet/issues/2016. But how can we do this in the source?

# Journal

2026-06-08 Wk 24 Mon - 16:55 +03:00

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
