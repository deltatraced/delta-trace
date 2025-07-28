---
status: todo
---

# 1 Objective

I used to write math in vscode. I came up with a system for equational reasoning using LaTeX.  It allowed steps and annotations/comments. I would like to replicate some of this here.

Maybe we would approach it differently by having many LaTeX blocks instead with the step-no/annotations being just in markdown. 

but we also had custom math functions and symbols and that could be good to replicate too, like some LaTeX file to load here.

# 2 Journal

## 2.1 Searching on extending LaTeX in obsidian

2025-07-28 Wk 31 Mon - 04:04

One issue also is whether github web view will be able to recognize it... chances are not?

2025-07-28 Wk 31 Mon - 05:49

This could also be useful:

[gh RyotaUshio/Obsidian LaTeX theorem equation referencerer](https://github.com/RyotaUshio/obsidian-latex-theorem-equation-referencer) [(obsidian)](obsidian://show-plugin?id=math-booster).


[gh artisticat1/obsidian-tikzjax](https://github.com/artisticat1/obsidian-tikzjax) [(obsidian)](obsidian://show-plugin?id=obsidian-tikzjax).

This one can render really cool graphs.


For what we need, there is 

[gh raineszm/Obsidian latex environments](https://github.com/raineszm/obsidian-latex-environments) [(obsidian)](obsidian://show-plugin?id=obsidian-latex-environments).

Seems to be local to the place it's injected? And requires some safe mode thing to be turned of...

[this forum post](https://forum.obsidian.md/t/requires-latex-functionality/57270/7) explains that new commands can be added, but are only loaded if the page itself is being opened. I guess... we could try to reference a setting page in every note that uses its commands?

![[LaTeX config#1.1 Argmin Argmax]]

They have some example

$$
\argmin_{text}
$$


It seems I have to reload the app to apply changes to these commands with `Ctrl+P Reload app without saving`. Or just closing the page and reopening it again seems to bring about effect too.


These commands are now rendering in github

![[Pasted image 20250728061104.png]]

I don't mind mirroring X sections from other notes for tailored inclusion of commands like this. But doing it automatically would be good. There should be an original section, and mirroring sections. See [[#3.1 On obsidian extensions for mirrored and synced text across notes]].

# 3 Investigations

## 3.1 On obsidian extensions for mirrored and synced text across notes

2025-07-28 Wk 31 Mon - 06:29

[This obsidian forum post](https://forum.obsidian.md/t/obsidian-plugin-to-mirror-blocks-e-g-synced-tasks/80442) has the idea.

We can already copy sections this way:

![[000 About book - A transition to Advanced Mathematics]]





### 3.1.1 Pend


# 4 References