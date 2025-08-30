---
status: done
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

# 1 Config

## 1.1 Argmin Argmax

$$
\DeclareMathOperator*{\argmin}{arg\,min}
\DeclareMathOperator*{\argmax}{arg\,max}
$$
<a name="argmin-argmax" />^argmin-argmax

## 1.2 Of Type

$$
$$
<a name="operator-oft" />^operator-oft

They have some example

$$
\argmin_{text}
$$
$$
$$

It seems I have to reload the app to apply changes to these commands with `Ctrl+P Reload app without saving`. Or just closing the page and reopening it again seems to bring about effect too.

These commands are now rendering in github

![Pasted image 20250728061104.png](../../../../../../attachments/Pasted%20image%2020250728061104.png)

I don't mind mirroring X sections from other notes for tailored inclusion of commands like this. But doing it automatically would be good. There should be an original section, and mirroring sections. See [3.1 On obsidian extensions for mirrored and synced text across notes](000%20Creating%20equation%20chain%20LaTeX%20shortcuts%20and%20others.md#31-on-obsidian-extensions-for-mirrored-and-synced-text-across-notes).

2025-07-28 Wk 31 Mon - 06:59

Let's try to find some of my equation commands from before and embed them for use here.

````
$$

% Custom commands
\newcommand{\mathcomm}[1]{& \gray{\text{// #1}} \\}
\newcommand{\mathstep}[1]{\text{#1. }}
\newcommand{\mathcncl}[1]{\text{\(\therefore\) }}
\newcommand{\mathln}[1]{& \ensuremath{\text{#1}} \\}

% Custom environment using \long\def to avoid alignment errors
\newcommand{\mymath}[1]{%
    \begingroup
    \allowdisplaybreaks{}
    \begin{align*}
        \tag{\theequation} \stepcounter{equation}
        #1
    \end{align*}
    \endgroup
}
$$
````

This doesn't really fit in right away, and it may be easier to just do hybrid markdown + LaTeX for long equational reasoning chains.

## 2.2 Redoing equational reasoning chains with Markdown and LaTeX

2025-07-28 Wk 31 Mon - 07:18

Without need for custom commands

They have some recommendations involving Obsidian in this [reddit post](https://www.reddit.com/r/ObsidianMD/comments/12rwjqf/math_and_science_people_how_do_you_write/) and some hybrid use of Obsidian with [lyx](https://www.lyx.org/) and also some obsidian extensions mentioned.

They recommend the [Latex Suite plugin](https://github.com/artisticat1/obsidian-latex-suite) [(obsidian)](obsidian://show-plugin?id=obsidian-latex-suite). We can also add configuration via this rather than via command embeds.

This [post](https://forum.obsidian.md/t/create-aligned-latex-equations/27554/2) talks about aligned equations.

1. Describe first logical step

$$
\begin{aligned}
&1+1 \\
&= 2 \\
&= 3-1 \\
&= 4-2 \\
\end{aligned}
$$

<a name="6d7ec9" />^6d7ec9

2. Describe second logical step

$$
\tag{eq1}
\begin{aligned}
&g(x) \\
&= 4-2 \\
&= 5-3 \\
&= 6-4 \\
\end{aligned}
$$

This seems cleaner, keep the math LaTeX and the text markdown.

Next we need equation referencing...

[gh RyotaUshio/Obsidian LaTeX theorem equation referencerer](https://github.com/RyotaUshio/obsidian-latex-theorem-equation-referencer) [(obsidian)](obsidian://show-plugin?id=math-booster).

It also needs [gh zhaoshenzhai/obsidian-mathlinks](https://github.com/zhaoshenzhai/obsidian-mathlinks) [(obsidian)](obsidian://show-plugin?id=mathlinks)

This was for that, but not sure how to use it.

We can add tags like `\tag{1}`. But those would be manual...

$$
h(x)
$$

<a name="b5d125" />^b5d125

We can add links to the equations by doing `[ [ # ^` (without spaces, obsidian insists on interpreting) and then details of the equation content. But it doesn't yet show numbering.

[^b5d125](000%20Creating%20equation%20chain%20LaTeX%20shortcuts%20and%20others.md#b5d125)

[^6d7ec9](000%20Creating%20equation%20chain%20LaTeX%20shortcuts%20and%20others.md#6d7ec9)

### 2.2.1 Pend

# 3 Investigations

## 3.1 On obsidian extensions for mirrored and synced text across notes

2025-07-28 Wk 31 Mon - 06:29

[This obsidian forum post](https://forum.obsidian.md/t/obsidian-plugin-to-mirror-blocks-e-g-synced-tasks/80442) has the idea.

We can already copy sections this way:

I found this book [here](../../../../resources/math/2025/000%20Math%20Studying%20Direction.md#22-finding-recommendations-on-prereqs-for-type-theory).

It can be read [here](https://mathdept.byu.edu/%7Epace/Transition_v104.pdf).

# 1 Entries

[Wk 30 Starting out](../../../books/math/2025/000%20A%20transition%20to%20Advanced%20Mathematics/entries/weekly/2025/Wk%2030%20000%20Math%20Book%20000%20Starting%20out.md)

This feature is [embeds](https://help.obsidian.md/embeds) in obsidian.

2025-07-28 Wk 31 Mon - 06:59

I can't seem to find an extension for this. It's two things for better github readability. To replace attachments with github links where possible, and to just insert literal content of the other type of link embeds for LaTeX interpretation in github.

I guess we could call it the `Github Embed Display` plugin or something

# 4 References
