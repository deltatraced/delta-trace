---
context_type: entry
---

Parent: [[lan/2026/topic/ideas/000 Ideas/entry/002 Terminal Editor Toolkit/002 Terminal Editor Toolkit]]

Spawned by: [[lan/2026/topic/ideas/000 Ideas/entry/002 Terminal Editor Toolkit/002 Terminal Editor Toolkit]]

Spawned in: [[lan/2026/topic/ideas/000 Ideas/entry/002 Terminal Editor Toolkit/002 Terminal Editor Toolkit#^spawn-entry-ca5988|^spawn-entry-ca5988]]

# Journal

2026-08-03 Wk 32 Mon - 06:52 +03:00

Looking for rust libraries where I could quickly assemble an editor

https://ratatui.rs/

They also list an alternative: https://crates.io/crates/iocraft

When I was looking at silverbullet's source, I found that it's built on top of https://codemirror.net/. So we do see a pattern of tools using a common core, but it would be better if we could simply express the editor we want through composition.