---
context_type: task
status: todo
---

Parent: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/000-clusterline-md-getting-started]]

Spawned by: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/005 Add a custom fuzzy selector window with some text]]

Spawned in: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/005 Add a custom fuzzy selector window with some text#^spawn-task-3c7bf1|^spawn-task-3c7bf1]]

# Journal

2026-07-07 Wk 28 Tue - 21:04 +03:00

We implemented confirmed selection and now post an `on_selected` message besides `on_canceled`.

There are some events that are not triggering:

- `keyDown` and `keyUp` for `sb_input1`.
- `mouseMove` for one of the option items.

https://www.w3schools.com/tags/ref_eventattributes.asp

2026-07-10 Wk 28 Fri - 09:59 +03:00

Just had to change them to `keydown`, `keyup`, and `mousemove`.

2026-07-10 Wk 28 Fri - 12:32 +03:00

Okay, right now we set `keydown` to handle moving up and down. We're doing like in silverbullet, registered for the case when the input text is focused. We also have basic filtering functionality now. Keyword based (space separated list of words to include/disclude). If the keyword starts with `!` it will be excluded. This is how I've done this in fzf mostly and with setting it in vim. Although they have that be fuzzy, so I usually try to opt out of the fuzzying by using `'`. But here no need for `'`. And because it is keyboard based, order of the keywords also doesn't matter. Obsidian often is sensitive in its search for the text order for some reason.

Search includes the shared text of the name, hint, and description. It is also case insensitive.

Not currently using `keyup`.  Silverbullet (`client/components/filter.tsx > fn FilterList > fn onKeyUp`) seems to use it for some alt+space function.

Pressing `Enter` now also selects, so the user doesn't have to use the mouse. 

But we need to implement `mousemove` next for users who prefer to move the mouse and click.

`mousemove` set! Just have to select whichever item is currently selected!

--/ 2026-07-10 Wk 28 Fri - 20:25 +03:00

Need to also handle case where everything is filtered. Nothing should be selected. And if the user presses Enter, it shouldn't confirm anything.

--/

2026-07-10 Wk 28 Fri - 13:21 +03:00

The new interactivity works in silverbullet as well, but we're not receiving the confirm event in rust. We do receive the cancel event in rust.

There was a panic I did not see from rust because I was filtering by `CLSTR`. Change that filter to just `clusterline` so we can see panics too. (Although we pay for this with currently occasional minimal noise from the index plugin on the clusterline plugin in the repo)

```
[clusterline plug] [clusterline-rs] We do reach on_selected_json
[clusterline plug] panicked at src/widgets/sb_options_filter_list.rs:102:14: on_selected_json: Failed to parse JSON message: JsValue(SyntaxError: JSON.parse: expected ',' or '}' after property value in object at line 3 column 6 of the JSON data
```

Forgot to add comma to the JSON message.

2026-07-10 Wk 28 Fri - 21:16 +03:00

```
[clusterline plug] [clusterline-sb] Plug Error: post_message expects arguments topic, subtopic, json_msg
```

Refining the error under `src/clusterline.ts > fn ts_post_message`.

```
[clusterline plug] [clusterline-sb] Plug Error: post_message expects arguments topic, subtopic, json_msg. We got (comma_separated_args sb_options_filter_list,on_selected,{ "service": "greet", "option_name": "Rocks..." }).
```

Right. Comma separation broke because the json message itself has a comma now.