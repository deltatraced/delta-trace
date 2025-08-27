# 1 Objective

Here we state the objective of the note in a concise matter. This may include how it relates to other notes and problems, and may have tasks for objectives we need to get done like

- [ ] File a bug report

## 1.1 Key information

top headings `Tasks`, `Issues`, `HowTos`, `Investigations`, `Ideas`, or `Side Notes` are categories of entries, where each level-2 heading is an entry in them.

Any entry may spawn/create any other entry, and this is labeled two-way.

| Heading        | Meaning                                                                                                                                                                                                                                                                                                                                                                                     |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Objective      | Scope and purpose of the entire note file                                                                                                                                                                                                                                                                                                                                                   |
| Journal        | Mostly sequential logs of progress towards our objective                                                                                                                                                                                                                                                                                                                                    |
| Tasks          | Entries with a clear scope and measure of completion. Think "do X". They're operative.                                                                                                                                                                                                                                                                                                      |
| Issues         | Encountered problems to be resolved. Each problem has an entry with a short description of the problem. Think "Program crashes with error 5".                                                                                                                                                                                                                                               |
| HowTos         | Knowledge checks. Often entries about using a tool or performing an action. If we need to research online, we often create a howto entry to be clear we did a context switch to searching online. Names are descriptive of the actions we want to learn to perform. For example, "read a file in python3" which could be read as "[how to] read a file in python3".                         |
| Investigations | Learning, diagnostics, debugging, questions, reducing noise and increasing signal. Those can be theoretical in nature and we may come in not knowing much to estimate scope for them like tasks. Entries here may have names in the form of questions "What are the major components of a game engine?", or in the form of an investigation objective "Look into totem video player crash". |
| Ideas          | Might not be immediately operative, but any related ideas to this note context can be captured here.                                                                                                                                                                                                                                                                                        |
| Side Notes     | I learned about something cool! I don't want to dilute the context of other entries so I capture them here as entries.                                                                                                                                                                                                                                                                      |
| External Links | Capture any external resources that refer to this note file                                                                                                                                                                                                                                                                                                                                 |
| References     | Shared references for everything in this document. Can also<br> additional include resources.                                                                                                                                                                                                                                                                                               |

Order of the headings is by objectivity in scope. 
1. Objective is most clear in scope. 
2. Journal is clear progress logged towards the objective. 
3. Tasks have a clear signal to completion, 
4. Issues can but may not have a clear signal to resolution
5. HowTos can but may not have a clear way to perform them.
6. Investigations could be open-ended in scope 
7. Ideas are very open-ended
8. Side Notes is for anything outside the scopes.

This should be all you need. If you want, you can go through a short tutorial below following from Journal.

# 2 Journal

This is a sequential section where we log progress on the task or entry as we move along. It's meant to be linearly read. Other sections below, on the other hand, are record-based. For example we may spawn a task, issue, or investigation from here. The order with which they are spawned will hint at some linearity or progress, but subsequent tasks can also reference prior ones so the ordering is not as strict. 

If you are viewing this on github, note since this has a lot of headings, you can view the headings on github via `Outline`

![[Pasted image 20250812150047.png]]

To get something like this that you can jump through!

![[Pasted image 20250812150118.png]]


Let's spawn a task. Let's write some special keyword, like SPW0. This allows this line to be easily identified so that we can create a back reference from the spawned task back here. 

Now when we add the new task under the Tasks section, we can just write `[ [ ^SPW0` and select the line and Obsidian generates a random record for it quickly. We can follow that link back with `Ctrl + .` and replace `SPW0` with  `Spawn {internal link}.`

This lets Obsidian generate a link for the line quickly. Then we change `^{xxxxxx}` to `^spawn-task-{xxxxxx}` to make the spawn category clear.

Let's go to the new task!

Spawn [[#3.1 File a bug report]] ^spawn-task-c33094

2025-08-12 Wk 33 Tue - 13:55

I wouldn't normally do the "Go back to Spawner" thing when taking actual notes, but It's a cool thing to insert linearity into our tutorial which uses a non-linear note taking style. 

We've now covered what many sections do!

The template for these headings can be found here:

[[Note Headings]]

Although there are some manual things right now, like spawning new records, or referencing items from References, they can be done quickly. They could be automated in an obsidian plugin in the future!

Thank you for going through my non-linear note taking style tutorial!

# 3 Tasks

## 3.1 File a bug report

- [x] Here we specify some objectives that has to be completed
- [x] Organized my documents
- [ ] Find where to file the report to

From [[#^spawn-task-c33094]] in [[#2 Journal]]

2025-08-12 Wk 33 Tue - 13:14

We reference both the spawn block identifier `^spawn-task-c33094` and also the spawning heading, which in this case is Journal.

We can now  journal for the limited context of this task here, or we can summon other tasks, reference other tasks, add subheadings, etc.

Notice I added a `### Status` below. This shows the status for the Task in the outline quickly so I know what is pending. Typically if the task is finished, I put no such heading status for brevity. 

This is in the case I want to signify the task is pending:

![[Pasted image 20250812131705.png]]

2025-08-12 Wk 33 Tue - 13:17

Notice the timestamps I am adding. This could be done in obsidian with `Ctrl+P QuickAdd: Run QuickAdd -> insert-datetime`. I usually do this very quickly. The keys are `Ctrl+P Q <Enter> <Enter>`.

I take notes in a logging style where I come and log the next thought after some time, or I use timestamps to signify context change. For big context change, I use subheadings.

The timestamp allows me to signify context change, correlate with other time information in deliverables, or track how I switch contexts.

Now let's spawn an issue next to move on with the tutorial! 

Spawn [[#4.1 Paint.exe crashed with error code 0xDEADFEED]] ^spawn-issue-252c2b

2025-08-12 Wk 33 Tue - 13:55

And again! [[#^spawn-task-c33094|Spawner]]

### 3.1.1 Status

## 3.2 Setup a fresh installation of Windows on a VM

from [[#^spawn-task-603822]] in [[#6.1 Investigate the Windows Operating System Internals]]

2025-08-12 Wk 33 Tue - 13:45

We're back in the Tasks section from the Investigation section! Because this is a clearly operative thing to get done!

Now to simulate some work, let's go through a mini tutorial!

### 3.2.1 Time logging mini tutorial

You can learn more about setting this up in [obsidian: 000 Setting up time logging in Obsidian](https://github.com/LanHikari22/lan-setup-notes/blob/webview/lan/topics/tooling/obsidian/entries/2025/000%20Setting%20up%20time%20logging%20in%20Obsidian.md) from my other vault [lan-setup-notes](https://github.com/LanHikari22/lan-setup-notes/tree/webview).

We can also time log against any heading in Obsidian using my time logging scripts.

Just put your cursor at the heading, like this

![[Pasted image 20250812145428.png]]

and then do `Alt+k Alt+e`

Given that you've configured this plugin with your user, you see

![[Pasted image 20250812144507.png]]

Write "Sta"  and select "{user} Start Log"

![[Pasted image 20250812144536.png]]

Now let's do Open Timeline Log to check out what happened!

![[Pasted image 20250812144657.png]]

Notice you can click the heading you started this with or even hover over it to see its content:

![[Pasted image 20250812144747.png]]


When we're done, we can do `Alt+k Alt+e Stop` or just stop from the logger UI directly.

![[Pasted image 20250812144855.png]]

We can do `Alt+k Alt+e Summarize Time Logs` and this will create a summary by week and all time over in the same director as the timeline.


![[Pasted image 20250812145037.png]]

This explained how we can get timing information for our notes, and specifically where! 

If you're curious about the WkNN, those are week numbers according to the [ISO week date](https://www.epochconverter.com/weeks/2025).

OK Let's go back to our [[#^spawn-task-603822|spawner]]

# 4 Issues

## 4.1 Paint.exe crashed with error code 0xDEADFEED

- [x] Resolved, turns out I made this error up for demonstration purposes!

From [[#^spawn-issue-252c2b]] in [[#3.1 File a bug report]]

2025-08-12 Wk 33 Tue - 13:20

So tasks usually have names that are dos. "Wash your laundry!" "Implement keyboard debouncing", etc...

Issues are problems that occur, and names should be more descriptions of the issue encountered. The spawning context can inform us of how we ran into this issue. We can also write in the journal body of the issue about the context if needed.

This will ensure that issues can be searched by the context that describes them and will allow us to build up a database of issues encountered over time if we ever run into the same issue twice or someone else runs into our issues.

Issues may be marked resolved or unresolved, they may be skipped if we believe we need to go a different direction.

So you're trying to resolve this issue, but you need to figure out how to open the windows registry... Wait how do we do that again?

This is what a HowTo is for! 

Spawn [[#5.1 Open the Windows Registery to fix Paint.exe!]] ^spawn-howto-9f5c66

2025-08-12 Wk 33 Tue - 13:29

You're back! You just got exposed to non-linear note flow! Hopefully you did follow the spawn instead of just following through here?

Anyway we discover now that in order to resolve this issue, it's not enough to figure out registry stuff... We actually need to learn about the internals of the Windows operating system! 

What an open-ended conceptual task! Tasks should preferably be operative and have a strong sense of what it means to complete them. For the more open-ended conceptual stuff, we got the Investigations section!

If I'm exploring a concept, a system, or doing some open-ended debugging and diagnosis, it will likely end up there. Let's investigate!

Spawn [[#6.1 Investigate the Windows Operating System Internals]] ^spawn-invst-eadf79

2025-08-12 Wk 33 Tue - 13:48

We've learned about the windows operating system. We can finally tackle fixing Paint.exe!

But we got an idea! Based on what we've learned, wouldn't it be cool to open source our Paint.exe research? This might not be a well-formed idea yet, but it might be worth exploring sometimes

Spawn an idea!

Spawn [[#7.1 Contribute Paint.exe research to open source]] ^spawn-idea-2caf16

2025-08-12 Wk 33 Tue - 13:54

This should cover most things. Let's just keep going back through our [[#^spawn-issue-252c2b|spawners]].

# 5 HowTos

## 5.1 Open the Windows Registery to fix Paint.exe!

- [x] 

From [[#^spawn-howto-9f5c66]] in [[#4.1 Paint.exe crashed with error code 0xDEADFEED]]

2025-08-12 Wk 33 Tue - 13:24

Maybe link some external resources, maybe find our the official windows documentation and do the exercise of translating technical specifications to specific needs here. 

This is a HowTo! It's where knowledge checks happen, it's where documentation is referenced often, the more general it is (such as translating official documentation to your specific context) the more useful it is. Because that methodology will be documented here, and likely can be referenced to solve many similar queries in the future.

You can also reference other sources that give you the answers you need here, like stackoverflow, etc.

Now this is your first encounter with non-linearity! We have just solved this HowTo, and yet, our spawning context, the issue, remains unresolved. We need to go back!

If you're using Obsidian with my settings here, you can put your editor cursor in those links and do `Ctrl + . ` to enter them quickly.

Let's return to [[#^spawn-howto-9f5c66|our spawner]]!


# 6 Investigations

## 6.1 Investigate the Windows Operating System Internals

From [[#^spawn-invst-eadf79]] in [[#4.1 Paint.exe crashed with error code 0xDEADFEED]]

2025-08-12 Wk 33 Tue - 13:34

You made it! Not sure if through the spawner or by coming linearly through the file from the HowTos section...

So this is where we write about our concepts, research, open-ended debugging sessions...

Investigations are more open-ended and they may even involve some operative actions. In that case, we can spawn some tasks like...

2025-08-12 Wk 33 Tue - 13:38

Spawn [[#3.2 Setup a fresh installation of Windows on a VM]] ^spawn-task-603822

2025-08-12 Wk 33 Tue - 13:45

Once we're satisfied with our investigation, there may be resources here that other records can reference.

We add a code for the reference based on the heading code of this record (6.1), and a simple counter. So now we can refer to [duckduckgo](https://duckduckgo.com/) [[#^6-1-1]]! The first link is the external link, the second is internal to where it is in the references for indexing purposes.

Back to the [[#^spawn-invst-eadf79|Spawner]]!
### 6.1.1 References
1. [duckduckgo](https://duckduckgo.com/) ^6-1-1

# 7 Ideas

## 7.1 Contribute Paint.exe research to open source

From [[#^spawn-idea-2caf16]] in [[#4.1 Paint.exe crashed with error code 0xDEADFEED]]

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