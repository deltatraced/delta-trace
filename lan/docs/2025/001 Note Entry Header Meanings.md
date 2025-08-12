# 1 Objective

Here we state the objective of the note in a concise matter. This may include how it relates to other notes and problems, and may have tasks for objectives we need to get done like

- [ ] File a bug report

# 2 Journal

This is a sequential section where we log progress on the task or entry as we move along. It's meant to be linearly read. Other sections below, on the other hand, are record-based. For example we may spawn a task, issue, or investigation from here. The order with which they are spawned will hint at some linearity or progress, but subsequent tasks can also reference prior ones so the ordering is not as strict. 

Let's spawn a task. Let's write some special keyword, like SPW0. This allows this line to be easily identified so that we can create a back reference from the spawned task back here. 

Now when we add the new task under the Tasks section, we can just write `[ [ ^SPW0` and select the line and Obsidian generates a random record for it quickly. We can follow that link back with `Ctrl + .` and replace `SPW0` with  `Spawn {internal link}.`

This lets Obsidian generate a link for the line quickly. Then we change `^{xxxxxx}` to `^spawn-task-{xxxxxx}` to make the spawn category clear.

Sometimes we may shorten this and just inline the spawned task under a name like "Task." that is clickable followed by the `^spawn-task-xxxxxx`. But for now, let's do it fully:

Spawn [[#3.1 File a bug report]]. ^spawn-task-c33094

2025-08-12 Wk 33 Tue - 13:55

I wouldn't normally do the "Go back to Spawner" thing when taking actual notes, but It's a cool thing to insert linearity into our tutorial of a non-linear note taking style. 

We've now covered what many sections do!

The template for these headings can be found here:

[[Entry Note Headers]].

Thank you for going through my non-linear tutorial!

# 3 Tasks

## 3.1 File a bug report

- [ ] Here we specify some objectives that has to be completed
- [ ] Organized my documents

From [[#^spawn-task-c33094]].

2025-08-12 Wk 33 Tue - 13:14

As you see I usually add a timestamp above. This could be done in obsidian with `Ctrl+P QuickAdd: Run QuickAdd -> insert-datetime`. I usually do this very quickly. The keys are `Ctrl+P Q <Enter> <Enter>`.

We can now  journal for the limited context of this task here, or we can summon other tasks, reference other tasks, add subheadings, etc.

Notice I added a `### Status` below. This shows the status for the Task in the outline quickly so I know what is pending. Typically if the task is finished, I put no such heading status for brevity. 

This is in the case I want to signify the task is pending:

![[Pasted image 20250812131705.png]]

2025-08-12 Wk 33 Tue - 13:17

Notice I added another timestamp above. I take notes in a logging style where I come and log the next thought after some time, or I use timestamps to signify context change. For big context change, I use subheadings.

Now let's spawn an issue next to move on with the tutorial! 

Spawn [[#4.1 Paint.exe crashed with error code 0xDEADFEED]]. ^spawn-issue-252c2b

2025-08-12 Wk 33 Tue - 13:55

And again! [[#^spawn-task-c33094|Spawner]].

### 3.1.1 Status

## 3.2 Setup a fresh installation of Windows on a VM

from [[#^spawn-task-603822]]

2025-08-12 Wk 33 Tue - 13:45

We're back in the Tasks section from the Investigation section! Because this is a clearly operative thing to get done!

OK Let's go back to our [[#^spawn-task-603822|spawner]].

# 4 Issues

## 4.1 Paint.exe crashed with error code 0xDEADFEED

- [x] Resolved, turns out I made this error up for demonstration purposes!

From [[#^spawn-issue-252c2b]]

2025-08-12 Wk 33 Tue - 13:20

So tasks usually have names that are dos. "Wash your laundry!" "Implement keyboard debouncing", etc...

Issues are problems that occur, and names should be more descriptions of the issue encountered. The spawning context can inform us of how we ran into this issue. We can also write in the journal body of the issue about the context if needed.

This will ensure that issues can be searched by the context that describes them and will allow us to build up a database of issues encountered over time if we ever run into the same issue twice or someone else runs into our issues.

Issues may be marked resolved or unresolved, they may be skipped if we believe we need to go a different direction.

So you're trying to resolve this issue, but you need to figure out how to open the windows registry... Wait how do we do that again?

This is what a HowTo is for! 

Spawn [[[#5.1 Open the Windows Registery to fix Paint.exe!]]]. ^spawn-howto-9f5c66

2025-08-12 Wk 33 Tue - 13:29

You're back! You just got exposed to non-linear note flow! Hopefully you did follow the spawn instead of just following through here?

Anyway we discover now that in order to resolve this issue, it's not enough to figure out registry stuff... We actually need to learn about the internals of the Windows operating system! 

What an open-ended conceptual task! Tasks should preferably be operative and have a strong sense of what it means to complete them. For the more open-ended conceptual stuff, we got the Investigations section!

If I'm exploring a concept, a system, or doing some open-ended debugging and diagnosis, it will likely end up there. Let's investigate!

Spawn [[#6.1 Investigate the Windows Operating System Internals]]. ^spawn-invst-eadf79

2025-08-12 Wk 33 Tue - 13:48

We've learned about the windows operating system. We can finally tackle fixing Paint.exe!

But we got an idea! Based on what we've learned, wouldn't it be cool to open source our Paint.exe research? This might not be a well-formed idea yet, but it might be worth exploring sometimes

Spawn an idea!

Spawn [[#7.1 Contribute Paint.exe research to open source]] ^spawn-idea-2caf16

2025-08-12 Wk 33 Tue - 13:54

This should cover most things. Let's just keep going back through our [[#^spawn-issue-252c2b|spawners]].

# 5 HowTos

## 5.1 Open the Windows Registery to fix Paint.exe!

- [ ] 

From [[#^spawn-howto-9f5c66]].

2025-08-12 Wk 33 Tue - 13:24

Maybe link some external resources, maybe find our the official windows documentation and do the exercise of translating technical specifications to specific needs here. 

This is a HowTo! It's where knowledge checks happen, it's where documentation is referenced often, the more general it is (such as translating official documentation to your specific context) the more useful it is. Because that methodology will be documented here, and likely can be referenced to solve many similar queries in the future.

You can also reference other sources that give you the answers you need here, like stackoverflow, etc.

Now this is your first encounter with non-linearity! We have just solved this HowTo, and yet, our spawning context, the issue, remains unresolved. We need to go back!

If you're using Obsidian with my settings here, you can put your editor cursor in those links and do `Ctrl + . ` to enter them quickly.

Let's return to [[#^spawn-howto-9f5c66|our spawner]]!


# 6 Investigations

## 6.1 Investigate the Windows Operating System Internals

From [[#^spawn-invst-eadf79]].

2025-08-12 Wk 33 Tue - 13:34

You made it! Not sure if through the spawner or by coming linearly through the file from the HowTos section...

So this is where we write about our concepts. Investigations are more open-ended and they may even involve some operative actions. For example...

2025-08-12 Wk 33 Tue - 13:38

Spawn [[#3.2 Setup a fresh installation of Windows on a VM]] ^spawn-task-603822

2025-08-12 Wk 33 Tue - 13:45

Once we're satisfied with our investigation, there may be resources here that other records can reference.

We add a code for the reference based on the heading code of this record (6.1), and a simple counter. So now we can refer to [google](https://www.google.com/) [[#^6-1-1]]! Here and everywhere.

Back to the [[#^spawn-invst-eadf79|Spawner]]!
### 6.1.1 References
1. [google](https://www.google.com/) ^6-1-1

# 7 Ideas

## 7.1 Contribute Paint.exe research to open source

From [[#^spawn-idea-2caf16]].

Just an idea! People need to know how Paint.exe works!

You can look below a little and then go back to the [[#^spawn-idea-2caf16|spawner]]!

## 7.2 Make more tutorials like this

2025-08-12 Wk 33 Tue - 13:52

Not every record we create need a spawner. Ideas for example can come from anywhere! Same for other sections when a spawning context is not present or clear.

# 8 Side Notes

## 8.1 Some data I gathered

Misc records here that are sort of related or side-channel to this note's context.

# 9 External Links

If you've linked this note anywhere in the world, maybe take note of that here!

# 10 References