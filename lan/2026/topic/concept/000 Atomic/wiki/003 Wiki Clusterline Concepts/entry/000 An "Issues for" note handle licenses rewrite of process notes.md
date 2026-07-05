---
context_type: entry
---

Parent: [[lan/2026/topic/concept/000 Atomic/wiki/003 Wiki Clusterline Concepts/003 Wiki Clusterline Concepts]]

Spawned by: [[lan/2026/topic/concept/000 Atomic/wikiproc/003 Wiki Proc Clusterline Concepts/003 Wiki Proc Clusterline Concepts]]

Spawned in: [[lan/2026/topic/concept/000 Atomic/wikiproc/003 Wiki Proc Clusterline Concepts/003 Wiki Proc Clusterline Concepts#^spawn-entry-4f6e42|^spawn-entry-4f6e42]]

---

Often we have to rewrite a [[000 Term Process Note|note]] because, midway while writing, we find that the content is inaccurate, or an assumption had broke and this requires rewriting that section. 

However, rewriting breaks immutability (contra [[001 Process notes should be immutable to act as an audit]]).

How can we handle this conflict? If we do not rewrite, we risk diluting signal, ie we write long sections and suddenly derail into issue discovery + correction + return back to course.

One possibility is to spawn an "Issues during X" where X is the original note name. Then we move the relevant content leading to a broken assumption or issue into that file. Because it is moved, we do not lost audibility: The state of our knowledge up to the point of an error is preserved there in `>` quotes.

Then we address the new problem.

Because this section was moved out, it may now be rewritten in the process note, allowing the process note to represent the end state, and giving the reader the option to check the "Issues During" note for a linearized record of writing followed by issues encountered and subsequently solved.

This is also good for homework problem corrections. We write a mathematical proof, but we hit a problem. Our algebra was just wrong. We could erase this, and we could make for a much messy note with corrections everywhere, or we can serialize the "note written until problem encountered" part of things, and so our process notes now seems to be rewritable without contradicting [[001 Process notes should be immutable to act as an audit]].