---
context_type: entry
---

Parent: [[lan/2026/topic/concept/000 Atomic/wiki/003 Wiki Clusterline Concepts/003 Wiki Clusterline Concepts]]

Spawned by: [[lan/2026/topic/concept/000 Atomic/wikiproc/003 Wiki Proc Clusterline Concepts/003 Wiki Proc Clusterline Concepts]]

Spawned in: [[lan/2026/topic/concept/000 Atomic/wikiproc/003 Wiki Proc Clusterline Concepts/003 Wiki Proc Clusterline Concepts#^spawn-entry-29d000|^spawn-entry-29d000]]

---

[[000 Term Process Note|process notes]] are the content under a `# Journal` header. Because [[005 Process Notes may contain artifact sections in non journal headers]], we can add a special section `# Decision`. 

This section should be reproducible and justified in the `# Journal`. It specifies facts that are true only due to changes made by the process note. This also includes changes that are decided to come: commitments for future process notes to handle.

The content of `# Decision` corresponds to the note type `Judgment` and may reference to an associated judgment spawned from this process note.

The note type `Judgement` also outputs a decision, and backs it up by facts which themselves may be note types of type `Inference`, or otherwise inlined reasoning citing other notes like `Investigation`s, `Task`s, and `Issue`s and an `# Inference` section in the process note.

If a new decision contradicts an old one, you may signal it by pointing to the decision as a judgment note, or by link to the process note followed by an anchor: `Contradicts <link_to_decision>.`

Decisions can also be supported by inferences found in the `# Inference`  section per [[023 Inference artifact sections enumerate propositions learned through the process note]].