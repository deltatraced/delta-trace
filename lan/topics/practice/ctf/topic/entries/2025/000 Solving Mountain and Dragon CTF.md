---
status: todo
---
#external 

# 1 What is this?

It can be found [here](https://2tie.rustedlogic.net/games/adv/adventure.html) [[#^1]].

# 2 Journal

## 2.1 Starting out

2025-07-30 Wk 31 Wed - 04:06

![[Pasted image 20250730040151.png]]

Outer HTML of that suspicious span is

```html
<span id="marquee" style="background-color:black;color:white;">        </span>
```

The HTML of that page itself isn't much...

```html
<html> <head><title>game</title></head> <body onload="mainloop()"> </br></br></br></br></br></br> <center><pre><span ID="marquee" style="background-color:black;color:white;"> </span></pre></center> <script src="[mountdrag.js](view-source:https://2tie.rustedlogic.net/games/adv/mountdrag.js)"></script></br></br> <center> Welcome, adventurers, to Mountain & Dragon!</br> This is a simple adventure game where the goal is to slay the dragon in its roost at the top of the mountains.</br> (to save your village or gain riches or blah blah)</br></br> The controls are simple:</br> arrow keys to move in a direction (Forward, Backward, Left or Right)</br> z key to Interact with the environment (such as throwing a lever or picking up an item)</br> x key to Use your currently held item</br></br> In case of death, refresh the page to restart your adventure! </center> </body> </html>
```

Oh that black spot responds to input in the order `L R F B I U`  corresponding to inputs `<- -> ^ v z x`.

After some inputs, it no longer responds, indicating that I died?

The files for this game, including ones to be modified, are to be references in  [lan-exp-scripts files](https://github.com/LanHikari22/lan-exp-scripts/tree/main/files/2025/persistent/000-mountain-n-dragon-ctf) [[#^2]].

# 3 Tasks

## 3.1 Run the game local with ability to modify the underlying script

- [x] 

2025-07-30 Wk 31 Wed - 04:26

The html and javascript used here seems self-contained. We can enhance our tools if we can modify the source code to play around with it and annotate it as we go.

To test that modification to the script is recognized, we will temporarily change the black spot input responses to all Ts:

```js
  mq.innerHTML = " ";
  mq.innerHTML += (inp >> 0) & (1 == 1) ? "T" : " ";
  mq.innerHTML += (inp >> 1) & (1 == 1) ? "T" : " ";
  mq.innerHTML += (inp >> 2) & (1 == 1) ? "T" : " ";
  mq.innerHTML += (inp >> 3) & (1 == 1) ? "T" : " ";
  mq.innerHTML += (inp >> 4) & (1 == 1) ? "T" : " ";
  mq.innerHTML += (inp >> 5) & (1 == 1) ? "T" : " ";
  mq.innerHTML += " ";
```

2025-07-30 Wk 31 Wed - 04:33

This works. Our javascript responses are recognized. Reverting back to

```js
  mq.innerHTML = " ";
  mq.innerHTML += (inp >> 0) & (1 == 1) ? "L" : " ";
  mq.innerHTML += (inp >> 1) & (1 == 1) ? "R" : " ";
  mq.innerHTML += (inp >> 2) & (1 == 1) ? "F" : " ";
  mq.innerHTML += (inp >> 3) & (1 == 1) ? "B" : " ";
  mq.innerHTML += (inp >> 4) & (1 == 1) ? "I" : " ";
  mq.innerHTML += (inp >> 5) & (1 == 1) ? "U" : " ";
  mq.innerHTML += " ";
```

Now we're going to modify `adventure.html` to point to our new js script that is `mountdrag.re.js`. We will maintain `mountaindrag.js` as the original copy.

We will also upgrade to `mountdrag.re.ts` because we can. Adding a `build.sh` that uses similar logic to this [entry](https://github.com/LanHikari22/lan-setup-notes/blob/3fbc4bad0a8e4f49739119f1acd88bf23f039bfb/lan/topics/tooling/web/entries/latest/000%20Making%20Greasemonkey%20scripts.md#16-creating-command-code-that-runs-every-t-ms).

```sh
#!/bin/bash

SCRIPT_DIR=$(dirname "$(readlink -f "$0")")

build() {
    basename="$1"

    npx esbuild "$basename.ts" --bundle --format=iife --outfile=build/$basename.js
}

pushd $SCRIPT_DIR

build mountdrag.re

popd
```

Oops there are many errors in the ts file if copied as is. See this [[#3.2 Investigate fixing typescript errors from copies mountdrag.js|task]].

## 3.2 Investigate fixing typescript errors from copies mountdrag.js

- [ ] 

2025-07-30 Wk 31 Wed - 05:07

```js
g_mq.innerHTML += (g_inp >> 2) & (1 == 1) ? "F" : " ";
							     ~~~~~~~~

// error
The right-hand side of an arithmetic operation must be of type 'any', 'number', 'bigint' or an enum type.ts(2363)
```

Seems like type shorthand to get boolean?

We can flip where the parenthesis go

```js
g_mq.innerHTML += ((g_inp >> 2) & 1) == 1 ? "F" : " ";
```

### 3.2.1 Pend

# 4 Issues

# 5 HowTos

## 5.1 Raising an error in typescript on undefined possibility

- [x] 

2025-07-30 Wk 31 Wed - 05:05

Trying to go from javascript to typescript I'm encountering some unhandled undefined errors, like

```typescript
'g_mq' is possibly 'null'.ts(18047)
```

We do not want to alter this functionality beyond the necessary, so let's throw an error on those sorts of possibilities.

As in this [stackoverflow answer](https://stackoverflow.com/a/38633821/6944447),

We can throw a `TypeError`:

```typescript
throw new TypeError('Error message');
```

# 6 References
1. [the game](https://2tie.rustedlogic.net/games/adv/adventure.html) ^1

2. [lan-exp-scripts files](https://github.com/LanHikari22/lan-exp-scripts/tree/main/files/2025/persistent/000-mountain-n-dragon-ctf) ^2

> Supplementary content and source files for this challenge.

