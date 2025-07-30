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

## 2.2 Reverse engineering the internal mini language

- [ ] 

2025-07-30 Wk 31 Wed - 07:42

So it seems we have a table of functions, and a long string of data being processed by  these functions.

```ts
  while (g_exit_code == 0) {
    g_chr = get_and_adv_tape();

    var cmd_idx = Math.floor(g_chr / 2);
    console.log(`calling cmd @ idx: ${cmd_idx} from tape byte ${g_data_cur}: ${g_chr}`);

    var fn = g_unk_cmds[cmd_idx];
    if (fn) fn();
  }
```

### 2.2.1 Pend 1

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

Now we're going to modify `adventure.html` to point to our new js script that is `build/mountdrag.js`. We will maintain `mountaindrag.js` as the original copy.

We will also upgrade to `mountdrag.ts` because we can. Adding a `build.sh` that uses similar logic to this [entry](https://github.com/LanHikari22/lan-setup-notes/blob/3fbc4bad0a8e4f49739119f1acd88bf23f039bfb/lan/topics/tooling/web/entries/latest/000%20Making%20Greasemonkey%20scripts.md#16-creating-command-code-that-runs-every-t-ms).

```sh
#!/bin/bash

script_dir=$(dirname "$(readlink -f "$0")")

build() {
    basename="$1"

    npx esbuild "$basename.ts" --bundle --format=iife --outfile=build/$basename.js
}

pushd $script_dir

build mountdrag

popd
```

Oops there are many errors in the ts file if copied as is. See this [[#3.2 Investigate fixing typescript errors from copies mountdrag.js|task]].

## 3.2 Investigate fixing typescript errors from copies mountdrag.js

- [x] 

2025-07-30 Wk 31 Wed - 05:07

```js
g_mq.innerHTML += (g_inp >> 2) & (1 == 1) ? "F" : " ";
							     ~~~~~~~~

// error
The right-hand side of an arithmetic operation must be of type 'any', 'number', 'bigint' or an enum type.ts(2363)
```

Seems like type shorthand to get boolean?

```js
g_mq.innerHTML += ((g_inp >> 2) & 1) != 0 ? "F" : " ";
```

2025-07-30 Wk 31 Wed - 06:22

Moving parenthesis is not enough. values can be non-0 due to remaining flags.

2025-07-30 Wk 31 Wed - 06:29

```javascript
Function`$${"\x61 \x3D\x20\x6E\x65\x77 \x44\x61\x74\x65\x28\x29\x5B\x27\x67\x65\x74\x53\x65\x63\x6F\x6E\x64\x73\x27\x5D\x28\x29 "}$`();

// error
Argument of type 'TemplateStringsArray' is not assignable to parameter of type 'string'.ts(2345)
```

2025-07-30 Wk 31 Wed - 07:40

We can just replace it with the actual code we found in [[#5.2 Turn hex bytes into string in terminal]].

```ts
g_a = new Date()['getSeconds']()
```

## 3.3 Build a frequency map of the commands being run and tape locations being hit on idle

- [ ] 

2025-07-30 Wk 31 Wed - 08:34

2025-07-30 Wk 31 Wed - 08:42

Experiment results in `experiments/cmd_idx_idle.csv` (n=42) for

```ts
  while (g_exit_code == 0) {
    g_chr = get_and_adv_tape();

    var cmd_idx = Math.floor(g_chr / 2);
    console.log(`${cmd_idx},${g_data_cur},${g_chr}`);

    var fn = g_unk_cmds[cmd_idx];
    if (fn) fn();
  }

```


![[Pasted image 20250730084258.png]]

### 3.3.1 Pend 1

## 3.4 Send web messages to terminal to collect experiment data

- [ ] Send a "Hello world" over port 4567 to be received in terminal from typescript.

In order to do [[#3.3 Build a frequency map of the commands being run and tape locations being hit on idle]], we need to be able to gather the data into a csv file. But right now our app can only write to the DOM or to console.log.

2025-07-30 Wk 31 Wed - 12:36

An option:
- [zeromq typescript library](https://www.npmjs.com/package/zeromq) [(on github.io)](https://zeromq.github.io/zeromq.js/).

Trying to run the pub/sub example there.

Looking into [[#4.2 Errors while attempting to import zeromq for non-node platform|zeromq import issue]].

2025-07-30 Wk 31 Wed - 13:12

Let's try another more basic option. ZeroMQ requires node...

Let's try the [WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket) example,

Start the server over port 3003:

```sh
socat - TCP4-LISTEN:3003
```

then run the client in the game

```js
// Create WebSocket connection.
const socket = new WebSocket("ws://localhost:3003");

// Connection opened
socket.addEventListener("open", (event) => {
  socket.send("Hello Server!");
});

// Listen for messages
socket.addEventListener("message", (event) => {
  console.log("Message from server ", event.data);
});
```

2025-07-30 Wk 31 Wed - 13:40

Looking into [[#4.3 Firefox cannot establish connection to server at localhost|firefox connection issue]].


### 3.4.1 Pend

# 4 Issues

## 4.1 TS Build not exposing functions for html

- [x] 

```sh
#!/bin/bash

script_dir=$(dirname "$(readlink -f "$0")")

build() {
    basename="$1"

    npx esbuild "$basename.ts" --bundle --format=iife --outfile=build/$basename.js
}

pushd $script_dir

build mountdrag

popd
```

`--bundle --format=iife` puts everything inside a block, so `mainloop` is not accessible to the html file.

`--bundle --format=cjs` generates extra CommonJS code that's unwanted (including module.exports), although it does expose the functions. This is node specific as the `--help` clarifies as well.

2025-07-30 Wk 31 Wed - 08:15

We could just remove the invocations.

```js
(() => {
[...]
 })();
 
```

Remove those lines for `--format=iife`. 

```sh
cat build/$basename.js | python3 -c "import sys; lines = sys.stdin.read().split('\n'); print('\n'.join(lines[1:-2]))" > build/tmp
cat build/tmp > build/$basename.js
rm build/tmp
```

Not a pretty workaround with the tmp file, but we can't cat and then file write simultaneously here.

## 4.2 Errors while attempting to import zeromq for non-node platform

- [x] Reject

2025-07-30 Wk 31 Wed - 13:00

Ran into this issue while trying to build the pub/sub example for [zeromq.js](https://zeromq.github.io/zeromq.js/).


```sh
npm install zeromq
```


```ts
✘ [ERROR] Could not resolve "fs"

    node_modules/cmake-ts/build/loader.js:1:116:
      1 │ ...tringTag,{value:"Module"});const g=require("module"),u=require("fs"),h=require("path");var l=typeof document<"u"?document.currentS...
        ╵                                                                   ~~~~

  The package "fs" wasn't found on the file system but is built into node. Are you trying to bundle
  for node? You can use "--platform=node" to do that, which will remove this error.
```

These are the available platforms

```sh
npx esbuild --help | less

# out (relevant) 
  --platform=...        Platform target (browser | node | neutral,
                        default browser)
```

Our script runs directly on the browser for this game.

This seems node only, so let's look at another option.

### 4.2.1 Reject

## 4.3 Firefox cannot establish connection to server at localhost

- [x] 

2025-07-30 Wk 31 Wed - 13:44

```
Firefox can’t establish a connection to the server at ws://localhost:3003/.
```

This happens when trying to start a client with [websockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket),

```ts
export function start_client() {
  // Create WebSocket connection.
  const socket = new WebSocket("ws://localhost:3003");

  // Connection opened
  socket.addEventListener("open", (event) => {
    socket.send("Hello Server!");
  });

  // Listen for messages
  socket.addEventListener("message", (event) => {
    console.log("Message from server ", event.data);
  });
}
```

2025-07-30 Wk 31 Wed - 13:50

This [stackoverflow answer](https://stackoverflow.com/a/60003844/6944447) suggests setting a flag in `about:config`:

```
network.dns.native-is-localhost
```

It's by default set to false.

Resetting back, didn't resolve the problem.

Oh actually it seems we can connect. I didn't realize my server was dying:

```sh
socat - TCP4-LISTEN:3003
```

Could this server itself be the issue?

2025-07-30 Wk 31 Wed - 14:03

Let's try [gh websocat](https://github.com/vi/websocat) instead to serve,

```sh
cargo install websocat
```

Serve on 3003:

```sh
websocat -s 3003
```

I works!

I noticed that with websocket the client was sending GET requests, so our basic socat server was not enough.

This should also help with this [greasemonkey task](https://github.com/LanHikari22/lan-setup-notes/blob/main/lan/topics/tooling/web/entries/2025/000%20Making%20Greasemonkey%20scripts.md#17-creating-command-that-can-be-summoned-via-console-to-retrieve-information) to create a robust command line control outside the browser and to retrieve information.

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

## 5.2 Turn hex bytes into string in terminal

- [x] 

We have the following bytes:

```
\x61 \x3D\x20\x6E\x65\x77 \x44\x61\x74\x65\x28\x29\x5B\x27\x67\x65\x74\x53\x65\x63\x6F\x6E\x64\x73\x27\x5D\x28\x29
```

from

```ts
Function`$${"\x61 \x3D\x20\x6E\x65\x77 \x44\x61\x74\x65\x28\x29\x5B\x27\x67\x65\x74\x53\x65\x63\x6F\x6E\x64\x73\x27\x5D\x28\x29 "}$`();
```

It seems to break into three different segments

```
\x61

\x3D\x20\x6E\x65\x77 

\x44\x61\x74\x65\x28\x29\x5B\x27\x67\x65\x74\x53\x65\x63\x6F\x6E\x64\x73\x27\x5D\x28\x29
```

2025-07-30 Wk 31 Wed - 06:58

From [[003 NUMEROLOGY]],

```sh
echo "expand 32-byte k" | xxd -p | fold -w4 | xargs
echo "6578 7061 6e64 2033 322d 6279 7465 206b 0a" | tr -d ' ' | xxd -r -p

# out
6578 7061 6e64 2033 322d 6279 7465 206b 0a
expand 32-byte k
```

But with `\xNN`, we can echo them directly:

```sh
echo "\x61 \x3D\x20\x6E\x65\x77 \x44\x61\x74\x65\x28\x29\x5B\x27\x67\x65\x74\x53\x65\x63\x6F\x6E\x64\x73\x27\x5D\x28\x29"

# out
a = new Date()['getSeconds']()
```

They just sometimes decided a space " " and sometimes a "\x20"...


## 5.3 Start basic tcp server/client over terminal for testing

- [x] 

From [this superuser answer](https://superuser.com/a/392518/2972491) which also refers to [socat examples](http://www.dest-unreach.org/socat/doc/socat.html#EXAMPLES),

```sh
sudo apt-get install socat
```

```sh
# start a TCP server and read/write data on port 3003
socat - TCP4-LISTEN:3003

# start a TCP client connection and read/write data on port 3003
nc localhost 3003
```


# 6 Investigations
## 6.1 Searching for js reference syntax for bytes call

- [ ] 

Specifically for this

```ts
Function`$${"\x61 \x3D\x20\x6E\x65\x77 \x44\x61\x74\x65\x28\x29\x5B\x27\x67\x65\x74\x53\x65\x63\x6F\x6E\x64\x73\x27\x5D\x28\x29 "}$`();
```

[mozilla js docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference). [js manuals and info](https://javascript.info/manuals-specifications). [ECMA-262 standard](https://ecma-international.org/publications-and-standards/standards/ecma-262/). [ECMA-262 standard web](https://262.ecma-international.org/16.0/index.html)


Seems we're interested in the [template-literal syntax](https://262.ecma-international.org/16.0/index.html#sec-template-literal-lexical-components) from [ECMA-262](https://262.ecma-international.org/16.0/index.html#sec-template-literal-lexical-components).

This [ECMA-262 section](https://262.ecma-international.org/16.0/index.html#sec-getsubstitution) talks about the  case of `$$` so it's likely treated special. 

### 6.1.1 Skipped

# 7 Tooling



# 8 Side Notes

## 8.1 vscode run extension

2025-07-30 Wk 31 Wed - 08:12

[extension github](https://github.com/HubbleCommand/run).

Not sure why this isn't by default that we can run command from the vscode explorer.

I kept going to the terminal just to rund `build.sh`.


# 9 References
1. [the game](https://2tie.rustedlogic.net/games/adv/adventure.html) ^1

2. [lan-exp-scripts files](https://github.com/LanHikari22/lan-exp-scripts/tree/main/files/2025/persistent/000-mountain-n-dragon-ctf) ^2

> Supplementary content and source files for this challenge.

