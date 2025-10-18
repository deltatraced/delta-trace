---
parent: "[[003 Ideas]]"
spawned_by: "[[003 Ideas]]"
context_type: entry
---

Parent: [[003 Ideas]]

Spawned by: [[003 Ideas]]

Spawned in: [[003 Ideas#^spawn-entry-32422f|^spawn-entry-32422f]]

[[Mn 10 October]]

# 1 Purpose

Ideas flash by throughout the month, so I capture them here to get a better sense for possibilities!

# 2 Journal

2025-10-16 Wk 42 Thu - 09:59 +03:00

(1)

During [[001 Reading through lukaswirth.dev decl-macros]],

For the obsidian knowledge graph rendering, we could adopt a similar design to the rust reference similar to [here](https://doc.rust-lang.org/reference/macros-by-example.html#r-macro.decl.transcription.intro)

![[Pasted image 20251016100042.png]]

This section could be written in obsidian with block identifiers as follows:

```
# Transcribing

^macro.decl.transcription

When a macro is invoked, the macro expander looks up macro invocations by name, and tries each macro rule in turn. It transcribes the first successful match; if this results in an error, then future matches are not tried.

^macro.decl.transcription.intro
```

Then they can be linked to and used as anchors for referring to specific sections of files