---
status: todo
---

\#external

# 1 What is this?

A CTF Game!

It can be found [here](https://2tie.rustedlogic.net/games/adv/adventure.html) [<a name="1" />^1](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#1).

# 2 Journal

## 2.1 Starting out

2025-07-30 Wk 31 Wed - 04:06

<img src="https://raw.githubusercontent.com/delta-domain-rnd/delta-trace/refs/heads/main/attachments/Pasted%20image%2020250730040151.png" />

Outer HTML of that suspicious span is

````html
<span id="marquee" style="background-color:black;color:white;">        </span>
````

The HTML of that page itself isn't much...

````html
<html> <head><title>game</title></head> <body onload="mainloop()"> </br></br></br></br></br></br> <center><pre><span ID="marquee" style="background-color:black;color:white;"> </span></pre></center> <script src="[mountdrag.js](view-source:https://2tie.rustedlogic.net/games/adv/mountdrag.js)"></script></br></br> <center> Welcome, adventurers, to Mountain & Dragon!</br> This is a simple adventure game where the goal is to slay the dragon in its roost at the top of the mountains.</br> (to save your village or gain riches or blah blah)</br></br> The controls are simple:</br> arrow keys to move in a direction (Forward, Backward, Left or Right)</br> z key to Interact with the environment (such as throwing a lever or picking up an item)</br> x key to Use your currently held item</br></br> In case of death, refresh the page to restart your adventure! </center> </body> </html>
````

Oh that black spot responds to input in the order `L R F B I U`  corresponding to inputs `<- -> ^ v z x`.

After some inputs, it no longer responds, indicating that I died?

The files for this game, including ones to be modified, are to be references in  [lan-exp-scripts files](https://github.com/LanHikari22/lan-exp-scripts/tree/main/files/2025/persistent/000-mountain-n-dragon-ctf) [<a name="2" />^2](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#2).

## 2.2 Reverse engineering the internal mini language

2025-07-30 Wk 31 Wed - 07:42

So it seems we have a table of functions, and a long string of data being processed by  these functions.

````ts
  while (g_exit_code == 0) {
    g_chr = get_and_adv_tape();

    var cmd_idx = Math.floor(g_chr / 2);
    console.log(`calling cmd @ idx: ${cmd_idx} from tape byte ${g_data_cur}: ${g_chr}`);

    var fn = g_unk_cmds[cmd_idx];
    if (fn) fn();
  }
````

### 2.2.1 Reconstructing tape content

2025-07-31 Wk 31 Thu - 00:31

Let's reconstruct the tape. The simple sum checksum for it is 287251 and it has 13229 elements.

We know this is the math of the initial tape command retrieval

````ts
  while (g_exit_code == 0) {
    g_inst8 = get_and_adv_tape();

    var cmd_idx = Math.floor(g_inst8 / 2);

    var fn = g_unk_cmds[cmd_idx];
    if (fn) fn();
  }
````

`g_data_cur` begins as 0.  The first `g_inst8` is also 0. Then `g_data_cur` advanced to 1.

So the very first command executed `cmd_idx`, should be 0.

````ts
// 0
function () {
  g_reg = get_and_adv_tape_u16_and_load_on_odd_caller();
},

function get_and_adv_tape_u16_and_load_on_odd_caller() {
  var inst16 = get_and_adv_tape_u16();
  if (g_inst8 % 2 > 0) inst16 = g_tape[inst16] || 0;
  return inst16;
}
````

We're an even caller (`g_inst8` is 0 and even). So we just read the next 2 bytes and get them.

That means instruction 0 will always read 3 bytes. 1 for itself and 2 for its parameters, and then it will store the param data in the global `g_reg` register.

2025-07-31 Wk 31 Thu - 01:06

The next `g_inst8` 3 bytes in reads `2`. With floor math, this should give us a `cmd_idx` of `1`.

That's another comand of size-3 (itself 1 + u16 input)

````ts
// 1
function () {
  // Write register data to the next u16 inst
  g_tape[get_and_adv_tape_u16()] = g_reg;
},
````

It basically just writes the previously read `g_reg` (0) to the location specified in the tape. In our case for `2, 175, 51` that should be

````sh
python3 -c "n0=175; n1=51; print(hex((n1 << 8) + n0))"

# out
0x33af
````

2025-07-31 Wk 31 Thu - 01:28

Order of operations matter.

````py
# good
python3 -c "n=0x33af; print((n & 0xFF00) >> 8)"
51

# bad
python3 -c "n=0x33af; print(n & 0xFF00 >> 8)"
````

There are 4 `cmd01` invocations here.

````ts
function reconstruct_tape(): number[] {
  var tape: number[] = []; 

  tape.push(...cmd00_write_param_to_reg(/*param16*/ 0x0000));
  tape.push(...cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33af));
  tape.push(...cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33b3));
  tape.push(...cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33b4));
  tape.push(...cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33b6));
  tape.push(...g_tape_remaining);

  return tape;
}
````

I will add them on whenever I encounter them.

Next, we read `g_inst8` as 8, yielding a `cmd_idx` of 4.

````ts
// 4
function () {
  g_reg += get_and_adv_tape_u16_and_load_on_odd_caller();
},
````

This is also a 3-byte command `8, 1, 0,` . Just summing its param value into `g_reg`.

2025-07-31 Wk 31 Thu - 01:42

The next new `g_inst8` command reads 46, inferring a `cmd_idx` of 23. But commands only go 0-22.

This also triggers at that point

````ts
var fn = g_cmds[cmd_idx];
if (fn) {
  fn();
} else {
  console.log(`${g_inst8},${cmd_idx}`);
  throw new Error("breakpoint");
}

// out
46,23
Uncaught Error: breakpoint
````

It seems to be a size-1 no-op.

2025-07-31 Wk 31 Thu - 01:54

I think the reason for the floor divide by 2 math

````ts
var cmd_idx = Math.floor(g_inst8 / 2);
````

is because of

````ts
function get_and_adv_tape_u16_and_load_on_odd_caller() {
  var inst16 = get_and_adv_tape_u16();
  if (g_inst8 % 2 > 0) inst16 = g_tape[inst16] || 0;
  return inst16;
}
````

This makes it so that every command can have an odd or even variant which changes how this function behaves. So it's a padded bit for this and the rest is the command index.

2025-07-31 Wk 31 Thu - 01:59

The next new `g_inst8` command reads 30, inferring a `cmd_idx` of 15.

````ts
// 15
function () {
  g_tape_addr_reg -= 1;
  g_tape[g_tape_addr_reg] = g_data_cur + 2;
  g_data_cur = get_and_adv_tape_u16_and_load_on_odd_caller();
},
````

Another 3-size command. Until now we have not touched `g_tape_addr_reg`, so it should be 0. So our first time we're accessing it at -1? Let's confirm.

````ts
// 15
function () {
  g_tape_addr_reg -= 1;
  var before = g_tape[g_tape_addr_reg];
  var data_cur_before = g_data_cur;
  g_tape[g_tape_addr_reg] = g_data_cur + 2;
  g_data_cur = get_and_adv_tape_u16_and_load_on_odd_caller();
  
  console.log(`g_tape_addr_reg: ${g_tape_addr_reg}`);
  console.log(`slot@before: ${before}`);
  console.log(`slot@after: ${g_tape[g_tape_addr_reg]}`);
  console.log(`data_cur@before: ${data_cur_before}`);
  console.log(`data_cur@after: ${g_data_cur}`);
  
  throw new Error("debugging")
},

// out
g_tape_addr_reg: -1
slot@before: undefined
slot@after: 32
data_cur@before: 30
data_cur@after: 2992

Uncaught Error: debugging
````

So there's some calling functionality here. It retains the previous program counter in a known location and updates the program counter according to the given u16 value.

The writing to -1 behavior is strange.

So we can treat `g_tape_addr_reg -= 1;` as some sort of pop?  Renaming `g_data_cur` to `g_pc16` since we have evidence here that u16 data is loaded into it directly.

Also renaming `g_tape_addr_reg` to `g_sp16` for a u16 stack pointer.

So from

````
g_tape[g_sp16] = g_pc16 + 2;
````

We know the tape address space is `u16`, and its element space is also `u16`.

When we do math with n-sized commands, we assumed that we are being fed `u8`s. But this may not always be true as we can see here. Yet the original tape data really only contains `u8` values.

cmd15 seems to be a 3-size stack-preserving caller.

2025-07-31 Wk 31 Thu - 02:39

The next new `g_inst8` command reads 12, inferring a `cmd_idx` of 6.

````ts
// 6
function () {
  g_cond_reg = g_reg == get_and_adv_tape_u16_and_load_on_odd_caller();
},
````

Just a condition check that the current register value is the next `u16` on the tape. And with this we should rewrite `g_reg` to `g_reg16` since it is a `u16` variable.

This is a 3-size command `12, 0, 0,`.

2025-07-31 Wk 31 Thu - 02:45

The next new `g_inst8` command reads 26, inferring a `cmd_idx` of 13.

````ts
// 13
function () {
  var v = get_and_adv_tape_u16_and_load_on_odd_caller();
  g_pc16 = g_cond_reg ? v : g_pc16;
},
````

<a name="cmd13" />^cmd13

It's a 3-size command `26, 138, 3`.

It's a branch if equal. Let's just call the command `beq`.

2025-07-31 Wk 31 Thu - 02:56

There's much repetition now. Let's write a python script to autoreconstruct the commands we've encountered so far until the first encountered new command for us to investigate.

See [task](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#35-write-python-script-to-reconstruct-tape-until-new-undocumented-command). <a name="spawn-reconstruct-tape-script" />^spawn-reconstruct-tape-script

2025-08-01 Wk 31 Fri - 02:58

Now that we autoreconstruct the tape, we can find the next commands quicker.

Following the same note convention as before for documenting commands,

The next new `g_inst8` command reads 24, inferring a `cmd_idx` of 12.

````ts
// 12
function () {
	g_pc16 = get_and_adv_tape_u16_and_load_on_odd_caller();
},
````

This is just a goto on the u16 param.

Adding

````ts
export function cmd12_goto(tape_addr16: number): number[] {
  return [24, tape_addr16 & 0xFF, (tape_addr16 & 0xFF00) >> 8];
}
````

to `reconstructed_commands.ts`

and

````py
    CommandData("cmd12", 24, "cmd12_goto(/*tape_addr16*/ {PARAM0})", [16]),
````

to `commands` in `data/tape.py`.

Now let's reconstruct the tape and ensure tape OK

````sh
python3 data/tape.py reconstruct-tape
./build.sh
````

In the web console when launching `adventure.html` in the browser we see

````
tape OK
````

2025-08-01 Wk 31 Fri - 03:08

The next new `g_inst8` command reads 34, inferring a `cmd_idx` of 17.

````ts
// 17
function () {
  g_sp16 -= 1;
  g_tape[g_sp16] = g_reg16;
},
````

This is similar to command 15. It's simpler, it just writes a value on the stack rather than the program counter. Let's just call it `cmd17_push_reg16_to_stack`.

2025-08-01 Wk 31 Fri - 03:20

Oops. I made it take a `param16` when really this one takes nothing, so then we got an invalid command `51` afterwards

2025-08-01 Wk 31 Fri - 03:35

The next new `g_inst8` command reads 1, inferring a `cmd_idx` of 0.

This is our first odd command. It's the same as 0, which we already have. This is a 3-size command of `1, 179, 51,`

Recall that command 0 is

````ts
// 0
function () {
  g_reg = get_and_adv_tape_u16_and_load_on_odd_caller();
},
````

The odd bit triggers something new about `get_and_adv_tape_u16_and_load_on_odd_caller`

````ts
function get_and_adv_tape() {
  var v = g_tape[g_pc16];
  g_pc16 += 1;
  return v;
}

function get_and_adv_tape_u16() {
  var v = get_and_adv_tape();
  return v + (get_and_adv_tape() << 8);
}

function get_and_adv_tape_u16_and_load_on_odd_caller() {
  var inst16 = get_and_adv_tape_u16();
  if (g_inst8 % 2 > 0) inst16 = g_tape[inst16] || 0;
  return inst16;
}
````

Basically instead of loading the instruction as-is, it loads it from the address provided.

Let's add an explicit command for this instead of relying on the odd bit, since it will perform a different behavior: `cmd00_write_loaded_param16_to_reg16`.

Also renaming `param` and `reg` to `param16` and `reg16` in the command names for consistency.

2025-08-01 Wk 31 Fri - 03:47

The next new `g_inst8` command reads 36, inferring a `cmd_idx` of 18.

````ts
// 18
function () {
  g_reg16 = g_tape[g_sp16];
  g_sp16 += 1;
},
````

This is a `cmd18_pop_stack_to_reg16`. It's a 1-size command.

If I make a mistake and not update 36->38 here for cmd18,

````py
    CommandData("cmd17", 34, "cmd17_push_reg16_to_stack()", []),
    CommandData("cmd18", 34, "cmd18_pop_stack_to_reg16()", []),
````

I will still get OK but we will not advance from 34 because in my [script](https://github.com/LanHikari22/lan-exp-scripts/blob/main/files/2025/persistent/000-mountain-n-dragon-ctf/data/tape.py) I ensure that every command must have a unique activation byte:

````py
    command = (
        commands
        | pipe.OfIter[CommandData].filter(
            lambda command: command.activation_byte == command_byte
        )
        | pipe.OfIter[CommandData].to_list()
        | pipe.Of[List[CommandData]].map(lambda lst: lst[0] if len(lst) == 1 else None)
    )

    if command is None:
        return (None, tape)
````

2025-08-01 Wk 31 Fri - 03:56

The next new `g_inst8` command reads 28, inferring a `cmd_idx` of 14.

````ts
// 14
function () {
  var v = get_and_adv_tape_u16_and_load_on_odd_caller();
  g_pc16 = g_cond_reg ? g_pc16 : v;
},
````

This is a 3-size command `28, 201, 0,`.

This is basically like command 13

[^cmd13](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#cmd13)

Except instead of `beq` it's `bne` for branch not equal.

2025-08-01 Wk 31 Fri - 05:17

The next new `g_inst8` command reads 9, inferring a `cmd_idx` of 4.

This is `cmd04_sum_reg16_loaded_param16_to_reg16`.

2025-08-01 Wk 31 Fri - 05:20

The next new `g_inst8` command reads 14, inferring a `cmd_idx` of 7.

````ts
// 7
function () {
  g_cond_reg = g_reg16 < get_and_adv_tape_u16_and_load_on_odd_caller();
},
````

This is a 3-size command. Let's call it `cmd07_check_reg16_lt_param16`.

2025-08-01 Wk 31 Fri - 05:23

The next new `g_inst8` command reads 10, inferring a `cmd_idx` of 5.

````ts
// 5
function () {
  g_reg16 -= get_and_adv_tape_u16_and_load_on_odd_caller();
},
````

This is a 3-size command. Call it `cmd05_sub_param16_from_reg16`

2025-08-01 Wk 31 Fri - 05:30

The next new `g_inst8` command reads 42, inferring a `cmd_idx` of 21.

````ts
// 21
function () {
  // Function`$${"\x61 \x3D\x20\x6E\x65\x77 \x44\x61\x74\x65\x28\x29\x5B\x27\x67\x65\x74\x53\x65\x63\x6F\x6E\x64\x73\x27\x5D\x28\x29 "}$`();
  // Hex for:
  // a = new Date()['getSeconds']()
  
  g_reg16 = new Date()['getSeconds']()
},
````

Right this one was obfuscated for some reason. I guess part of the game puzzle. It would hardcode the variable to be called `a`. So that could have broken things with us reverse engineering and renaming things in the typescript.

It's a 1-size command. Let's call it `cmd21_set_reg16_to_cur_seconds`

2025-08-01 Wk 31 Fri - 05:34

The next new `g_inst8` command reads 18, inferring a `cmd_idx` of 9.

````ts
// 9
function () {
  g_reg16 = g_reg16 & get_and_adv_tape_u16_and_load_on_odd_caller();
},
````

This is a 3-size command. Let's call it `cmd09_check_reg16_and_param16`

2025-08-01 Wk 31 Fri - 05:37

We've reached PC `0x0817`.

The next new `g_inst8` command reads 4, inferring a `cmd_idx` of 2.

````ts
// 2
function () {
  g_tape[get_and_adv_tape_u16()] = g_tape[g_reg16];
},
````

This is a 3-size command. Call it `cmd02_store_loaded_reg16_to_loaded_param16`.

2025-08-01 Wk 31 Fri - 05:41

We've reached PC `0x0a2a`.

The next new `g_inst8` command reads 38, inferring a `cmd_idx` of 19.

````ts
// 19
function () {
  g_exit_code = 1;
},
````

Here it is, the command that terminates the current mainloop and forces a UI update.

Let's rename it from `g_exit_code` to `g_new_frame`. It does not exit the actual game, it only forces a the while loop of `mainloop` to terminate and immediately updates inner HTML elements for the inputs and requests a new animation frame.

This is a 1-sized command. Call it `cmd19_issue_new_frame`.

2025-08-01 Wk 31 Fri - 05:48

We've reached PC `0x0a2b`. Just the very next command.

The next new `g_inst8` command reads 40, inferring a `cmd_idx` of 20.

````ts
// 20
function () {
  g_inp = 0;
  g_inp +=
    g_joyp[37] +
    (g_joyp[39] << 1) +
    (g_joyp[38] << 2) +
    (g_joyp[40] << 3) +
    (g_joyp[90] << 4) +
    (g_joyp[88] << 5);
  g_reg16 = g_inp;
},
````

Ooh! Joypad!

This is a 1-sized command. Let's call it  `cmd20_read_joypad_input_and_set_to_reg16`

2025-08-01 Wk 31 Fri - 05:52

We've reached PC `0x0a36`.

The next new `g_inst8` command reads 44, inferring a `cmd_idx` of 22.

````ts
// 22
function () {
  this.document.location.href = "" + l(g_reg16) + ".html";
},
````

Hmm. Will have to investigate why set the href. See [investigation](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#62-why-is-href-being-set-in-cmd22). <a name="spawn-cmd22-invst" />^spawn-cmd22-invst

This is a 1-sized command. Call it `cmd22_set_dom_href_to_reg16`.

2025-08-01 Wk 31 Fri - 06:03

We've reached PC `0x0a9d`.

The next new `g_inst8` command reads 7, inferring a `cmd_idx` of 3.

````ts
// 3
function () {
  g_tape[g_reg16] = get_and_adv_tape_u16_and_load_on_odd_caller();
},
````

It's the odd version of this. This is a 3-sized command. Call it `cmd03_set_loaded_reg16_to_loaded_param16`

2025-08-01 Wk 31 Fri - 06:07

We've reached PC `0x0aa9`.

The next new `g_inst8` command reads 13, inferring a `cmd_idx` of 6.

3-size command. Loaded version of cmd06. Call it `cmd06_check_reg16_is_loaded_param16`

2025-08-01 Wk 31 Fri - 06:11

We've reached PC `0x0ab8`.

The next new `g_inst8` command reads 6, inferring a `cmd_idx` of 3.

We've created the loaded version of this 3-sized command. This one is `cmd03_set_loaded_reg16_to_param16`.

2025-08-01 Wk 31 Fri - 06:15

We've reached PC `0x0abe`.

The next new `g_inst8` command reads 32, inferring a `cmd_idx` of 16.

````ts
// 16
function () {
  g_pc16 = g_tape[g_sp16];
  g_sp16 += 1;
},
````

Ooh! It's a return!

This is a 1-sized command. Let's call it `cmd16_return`!

2025-08-01 Wk 31 Fri - 06:20

We've reached PC `0x0b48`.

The next new `g_inst8` command reads 15, inferring a `cmd_idx` of 7.

This is a 3-sized command `cmd07_check_reg16_lt_loaded_param16`.

Renaming all `tape_addr16` to `addr16` for brevity.

2025-08-01 Wk 31 Fri - 06:24

We've reached PC `0x0b51`.

The next new `g_inst8` command reads 11, inferring a `cmd_idx` of 5.

There's many loaded versions of commands being used. Let's revert our design decision to name those "loaded" commands and just handle the loaded instruction flag automatically.

See [task](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#37-handle-loaded-param16-flag-automatically-in-commands-when-reconstructing-tape-program). <a name="spawn-task-handle-loaded-param16" />^spawn-task-handle-loaded-param16

2025-08-01 Wk 31 Fri - 07:06

We seem to end up at an invalid command 51. This is a common data byte... See [issue](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#44-tape-reaches-invalid-command-51-at-0x0b89). <a name="spawn-issue-0b89" />^spawn-issue-0b89

2025-08-01 Wk 31 Fri - 10:04

We added a bug command `cmd25_bug`... We know this would not lead to any critical failure for the game.

We've reached PC `0x0bb5`.

The next new `g_inst8` command reads 23, inferring a `cmd_idx` of 11.

````ts
// 11
function () {
  g_reg16 = g_reg16 ^ get_and_adv_tape_u16_and_load_on_odd_caller();
},
````

Call this `cmd11_check_reg16_xor_param16`.

2025-08-01 Wk 31 Fri - 10:13

We've reached PC `0x0bc6`.

The next new `g_inst8` command reads 16, inferring a `cmd_idx` of 8.

Let's just go for completeness here and cover any remaining commands we haven't yet mapped.

These would be only 8, 10...

Let's do 8 and see if we hit 10 in activation 20/21.

````ts
// 8
function () {
  g_cond_reg = 0 != (g_reg16 & get_and_adv_tape_u16_and_load_on_odd_caller());
},

// 9
function () {
  g_reg16 = g_reg16 & get_and_adv_tape_u16_and_load_on_odd_caller();
},

// 10
function () {
  g_reg16 = g_reg16 | get_and_adv_tape_u16_and_load_on_odd_caller();
},

````

We called command 9 `cmd09_check_reg16_and_param16` but this is false. it's storing mask results. Change that to `cmd09_mask_reg16_and_param16_to_reg16`

Call command 8 `cmd08_check_reg16_and_param16` and command 10 `cmd08_check_reg16_or_param16`

2025-08-01 Wk 31 Fri - 10:30

We've reached PC `0x0c98`.

We've encountered command 10. Let's add it too.

2025-08-01 Wk 31 Fri - 10:38

We've reached PC `0x1088`.

We've hit a `255`. There are many `255`s.

2025-08-01 Wk 31 Fri - 11:07

Here is a frequency analysis of the commands from data captured in `experiments/tape_program_data.csv`

<img src="https://raw.githubusercontent.com/delta-domain-rnd/delta-trace/refs/heads/main/attachments/Pasted%20image%2020250801110727.png" />

`cmd16_return` is only referenced 10 times. Do we assume we have 10 functions?

`cmd15_stack_preserve_call` is called 63 times. 63 function calls?

We need to document labels here for this program. Let's reconstruct the labels too.  See [task](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#38-reconstruct-labels-in-the-tape-program). <a name="spawn-task-reconstruct-labels" />^spawn-task-reconstruct-labels

### 2.2.2 Pend

# 3 Tasks

## 3.1 Run the game local with ability to modify the underlying script

* [x] 

2025-07-30 Wk 31 Wed - 04:26

The html and javascript used here seems self-contained. We can enhance our tools if we can modify the source code to play around with it and annotate it as we go.

To test that modification to the script is recognized, we will temporarily change the black spot input responses to all Ts:

````js
  mq.innerHTML = " ";
  mq.innerHTML += (inp >> 0) & (1 == 1) ? "T" : " ";
  mq.innerHTML += (inp >> 1) & (1 == 1) ? "T" : " ";
  mq.innerHTML += (inp >> 2) & (1 == 1) ? "T" : " ";
  mq.innerHTML += (inp >> 3) & (1 == 1) ? "T" : " ";
  mq.innerHTML += (inp >> 4) & (1 == 1) ? "T" : " ";
  mq.innerHTML += (inp >> 5) & (1 == 1) ? "T" : " ";
  mq.innerHTML += " ";
````

2025-07-30 Wk 31 Wed - 04:33

This works. Our javascript responses are recognized. Reverting back to

````js
  mq.innerHTML = " ";
  mq.innerHTML += (inp >> 0) & (1 == 1) ? "L" : " ";
  mq.innerHTML += (inp >> 1) & (1 == 1) ? "R" : " ";
  mq.innerHTML += (inp >> 2) & (1 == 1) ? "F" : " ";
  mq.innerHTML += (inp >> 3) & (1 == 1) ? "B" : " ";
  mq.innerHTML += (inp >> 4) & (1 == 1) ? "I" : " ";
  mq.innerHTML += (inp >> 5) & (1 == 1) ? "U" : " ";
  mq.innerHTML += " ";
````

Now we're going to modify `adventure.html` to point to our new js script that is `build/mountdrag.js`. We will maintain `mountaindrag.js` as the original copy.

We will also upgrade to `mountdrag.ts` because we can. Adding a `build.sh` that uses similar logic to this [entry](https://github.com/LanHikari22/lan-setup-notes/blob/3fbc4bad0a8e4f49739119f1acd88bf23f039bfb/lan/topics/tooling/web/entries/latest/000%20Making%20Greasemonkey%20scripts.md#16-creating-command-code-that-runs-every-t-ms).

````sh
#!/bin/bash

script_dir=$(dirname "$(readlink -f "$0")")

build() {
    basename="$1"

    npx esbuild "$basename.ts" --bundle --format=iife --outfile=build/$basename.js
}

pushd $script_dir

build mountdrag

popd
````

Oops there are many errors in the ts file if copied as is. See this [task](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#32-investigate-fixing-typescript-errors-from-copies-mountdragjs).

## 3.2 Investigate fixing typescript errors from copies mountdrag.js

* [x] 

2025-07-30 Wk 31 Wed - 05:07

````js
g_mq.innerHTML += (g_inp >> 2) & (1 == 1) ? "F" : " ";
							     ~~~~~~~~

// error
The right-hand side of an arithmetic operation must be of type 'any', 'number', 'bigint' or an enum type.ts(2363)
````

Seems like type shorthand to get boolean?

````js
g_mq.innerHTML += ((g_inp >> 2) & 1) != 0 ? "F" : " ";
````

2025-07-30 Wk 31 Wed - 06:22

Moving parenthesis is not enough. values can be non-0 due to remaining flags.

2025-07-30 Wk 31 Wed - 06:29

````javascript
Function`$${"\x61 \x3D\x20\x6E\x65\x77 \x44\x61\x74\x65\x28\x29\x5B\x27\x67\x65\x74\x53\x65\x63\x6F\x6E\x64\x73\x27\x5D\x28\x29 "}$`();

// error
Argument of type 'TemplateStringsArray' is not assignable to parameter of type 'string'.ts(2345)
````

2025-07-30 Wk 31 Wed - 07:40

We can just replace it with the actual code we found in [5.2 Turn hex bytes into string in terminal](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#52-turn-hex-bytes-into-string-in-terminal).

````ts
g_a = new Date()['getSeconds']()
````

## 3.3 Build a frequency map of the commands being run and tape locations being hit on idle

* [ ] 

2025-07-30 Wk 31 Wed - 08:34

2025-07-30 Wk 31 Wed - 08:42

Experiment results in `experiments/cmd_idx_idle.csv` (n=42) for

````ts
  while (g_exit_code == 0) {
    g_chr = get_and_adv_tape();

    var cmd_idx = Math.floor(g_chr / 2);
    console.log(`${cmd_idx},${g_data_cur},${g_chr}`);

    var fn = g_unk_cmds[cmd_idx];
    if (fn) fn();
  }

````

<img src="https://raw.githubusercontent.com/delta-domain-rnd/delta-trace/refs/heads/main/attachments/Pasted%20image%2020250730084258.png" />

2025-07-31 Wk 31 Thu - 00:17

Using experiment setup in [3.4 Send web messages to terminal to collect experiment data](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#34-send-web-messages-to-terminal-to-collect-experiment-data),

Experiment results in `experiments/cmd_idx_idle.csv` (n=30360) for

````ts
  var i = 0;

  while (g_exit_code == 0) {
    g_chr = get_and_adv_tape();

    var cmd_idx = Math.floor(g_chr / 2);

    if (webio.m_connected) {
      g_socket.send(`${i++},${cmd_idx},${g_data_cur},${g_chr}`)
    }

    var fn = g_unk_cmds[cmd_idx];
    if (fn) fn();
  }
````

Using visidata,

````sh
vd experiments/cmd_idx_idle.csv
# select cmd_idx
# F for frequency analysis
````

<img src="https://raw.githubusercontent.com/delta-domain-rnd/delta-trace/refs/heads/main/attachments/Pasted%20image%2020250731002037.png" />

Those are the commands in effect out of the 23 commands used in idle activity.

2025-08-01 Wk 31 Fri - 08:11

````sh
vd experiments/cmd_idx_idle.csv
# select g_chr
# F for frequency analysis
````

<img src="https://raw.githubusercontent.com/delta-domain-rnd/delta-trace/refs/heads/main/attachments/Pasted%20image%2020250801081102.png" />
^freq-analysis-gchr

## 3.4 Send web messages to terminal to collect experiment data

* [x] Send a "Hello world" over port 4567 to be received in terminal from typescript.

In order to do [3.3 Build a frequency map of the commands being run and tape locations being hit on idle](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#33-build-a-frequency-map-of-the-commands-being-run-and-tape-locations-being-hit-on-idle), we need to be able to gather the data into a csv file. But right now our app can only write to the DOM or to console.log.

2025-07-30 Wk 31 Wed - 12:36

An option:

* [zeromq typescript library](https://www.npmjs.com/package/zeromq) [(on github.io)](https://zeromq.github.io/zeromq.js/).

Trying to run the pub/sub example there.

Looking into [zeromq import issue](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#42-errors-while-attempting-to-import-zeromq-for-non-node-platform).

2025-07-30 Wk 31 Wed - 13:12

Let's try another more basic option. ZeroMQ requires node...

Let's try the [WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket) example,

Start the server over port 3003:

````sh
socat - TCP4-LISTEN:3003
````

then run the client in the game

````js
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
````

2025-07-30 Wk 31 Wed - 13:40

Looking into [firefox connection issue](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#43-firefox-cannot-establish-connection-to-server-at-localhost).

2025-07-30 Wk 31 Wed - 23:29

We can find the different events in the [docs here](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket/close_event): close, error, message, and open.

Start a [websocket server](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#websocat-serve) over `localhost:3003` gathering experimental data to file `a`:

````sh
websocat -s 3003 > a
````

Connect to `localhost:3003`:

````ts
var g_socket = webio.connect("localhost", 3003, (event) => {
  console.log(`Server: ${event.data}`)
})
````

And begin the experiment:

````ts
  var i = 0;
  
  while (g_exit_code == 0) {
    g_chr = get_and_adv_tape();

    var cmd_idx = Math.floor(g_chr / 2);

    if (webio.m_connected) {
		g_socket.send(`${i++},${cmd_idx},${g_data_cur},${g_chr}`)
    }

    var fn = g_unk_cmds[cmd_idx];
    if (fn) fn();
  }
````

Interestingly from the data, this always loops 24 times (i=0 -> i=23) and then `g_exit_code` changes.

But it does not change anywhere at the beginning of the command, so

````ts
g_socket.send(`${i++},${cmd_idx},${g_data_cur},${g_chr},${g_exit_code}`)
````

would show all zeros for `g_exit_code`. It likely happens due to the result of the 24th command.

## 3.5 Write python script to reconstruct tape until new undocumented command

* [x] 

From [^spawn-reconstruct-tape-script](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#spawn-reconstruct-tape-script).

The script for this is [here](https://github.com/LanHikari22/lan-exp-scripts/blob/main/files/2025/persistent/000-mountain-n-dragon-ctf/data/tape.py).

2025-07-31 Wk 31 Thu - 02:59

We will write this in `data/tape.py`. We used that script to give us the checksum and length, but we can use it to generate this function for us given the currently known commands:

````ts
function reconstruct_tape(): number[] {
  var tape: number[] = []; 

  tape.push(...cmd00_write_param_to_reg(/*param16*/ 0x0000));
  tape.push(...cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33af));
  tape.push(...cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33b3));
  tape.push(...cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33b4));
  tape.push(...cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33b6));
  tape.push(...cmd04_sum_reg_param_to_reg(/*param16*/ 0x0001));
  tape.push(...cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33b7));
  tape.push(...cmd00_write_param_to_reg(/*param16*/ 0x0c8c));
  tape.push(...cmd23_noop());
  tape.push(...cmd00_write_param_to_reg(/*param16*/ 0x0d22));
  tape.push(...cmd23_noop());
  tape.push(...cmd15_stack_preserve_call(/*tape_addr16*/ 0x0bb0));
  tape.push(...cmd06_check_reg16_is_param16(/*param16*/ 0x0000));
  tape.push(...cmd13_beq(/*tape_addr16*/ 0x038a));
  tape.push(...cmd06_check_reg16_is_param16(/*param16*/ 0x0002));
  tape.push(...cmd13_beq(/*tape_addr16*/ 0x0059));
  tape.push(...cmd06_check_reg16_is_param16(/*param16*/ 0x0001));
  tape.push(...cmd13_beq(/*tape_addr16*/ 0x046a));
  tape.push(...g_tape_remaining);

  return tape;
}
````

Let's start by adding some argparse stuff in there for the reconstruction or reporting. See [template](https://github.com/LanHikari22/lan-exp-scripts/blob/main/templates/2025/topics/py3/persistant/000-argparse/template_with_subcommands.py).

2025-08-01 Wk 31 Fri - 02:29

Now we should be able to see where the next undocumented command is!

````ts
python3 data/tape.py reconstruct-tape

// out
function reconstruct_tape(): number[] {
  var tape: number[] = [];

  tape.push(...cmd00_write_param_to_reg(/*param16*/ 0x0000));
  tape.push(...cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33af));
  tape.push(...cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33b3));
  tape.push(...cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33b4));
  tape.push(...cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33b6));
  tape.push(...cmd04_sum_reg_param_to_reg(/*param16*/ 0x0001));
  tape.push(...cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33b7));
  tape.push(...cmd00_write_param_to_reg(/*param16*/ 0x0c8c));
  tape.push(...cmd23_noop());
  tape.push(...cmd00_write_param_to_reg(/*param16*/ 0x0d22));
  tape.push(...cmd23_noop());
  tape.push(...cmd15_stack_preserve_call(/*tape_addr16*/ 0x0bb0));
  tape.push(...cmd06_check_reg16_is_param16(/*param16*/ 0x0000));
  tape.push(...cmd13_beq(/*tape_addr16*/ 0x038a));
  tape.push(...cmd06_check_reg16_is_param16(/*param16*/ 0x0002));
  tape.push(...cmd13_beq(/*tape_addr16*/ 0x0059));
  tape.push(...cmd06_check_reg16_is_param16(/*param16*/ 0x0001));
  tape.push(...cmd13_beq(/*tape_addr16*/ 0x046a));
  tape.push(...cmd06_check_reg16_is_param16(/*param16*/ 0x0003));
  tape.push(...cmd13_beq(/*tape_addr16*/ 0x016a));
  tape.push(...cmd06_check_reg16_is_param16(/*param16*/ 0x0005));
  tape.push(...cmd13_beq(/*tape_addr16*/ 0x0044));
  tape.push(...cmd15_stack_preserve_call(/*tape_addr16*/ 0x0ac5));
  tape.push(...remaining_tape.data);

  return tape;
}
````

So far the commands we have documented are

````py
commands = [
    CommandData("cmd00", 0, "cmd00_write_param_to_reg(/*param16*/ {PARAM0})", [16]),
    CommandData(
        "cmd01", 2, "cmd01_store_reg_to_tape_addr(/*tape_addr16*/ {PARAM0})", [16]
    ),
    CommandData("cmd04", 8, "cmd04_sum_reg_param_to_reg(/*param16*/ {PARAM0})", [16]),
    CommandData(
        "cmd06", 12, "cmd06_check_reg16_is_param16(/*param16*/ {PARAM0})", [16]
    ),
    CommandData("cmd13", 26, "cmd13_beq(/*tape_addr16*/ {PARAM0})", [16]),
    CommandData(
        "cmd15", 30, "cmd15_stack_preserve_call(/*tape_addr16*/ {PARAM0})", [16]
    ),
    CommandData("cmd23", 46, "cmd23_noop()", []),
]
````

This script will reconstruct `remaining_tape.ts` such that the first bytes there are of a new command that we have not registered yet.

2025-08-01 Wk 31 Fri - 02:38

To make this more seamless, I'm extracting also the reconstructed commands and reconstructed tape into their own typescript files. This way I can automatically write `reconstructed_tape.ts`. Also just to be clear they are autogen, we put them in an `autogen/` folder: `autogen/reconstructed_tape.ts` and `autogen/remaining_tape.ts`.

So `reconstructed_tape.ts` contents should look like this:

````ts
import * as c from "../reconstructed_commands.ts"
import * as remaining_tape from "./remaining_tape.ts";

export function reconstruct_tape(): number[] {
  var tape: number[] = [];

  tape.push(...c.cmd00_write_param_to_reg(/*param16*/ 0x0000));
  tape.push(...c.cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33af));
  tape.push(...c.cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33b3));
  tape.push(...c.cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33b4));
  tape.push(...c.cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33b6));
  tape.push(...c.cmd04_sum_reg_param_to_reg(/*param16*/ 0x0001));
  tape.push(...c.cmd01_store_reg_to_tape_addr(/*tape_addr16*/ 0x33b7));
  tape.push(...c.cmd00_write_param_to_reg(/*param16*/ 0x0c8c));
  tape.push(...c.cmd23_noop());
  tape.push(...c.cmd00_write_param_to_reg(/*param16*/ 0x0d22));
  tape.push(...c.cmd23_noop());
  tape.push(...c.cmd15_stack_preserve_call(/*tape_addr16*/ 0x0bb0));
  tape.push(...c.cmd06_check_reg16_is_param16(/*param16*/ 0x0000));
  tape.push(...c.cmd13_beq(/*tape_addr16*/ 0x038a));
  tape.push(...c.cmd06_check_reg16_is_param16(/*param16*/ 0x0002));
  tape.push(...c.cmd13_beq(/*tape_addr16*/ 0x0059));
  tape.push(...c.cmd06_check_reg16_is_param16(/*param16*/ 0x0001));
  tape.push(...c.cmd13_beq(/*tape_addr16*/ 0x046a));
  tape.push(...c.cmd06_check_reg16_is_param16(/*param16*/ 0x0003));
  tape.push(...c.cmd13_beq(/*tape_addr16*/ 0x016a));
  tape.push(...c.cmd06_check_reg16_is_param16(/*param16*/ 0x0005));
  tape.push(...c.cmd13_beq(/*tape_addr16*/ 0x0044));
  tape.push(...c.cmd15_stack_preserve_call(/*tape_addr16*/ 0x0ac5));
  tape.push(...remaining_tape.data);

  return tape;
}
````

where `reconstructed_commands.ts` is written manually.

2025-08-01 Wk 31 Fri - 02:49

````sh
python3 data/tape.py reconstruct-tape

# out
Created autogen/remaining_tape.ts
Created autogen/reconstructed_tape.ts
````

````sh
./build.sh
````

````ts
function verify_tape_integrity() {
  const expected_simple_checksum = 287251;
  const expected_num_elements = 13229;

  if (g_tape.length != expected_num_elements) {
    throw new Error(`Expected tape length ${expected_num_elements} but got ${g_tape.length}`);
  }

  var sum = 0;
  
  for (var i=0; i<g_tape.length; i++) {
    sum += g_tape[i];
  }

  if (sum != expected_simple_checksum) {
    throw new Error(`Expected tape sum ${expected_simple_checksum} but got ${sum}`);
  }

  console.log("tape OK");
}

verify_tape_integrity()

# out
tape OK
````

## 3.6 Add program counter information to reconstructed tape to make jumps legible

* [x] 

The script for this is [here](https://github.com/LanHikari22/lan-exp-scripts/blob/main/files/2025/persistent/000-mountain-n-dragon-ctf/data/tape.py).

2025-08-01 Wk 31 Fri - 04:03

The program counter is mostly the sum of the prior bytes read, make it a 4-digit 0 padded hex number like in the params.

Instead of

````ts
tape.push(...c.cmd00_write_param16_to_reg16(/*param16*/ 0x0000));
````

do

````ts
/*0x0000*/ tape.push(...c.cmd00_write_param16_to_reg16(/*param16*/ 0x0000));
````

2025-08-01 Wk 31 Fri - 04:16

````ts
export function reconstruct_tape(): number[] {
  var tape: number[] = [];

  /*0x0000*/ tape.push(...c.cmd00_write_param16_to_reg16(/*param16*/ 0x0000));
  /*0x0003*/ tape.push(...c.cmd01_store_reg16_to_tape_addr(/*tape_addr16*/ 0x33af));
  /*0x0006*/ tape.push(...c.cmd01_store_reg16_to_tape_addr(/*tape_addr16*/ 0x33b3));
  /*0x0009*/ tape.push(...c.cmd01_store_reg16_to_tape_addr(/*tape_addr16*/ 0x33b4));
  /*0x000c*/ tape.push(...c.cmd01_store_reg16_to_tape_addr(/*tape_addr16*/ 0x33b6));
  /*0x000f*/ tape.push(...c.cmd04_sum_reg16_param16_to_reg16(/*param16*/ 0x0001));
  /*0x0012*/ tape.push(...c.cmd01_store_reg16_to_tape_addr(/*tape_addr16*/ 0x33b7));
  /*0x0015*/ tape.push(...c.cmd00_write_param16_to_reg16(/*param16*/ 0x0c8c));
  /*0x0018*/ tape.push(...c.cmd23_noop());
  /*0x0019*/ tape.push(...c.cmd00_write_param16_to_reg16(/*param16*/ 0x0d22));
  /*0x001c*/ tape.push(...c.cmd23_noop());
  /*0x001d*/ tape.push(...c.cmd15_stack_preserve_call(/*tape_addr16*/ 0x0bb0));
  /*0x0020*/ tape.push(...c.cmd06_check_reg16_is_param16(/*param16*/ 0x0000));
  /*0x0023*/ tape.push(...c.cmd13_beq(/*tape_addr16*/ 0x038a));

  // ...
````

OK

## 3.7 Handle loaded param16 flag automatically in commands when reconstructing tape program

* [x] 

From [^spawn-task-handle-loaded-param16](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#spawn-task-handle-loaded-param16).

The script for this is [here](https://github.com/LanHikari22/lan-exp-scripts/blob/main/files/2025/persistent/000-mountain-n-dragon-ctf/data/tape.py).

### 3.7.1 Passing None as a command byte issue

2025-08-01 Wk 31 Fri - 06:49

I'm trying to pass the command byte along with the content so that I check for the first bit. I decided to just follow a simple convention of `cmdlNN` for loaded commands and `cmdNN` for non-loaded. And I just have to mirror all the commands.

Right now it seems like I'm passing `None` for some reason for the byte to `format_command` :

````
[None, 512]

    if command_byte % 2 == 1:
       ~~~~~~~~~~~~~^~~
TypeError: unsupported operand type(s) for %: 'NoneType' and 'int'
````

This is from `scan_next_command`,

````py
content = (
	[command_byte] + command.param_widths
	| pipe.OfIter[int].enumerate()
	| pipe.OfIter[Tuple[int, int]].map(
		pipe.tup2_unpack(enumerated_param_width_to_read_value)
	)
	| pipe.OfIter[int].to_list()
)

print('content', content)

# out
# ...
content [None, 512]
content [None, 6154]
content [None, 778]
content [None, 1]
content [None, 13232]
````

2025-08-01 Wk 31 Fri - 06:58

This assert here is wrong... this is always true! I wanted to throw an error here.

````py
    def enumerated_param_width_to_read_value(i: int, width: int) -> int:
        if width == 16:
            return (tape[i + 2] << 8) + tape[i + 1]
        elif width == 8:
            return tape[i + 1]
        else:
            assert f"unsupported width {width}"
````

Changing it to `raise Exception(f"unsupported width {width}")` gives us

````
Exception: unsupported width 0
````

It's an order of operations thing...

````py
content = (
	[command_byte] + (command.param_widths
	| pipe.OfIter[int].enumerate()
	| pipe.OfIter[Tuple[int, int]].map(
		pipe.tup2_unpack(enumerated_param_width_to_read_value)
	)
	| pipe.OfIter[int].to_list())
)
````

Need to add `[command_byte]` to the rest, not add and then pipeline process!

2025-08-01 Wk 31 Fri - 07:05

Okay this works!

Now we use `cmdl` instead of `cmd` if the bit is set!

## 3.8 Reconstruct labels in the tape program

* [ ] 

2025-08-01 Wk 31 Fri - 11:12

We want to be able to document labels, which are positions in the program that are used as `addr16` in commands.

2025-08-02 Wk 31 Sat - 03:04

We need to create a labels file that can be both manipulated by tools and by us. We need basically three pieces of information: `addr16`, `type`, `label`.

For example,

````
0x01ac u16[]    some_input_array
0x0200 addr16[] some_pointer_arr
````

The types should be space-padded according to the largest type string. Addresses should be 4 0-padded. I put the type last before, but because there is much variance in the size of the label strings, it's better that they are in the middle with known widths to reduce visual noise.

Supported types will be

````
u8 u16 u8[] u16[] addr16 addr16[] unk8 unk16 unk8[] unk16[] num num[]
````

For arrays like `unk8[]`, size is inferred by the address difference from the next label.

Type `num` is to acknowledge that numbers can be arbitrarily large in javascript. Remember this is not really a tape but a javascript array, and its elements can get arbitrarily large. Still it can be useful to think of them as logical bytes. For example `inst8` are mostly bytes themselves and addresses are consistently 16-bit.

### 3.8.1 Pend

## 3.9 Create a driver to search for death and infinity strings

* [ ] 

2025-08-01 Wk 31 Fri - 12:28

So I thought it was a map of some sorts, but these strings defy assumptions of a static map:

````
[ ][ ][ ][ ][ ][ ][ ][ ][ ]
[ ][ ][ ][ ][ ][ ][ ][ ][ ]
[ ][ ][ ][ ][D][^][^][ ][ ]
[ ][ ][ ][D][.][.][.][D][ ]
[ ][ ][ ][<][S][>][v][ ][ ]
[ ][ ][ ][ ][v][ ][ ][ ][ ]
[ ][ ][ ][ ][ ][ ][ ][ ][ ]
[ ][ ][ ][ ][ ][ ][ ][ ][ ]
[ ][ ][ ][ ][ ][ ][ ][ ][ ]


Death Strings
S 2^ D
S 1^ 1< D
S 1^ 3> D
S 1^ 2> 1< D
S 1^ 1> 2< D
S 4> 2v D
S 2< 2> 1^ D

Infinite Stretch 
S 1< I
S 1v I 
S 1> I
````

<a name="death-strings-1" />^death-strings-1

We could search this space and set a parameter for what counts as a stretch from a savepoint. The issue is resetting the game... Normally we have to refresh our browser but what if we can reset the game state ourselves?

2025-08-01 Wk 31 Fri - 12:33

Let's create a control interface for testing. Let's start with a `/hello` command over websocket and a `/help` command to list all commands.

Start a server over `localhost:3004` for control

````sh
websocat -s 3004
````

2025-08-01 Wk 31 Fri - 12:55

Okay they work. Let's add `/reset` and register a handler from the main game to reset the data to be called by it.

2025-08-01 Wk 31 Fri - 13:04

`/reset` works! We just had to set every global back to its initial state, and reconstruct the tape!

What if we gather diff data over the tape? Implement a `/diff-tape` command that dump a diff from original and a `/diff-tape-last` command that would diff since the last snapshot (whenever `/diff-tape` or `/diff-tape-last` are called, update last snapshot.)

2025-08-01 Wk 31 Fri - 13:18

Moving all globals to `globals.ts` so that they could be interfaced in other scripts and we don't have to modify the main script too much from the original.

Ran into an issue where I had to put them in a dictionary to export write. See [issue](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#45-globals-cannot-be-accessed-from-another-typescript-file). <a name="spawn-issue-globals-read-only" />^spawn-issue-globals-read-only

2025-08-01 Wk 31 Fri - 13:47

Okay so `/reset` works again and all in `web_control.ts` this time. Synchronized by frame end.

2025-08-02 Wk 31 Sat - 01:27

````
/diff-tape
Error: The arrays must be of equal size: 13229 != 13240
````

It's adding new elements to the tape...?

2025-08-02 Wk 31 Sat - 01:37

````
/verify-tape
Warn: Expected tape length 13229 but got 13259
Warn: Expected tape sum 287251 but got NaN
/verify-tape
Warn: Expected tape length 13229 but got 13260
Warn: Expected tape sum 287251 but got NaN
````

This is done after one input arrow key up: 13259 -> 13260.  See [investigation](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#65-how-is-the-game-adding-data-to-the-end-of-the-tape). <a name="spawn-invst-add-tape-end" />^spawn-invst-add-tape-end

We need to account for new content.

2025-08-02 Wk 31 Sat - 02:12

Changing `/diff-tape-last` to `/diff-last-tape` just so it's easy to use `StartsWith` to route them.

2025-08-02 Wk 31 Sat - 04:25

See [investigation](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#66-inferring-tape-data-labels-through-play-and-diffs) for inferring data meaning with `/diff-tape`. <a name="spawn-invst-infer-data-diff-play" />^spawn-invst-infer-data-diff-play

2025-08-02 Wk 31 Sat - 06:02

Adding `/read {addr16}` and `/write {addr16} {val}`. These just interact with the element at `{addr16}`, as it may be written or read as any value `num`. <a name="spawn-howtos-js-str-to-num" />^spawn-howtos-js-str-to-num

2025-08-02 Wk 31 Sat - 07:09

Adding `/watch {addr16}` and `/nowatch {addr16}`. This allows the value to be pinged every second to the web console. If it's unwatched with `nowatch`, then it will stop pinging. <a name="spawn-howtos-js-on-interval" />^spawn-howtos-js-on-interval

2025-08-02 Wk 31 Sat - 08:06

We have `/watch {addr16}`. But then it becomes very hard to issue commands... We need a separate data channel from the control channel. We're starting a new websocket with `/data-connect` over `localhost:3005`.

### 3.9.1 Pend

# 4 Issues

## 4.1 TS Build not exposing functions for html

* [x] 

````sh
#!/bin/bash

script_dir=$(dirname "$(readlink -f "$0")")

build() {
    basename="$1"

    npx esbuild "$basename.ts" --bundle --format=iife --outfile=build/$basename.js
}

pushd $script_dir

build mountdrag

popd
````

`--bundle --format=iife` puts everything inside a block, so `mainloop` is not accessible to the html file.

`--bundle --format=cjs` generates extra CommonJS code that's unwanted (including module.exports), although it does expose the functions. This is node specific as the `--help` clarifies as well.

2025-07-30 Wk 31 Wed - 08:15

We could just remove the invocations.

````js
(() => {
[...]
 })();
 
````

Remove those lines for `--format=iife`.

````sh
cat build/$basename.js | python3 -c "import sys; lines = sys.stdin.read().split('\n'); print('\n'.join(lines[1:-2]))" > build/tmp
cat build/tmp > build/$basename.js
rm build/tmp
````

Not a pretty workaround with the tmp file, but we can't cat and then file write simultaneously here.

## 4.2 Errors while attempting to import zeromq for non-node platform

* [x] Reject

2025-07-30 Wk 31 Wed - 13:00

Ran into this issue while trying to build the pub/sub example for [zeromq.js](https://zeromq.github.io/zeromq.js/).

````sh
npm install zeromq
````

````ts
✘ [ERROR] Could not resolve "fs"

    node_modules/cmake-ts/build/loader.js:1:116:
      1 │ ...tringTag,{value:"Module"});const g=require("module"),u=require("fs"),h=require("path");var l=typeof document<"u"?document.currentS...
        ╵                                                                   ~~~~

  The package "fs" wasn't found on the file system but is built into node. Are you trying to bundle
  for node? You can use "--platform=node" to do that, which will remove this error.
````

These are the available platforms

````sh
npx esbuild --help | less

# out (relevant) 
  --platform=...        Platform target (browser | node | neutral,
                        default browser)
````

Our script runs directly on the browser for this game.

This seems node only, so let's look at another option.

### 4.2.1 Reject

## 4.3 Firefox cannot establish connection to server at localhost

* [x] 

2025-07-30 Wk 31 Wed - 13:44

````
Firefox can’t establish a connection to the server at ws://localhost:3003/.
````

This happens when trying to start a client with [websockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket),

````ts
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
````

2025-07-30 Wk 31 Wed - 13:50

This [stackoverflow answer](https://stackoverflow.com/a/60003844/6944447) suggests setting a flag in `about:config`:

````
network.dns.native-is-localhost
````

It's by default set to false.

Resetting back, didn't resolve the problem.

Oh actually it seems we can connect. I didn't realize my server was dying:

````sh
socat - TCP4-LISTEN:3003
````

Could this server itself be the issue?

2025-07-30 Wk 31 Wed - 14:03

Let's try [gh websocat](https://github.com/vi/websocat) instead to serve,

````sh
cargo install websocat
````

Serve on 3003:

````sh
websocat -s 3003
````

<a name="websocat-serve" />^websocat-serve

I works!

I noticed that with websocket the client was sending GET requests, so our basic socat server was not enough.

This should also help with this [greasemonkey task](https://github.com/LanHikari22/lan-setup-notes/blob/main/lan/topics/tooling/web/entries/2025/000%20Making%20Greasemonkey%20scripts.md#17-creating-command-that-can-be-summoned-via-console-to-retrieve-information) to create a robust command line control outside the browser and to retrieve information.

## 4.4 Tape reaches invalid command 51 at 0x0b89

* [x] 

From [^spawn-issue-0b89](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#spawn-issue-0b89).

2025-08-01 Wk 31 Fri - 07:18

We seem to be misreading the tape here:

````
0, 2, 74, 10, 0, 62, 0, 2, 132, 11, 0, 125, 0, 2, 73, 10, 24, 197, 10, 3, 8,
1, 0, 2, 176, 51, 14, 12, 0, 36, 26, 197, 10, 36, 0, 6, 0, 2, 175, 11, 0, 253,
````

Let's try to route the commands manually to see. We're seeing 51 on the next command byte in the remaining tape but this should be incorrect.

````
0, [2, 74, 10,] [0, 62, 0,] [2, 132, 11,] [0, 125, 0,] [2, 73, 10,] [24, 197, 10,] [3, 8,
1,] [0, 2, 176,] 51, 14, 12, 0, 36, 26, 197, 10, 36, 0, 6, 0, 2, 175, 11, 0, 253,
````

2025-08-01 Wk 31 Fri - 08:07

We can confirm through `experiments/cmd_idx_idle.csv` that we never trigger a command whose character is `51`.

[<a name="freq-analysis-gchr" />^freq-analysis-gchr](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#freq-analysis-gchr)

2025-08-01 Wk 31 Fri - 08:38

Oh I forgot to increment the bytes for cmdl by 1...

I added `+0` to all `cmd` commands so that when I mirror, I also change `+0` to `+1` at scale.

I thought I had a `tape OK` signal but the web console wasn't updating...

We are getting `tape OK` now with the `+1` change but we're still expecting `51` after `0x0b89`...

2025-08-01 Wk 31 Fri - 08:54

I also double checked the command sizes. All up to command 15 are 3-sized commands. 16 onward are 1-sized. And I got that correct.

Another possibility is that we've left the text section and entered the data section of the tape...

2025-08-01 Wk 31 Fri - 09:05

16 is 32. So Anything $\ge32$  is a 1-sized command.

````
  2, 179, 51, 
  32, 
  0, 81, 50, 
  46, 
  24, 136, 12, 
  0, 142, 50, 
  46, 
  32, 
  12, 2, 0,
  
  
  28, 197, 10, 
  34, 
  1, 188, 51, 
  12, 0, 0, 
  36, 
  26, 197, 10, 
  34, 
  1, 187, 51, 
  12, 0, 0, 
  28, 71, 11, 
  
  42, 
  8, 1, 0, 
  2, 187, 51, 
  36, 
  24, 197, 10, 
  0, 10, 42, 
  8, 1, 0,
  15, 187, 51, 
  28, 84, 11, 
  8, 60, 0, 
  
  11, 187, 51, 
  13, 188, 51, 
  26, 103, 11, 
  0, 0, 0, 
  2, 188, 51, 
  36, 
  24, 197, 10, 
  36, 
  0, 121, 30, 
  0, 10, 0, 
  
  2, 133, 11, 
  0, 8, 0, 
  2, 74, 10, 
  0, 62, 0, 
  2, 132, 11, 
  0, 125, 0, 
  2, 73, 10, 
  24, 197, 10, 
  3, 8, 1, 
  0, 2, 176, 
  
  51, 
````

I thought a command started with 71 but I missed a 1-sized command 42...

These values don't seem wrong. So maybe We intentionally hit an invalid command to mark the data section?

2025-08-01 Wk 31 Fri - 09:47

You can go the other way too... There's just not enough grammar to this for error to be likely. If it is $\lt 32$  you form a 3-size box. If it's $\ge 32$ you form a 1-size box.  The only error signal is that in case of $\ge 32$ the value exceeds 46, which is the `no-op`  right out of index.

Even if the code is false, no error will be triggered by the interpreter itself. It will simply ignore it and advance the tape.

2025-08-01 Wk 31 Fri - 09:56

Let's just add a 1-size bug command `cmd25_bug` and `cmdl25_buf`. for 50/51.

## 4.5 Globals cannot be accessed from another typescript file

* [x] 

From [^spawn-issue-globals-read-only](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#spawn-issue-globals-read-only).

2025-08-01 Wk 31 Fri - 13:32

When I tried to move the global files to another file so that multiple files could access them, I got errors like

````ts
Cannot assign to 'reg16' because it is a read-only property.ts(2540)
````

Via a quick LLM query,

a wrapper allows them to be accessed:

````ts
import * as reconstructed_tape from './autogen/reconstructed_tape.ts'

export const g = {
  inp: 0,
  mq: document.getElementById("marquee"),
  cond_reg: false,
  reg16: 0,
  pc16: 0,
  sp16: 0,
  inst8: 0,
  new_frame: 0,
  tape: reconstructed_tape.reconstruct_tape(),
  joyp: {},
};
````

So I can access them out like this

````ts
import * as g from "./globals.ts";

// 1
function () {
  // Write register data to the next u16 inst
  g.g.tape[get_and_adv_tape_u16()] = g.g.reg16;
},
````

# 5 HowTos

## 5.1 Raising an error in typescript on undefined possibility

* [x] 

2025-07-30 Wk 31 Wed - 05:05

Trying to go from javascript to typescript I'm encountering some unhandled undefined errors, like

````typescript
'g_mq' is possibly 'null'.ts(18047)
````

We do not want to alter this functionality beyond the necessary, so let's throw an error on those sorts of possibilities.

As in this [stackoverflow answer](https://stackoverflow.com/a/38633821/6944447),

We can throw a `TypeError`:

````typescript
throw new TypeError('Error message');
````

## 5.2 Turn hex bytes into string in terminal

* [x] 

We have the following bytes:

````
\x61 \x3D\x20\x6E\x65\x77 \x44\x61\x74\x65\x28\x29\x5B\x27\x67\x65\x74\x53\x65\x63\x6F\x6E\x64\x73\x27\x5D\x28\x29
````

from

````ts
Function`$${"\x61 \x3D\x20\x6E\x65\x77 \x44\x61\x74\x65\x28\x29\x5B\x27\x67\x65\x74\x53\x65\x63\x6F\x6E\x64\x73\x27\x5D\x28\x29 "}$`();
````

It seems to break into three different segments

````
\x61

\x3D\x20\x6E\x65\x77 

\x44\x61\x74\x65\x28\x29\x5B\x27\x67\x65\x74\x53\x65\x63\x6F\x6E\x64\x73\x27\x5D\x28\x29
````

2025-07-30 Wk 31 Wed - 06:58

From [003 NUMEROLOGY](../../../2025/google-ctf-2025/during/topics/crypto/entries/003%20NUMEROLOGY.md),

````sh
echo "expand 32-byte k" | xxd -p | fold -w4 | xargs
echo "6578 7061 6e64 2033 322d 6279 7465 206b 0a" | tr -d ' ' | xxd -r -p

# out
6578 7061 6e64 2033 322d 6279 7465 206b 0a
expand 32-byte k
````

But with `\xNN`, we can echo them directly:

````sh
echo "\x61 \x3D\x20\x6E\x65\x77 \x44\x61\x74\x65\x28\x29\x5B\x27\x67\x65\x74\x53\x65\x63\x6F\x6E\x64\x73\x27\x5D\x28\x29"

# out
a = new Date()['getSeconds']()
````

They just sometimes decided a space " " and sometimes a "\x20"...

## 5.3 Start basic tcp server/client over terminal for testing

* [x] 

From [this superuser answer](https://superuser.com/a/392518/2972491) which also refers to [socat examples](http://www.dest-unreach.org/socat/doc/socat.html#EXAMPLES),

````sh
sudo apt-get install socat
````

````sh
# start a TCP server and read/write data on port 3003
socat - TCP4-LISTEN:3003

# start a TCP client connection and read/write data on port 3003
nc localhost 3003
````

Note for websocket communication, you can set websocat. This is an example of a websocat server:

[^websocat-serve](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#websocat-serve)

## 5.4 Get script path in python for local files

* [x] 

2025-08-01 Wk 31 Fri - 01:14

From this [stackoverflow answer](https://stackoverflow.com/a/9350788/6944447),

````py
import os
print(os.path.dirname(os.path.realpath(__file__)))
````

## 5.5 How to do stopwatch timing in python3

* [x] 

From this [stackoverflow answer](https://stackoverflow.com/a/67702746/6944447),

````python
import time

start = time.time()
# <code to time>
end = time.time()

print(f"Time taken to run the code was {end-start} seconds")
````

## 5.6 How to print zero-padded hex in typescript

* [x] 

From [<a name="spawn-howtos-hex-js" />^spawn-howtos-hex-js](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#spawn-howtos-hex-js).

2025-08-02 Wk 31 Sat - 02:51

In python3, we can do something like

````python
f'0x{pc:04x}'
````

for a 4 0-padded hexadecimal print

This \[stackoverflow answer\] points to `.toString(16)` :

````ts
var g: number = 255;
alert(g.toString(16));
````

But this wouldn't pad it with 4 zeros.

This [stackoverflow answer](https://stackoverflow.com/a/63297748/6944447) points to `.padStart(n, "0")`:

````ts
str.padStart(9 ,"0")
````

So put together we get...

````ts
`0x${pc.toString(16).padStart(4, "0")}`
````

We can also generalize

````ts
export function addr16(n: number): string {
  return `0x${n.toString(16).padStart(4, "0")}`;
}

// ...

`${addr16(pc)}`
````

## 5.7 How to convert string to num in javascript

* [x] 

From [^spawn-howtos-js-str-to-num](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#spawn-howtos-js-str-to-num).

2025-08-02 Wk 31 Sat - 06:11

From [w3schools reference](https://www.w3schools.com/js/js_type_conversion.asp),

We can use `Number("3.14")`. If it's not a valid number given like `Number("hi!")`, it should yield `NaN`.

Also this is an interesting error when dealing with `NaN`,

````ts
This condition will always return 'false'.ts(2845)

web_control.ts(176, 9): Did you mean 'Number.isNaN(m_addr16_to_access_or_nan)'?
````

We can't use ` ==` with them.

## 5.8 Javascript execute some code every second on interval

* [x] 

From [^spawn-howtos-js-on-interval](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#spawn-howtos-js-on-interval).

2025-08-02 Wk 31 Sat - 07:13

From my [greasemonkey scripting notes](https://github.com/LanHikari22/lan-setup-notes/blob/main/lan/topics/tooling/web/entries/2025/000%20Making%20Greasemonkey%20scripts.md#16-creating-command-code-that-runs-every-t-ms),

````ts
setInterval(function, delay)
````

In my case,

````ts
setInterval(on_interval, 1000)
````

## 5.9 Delete element from array in javascript

* [ ] 

2025-08-02 Wk 31 Sat - 07:52

There are some pretty popular stackoverflow answers like [this](https://stackoverflow.com/a/20690490/6944447).

Debating on different methods as the language has evolved. splice, or filters...

We can use

````ts
arr = arr.filter(item => item !== value)
````

### 5.9.1 Pend

# 6 Investigations

## 6.1 Searching for js reference syntax for bytes call

* [ ] 

Specifically for this

````ts
Function`$${"\x61 \x3D\x20\x6E\x65\x77 \x44\x61\x74\x65\x28\x29\x5B\x27\x67\x65\x74\x53\x65\x63\x6F\x6E\x64\x73\x27\x5D\x28\x29 "}$`();
````

[mozilla js docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference). [js manuals and info](https://javascript.info/manuals-specifications). [ECMA-262 standard](https://ecma-international.org/publications-and-standards/standards/ecma-262/). [ECMA-262 standard web](https://262.ecma-international.org/16.0/index.html)

Seems we're interested in the [template-literal syntax](https://262.ecma-international.org/16.0/index.html#sec-template-literal-lexical-components) from [ECMA-262](https://262.ecma-international.org/16.0/index.html#sec-template-literal-lexical-components).

This [ECMA-262 section](https://262.ecma-international.org/16.0/index.html#sec-getsubstitution) talks about the  case of `$$` so it's likely treated special.

### 6.1.1 Skipped

## 6.2 Why is href being set in cmd22?

* [ ] 

From [^spawn-cmd22-invst](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#spawn-cmd22-invst).

### 6.2.1 Pend

## 6.3 Investigate the process resetting roughly every 24 iterations

* [ ] 

2025-08-01 Wk 31 Fri - 09:42

The experiment data we gathered in `experiments/cmd_idx_idle.csv` show that we're in some sort of control loop 23 instructions long every frame.  Let's examine what that loop does.

The frequency analysis over `i` also shows uniform indices. All indices have exactly 1265 counts.  The sample size is 30360... $1265 \times 24 = 30360$  . It seems that when we shut down the experiment, it did so on frame update which aligns exactly at the end of this loop.

### 6.3.1 Pend

## 6.4 On Javascript arrays and shallow copying

* [x] 

2025-08-01 Wk 31 Fri - 13:49

So I could do

````ts
export var m_tape_snapshot: number[] = g.g.tape;
````

But is this a shallow copy or just setting a reference?

[this post](https://medium.com/@ziyoshams/deep-copying-javascript-arrays-4d5fc45a6e3e) explains that it is indeed just a reference. For our purposes a shallow copy suffices, but we can use the spread operator syntax `[...arr]`  for a deep copy.

## 6.5 How is the game adding data to the end of the tape?

* [ ] 

From [^spawn-invst-add-tape-end](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#spawn-invst-add-tape-end).

2025-08-02 Wk 31 Sat - 01:40

When we press a key, like up and it registers, a new byte is added to the tape

````
/verify-tape
Warn: Expected tape length 13229 but got 13259
Warn: Expected tape sum 287251 but got NaN
/verify-tape
Warn: Expected tape length 13229 but got 13260
Warn: Expected tape sum 287251 but got NaN
````

We need to find out how.

### 6.5.1 Pend

## 6.6 Inferring tape data labels through play and diffs

From [^spawn-invst-infer-data-diff-play](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#spawn-invst-infer-data-diff-play).

2025-08-02 Wk 31 Sat - 02:27

These are the tape changes on the string `S ^ ^ D`   (Start, up, up, dead)

````
/diff-tape
orig.length: 13229, tape.length: 13260
13229: NA != 4
13230: NA != 4
13231: NA != 2
13232: NA != undefined
13233: NA != 1
13234: NA != 1
13235: NA != 0
13236: NA != 0
13237: NA != undefined
13238: NA != 0
13239: NA != 1
13240: NA != undefined
13241: NA != undefined
13242: NA != undefined
13243: NA != undefined
13244: NA != undefined
13245: NA != undefined
13246: NA != undefined
13247: NA != undefined
13248: NA != undefined
13249: NA != undefined
13250: NA != undefined
13251: NA != undefined
13252: NA != undefined
13253: NA != undefined
13254: NA != undefined
13255: NA != undefined
13256: NA != undefined
13257: NA != undefined
13258: NA != 1
13259: NA != 1
````

Those last two entries

````
13258: NA != 1
13259: NA != 1
````

mark the input string "up up"

````
13258: NA != 2
13259: NA != 2
13260: NA != 2
13261: NA != 2
13262: NA != 2
13263: NA != 2
13264: NA != 2
13265: NA != 2
13266: NA != 2
13267: NA != 2
13268: NA != 2
13269: NA != 2
13270: NA != 2
13271: NA != 2
13272: NA != 2
13273: NA != 2
13274: NA != 2
13275: NA != 2
13276: NA != 2
13277: NA != 2
13278: NA != 2
13279: NA != 2
13280: NA != 2
13281: NA != 2
````

It's also able to track how many times we're pressing Right.

It just keeps going. Since this starts from 13258, so far I've reached $13538 - 13258 + 1 =  281$ inputs.

So let's map all the input values:

````
L R F B I U
0 2 1 3 4 5
````

<a name="input-data-values" />^input-data-values

2025-08-02 Wk 31 Sat - 02:37

Here is what happens on reset and string `S ^ ^ D`

````
/reset
Resetting

/diff-last-tape
snap.length: 13260, tape.length: 13240
13229: 4 != 0
13230: 4 != 0
13231: 2 != 0
13233: 1 != undefined
13234: 1 != undefined
13258: 1 != NA
13259: 1 != NA

Press F

/diff-last-tape
snap.length: 13240, tape.length: 13259
13231: 0 != 1
13233: undefined != 1
13234: undefined != 1
13240: NA != undefined
13241: NA != undefined
13242: NA != undefined
13243: NA != undefined
13244: NA != undefined
13245: NA != undefined
13246: NA != undefined
13247: NA != undefined
13248: NA != undefined
13249: NA != undefined
13250: NA != undefined
13251: NA != undefined
13252: NA != undefined
13253: NA != undefined
13254: NA != undefined
13255: NA != undefined
13256: NA != undefined
13257: NA != undefined
13258: NA != 1

Press F

/diff-last-tape
snap.length: 13259, tape.length: 13260
13229: 0 != 4
13230: 0 != 4
13231: 1 != 2
13259: NA != 1

Key inputs no longer make a difference

/diff-last-tape
snap.length: 13260, tape.length: 13260
````

On each right press that doesn't lead to death:

````
/diff-last-tape
snap.length: 13276, tape.length: 13277
13231: 18 != 19
13276: NA != 2

/diff-last-tape
snap.length: 13277, tape.length: 13278
13231: 19 != 20
13277: NA != 2
````

So likely `13231` is the input string length. Let's display those values in hex since we've done everything else like that. ^spawn-howtos-hex-js

````
/diff-last-tape
snap.length: 13259, tape.length: 13260
0x33af: 1 != 2
0x33cb: NA != 2

/diff-last-tape
snap.length: 13260, tape.length: 13261
0x33af: 2 != 3
0x33b6: 0 != 2
0x33cc: NA != 2
````

We also need labels as per this [task](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#38-reconstruct-labels-in-the-tape-program) to document what those values mean.

````
/diff-last-tape
snap.length: 13623, tape.length: 13625
0x33af: 365 != 367
0x3537: NA != 2
0x3538: NA != 2
````

This is after going right 367 times.

So starting `0x33ca` we have a `u8[]` signifying input strings. We should add the labels

````
0x33af num    d_inp_str_len
0x33ca u8[]   d_inp_str
````

2025-08-02 Wk 31 Sat - 03:55

````
/diff-last-tape
snap.length: 13259, tape.length: 13260
0x33af: 1 != 2
0x33b1: 1 != 4
0x33b2: 1 != 4
0x33cb: NA != 4
````

These `0x33b1` and `0x33b2` seem to be flags. T

On pressing Right, we get

````
/diff-last-tape
snap.length: 13260, tape.length: 13261
0x33af: 2 != 3
0x33b1: 4 != 2
0x33b2: 4 != 2
0x33cc: NA != 2
````

Then pressing left...

````
/diff-last-tape
snap.length: 13262, tape.length: 13263
0x33ad: 0 != 1
0x33ae: 0 != 1
0x33af: 4 != 5
0x33b1: 2 != 0
0x33b2: 2 != 0
0x33ce: NA != 0
````

Actually they seem to map to the input pressed? So pressing X should give us 5 and Z 4 according to

[^input-data-values](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#input-data-values)

````
Press Z

/diff-last-tape
snap.length: 13260, tape.length: 13261
0x33af: 2 != 3
0x33b1: 2 != 4
0x33b2: 2 != 4
0x33cc: NA != 4

Press X

/diff-last-tape
snap.length: 13261, tape.length: 13262
0x33af: 3 != 4
0x33b1: 4 != 5
0x33b2: 4 != 5
0x33cd: NA != 5
````

They do. Call them `d_last_inp1` and `d_last_inp2`.

### 6.6.1 Capturing diff on last input for a death string

2025-08-02 Wk 31 Sat - 04:03

Capturing the diff for the last input

On String `S ^ ^ D`,

````
/diff-last-tape
snap.length: 13259, tape.length: 13260
0x33ad: 0 != 4
0x33ae: 0 != 4
0x33af: 1 != 2
0x33cb: NA != 1
````

On String `S < < > > > D`,

````
/diff-last-tape
snap.length: 13262, tape.length: 13263
0x33ad: 0 != 2
0x33ae: 0 != 2
0x33af: 4 != 5
0x33ce: NA != 2
````

On String `S ^ < D`,

````
/diff-last-tape
snap.length: 13259, tape.length: 13260
0x33ad: 0 != 1
0x33ae: 0 != 1
0x33af: 1 != 2
0x33b1: 1 != 0
0x33b2: 1 != 0
0x33cb: NA != 0
````

At least we know that `0x33ad` and `0x33ae` mirror one another and seem to update on death. So call them `unk_death_33ad_1`  and `unk_death_33ad_2`.

[^death-strings-1](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#death-strings-1)

2025-08-02 Wk 31 Sat - 04:27

On String `S 4> 2v D`,

````
/diff-last-tape
snap.length: 13263, tape.length: 13264
0x33ad: 0 != 8
0x33ae: 0 != 8
0x33af: 5 != 6
0x33cf: NA != 3
````

Actually, this is the snapshot diff on `S 4> 1v`,

````
/diff-last-tape
snap.length: 13262, tape.length: 13263
0x33af: 4 != 5
0x33b1: 0 != 1
0x33b2: 2 != 3
0x33ce: NA != 3
````

Note that `0x33b1 <d_last_inp1>` and `0x33b2 <d_last_inp2>` are different. They're not mirrors here.

[^input-data-values](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#input-data-values)

So 2->3 for `0x33b2 <d_last_inp2>`  does seem to signify R->B as we expect. So let's call that `0x33b2 <d_last_inp>` and revert `0x33b1 <d_unk_33b1>`.

but why 0->1 for `0x33b1 <d_unk_33b1>`?

For consistency, all data labels will be `d_xxx`.  Jumps/labels within code will be `l_xxx`.  And if we think something marks a function, then `fn_xxx`.

2025-08-02 Wk 31 Sat - 06:25

This is in the beginning, the very first `/diff-last-tape` from launching the game.

````
/diff-last-tape
snap.length: 13229, tape.length: 13240
0x33ad: NA != 0
0x33ae: NA != 0
0x33af: NA != 0
0x33b0: NA != undefined
0x33b1: NA != undefined
0x33b2: NA != undefined
0x33b3: NA != 0
0x33b4: NA != 0
0x33b5: NA != undefined
0x33b6: NA != 0
0x33b7: NA != 1
````

2025-08-02 Wk 31 Sat - 06:26

After performing each string, read the value of `0x33b1 <d_unk_33b1>` with

````
/reset

Do the specified string

/read 0x33b1
````

[^input-data-values](000%20Solving%20Mountain%20and%20Dragon%20CTF.md#input-data-values)

|Row|String0|String1|String2|String3|String4|String5|String6|
|---|-------|-------|-------|-------|-------|-------|-------|
|Strings|`S`|`S 1>`|`S 2>`|`S 3>`|`S 4>`|`S 4> 1v`|`S 4> 2v D`|
|0x33b1|`NaN`|2|2|2|0|1|1|
|0x33b2|`NaN`|2|2|2|2|3|3|
|Strings|`S 4> 1<`|`S 4> 2<`|`S 4> 1v 1^`|`S 4> 1v 1^ 1v`|`S 4> 1v 1^ 1v 1^`<br>|||
|0x33b1|2|2|3|1|3|||
|||||||||

2025-08-02 Wk 31 Sat - 08:20

Oops. I had to correct `0x3bb1` to `0x33b1` here. But I should have been using the correct address in data collection. `0x3bb1` cannot be accessed under many strings.

2025-08-02 Wk 31 Sat - 08:22

We can see with `/watch 0x33b1` that it tracks with the input code most of the time until death.

### 6.6.2 Pend

# 7 Tooling

# 8 Side Notes

## 8.1 vscode run extension

2025-07-30 Wk 31 Wed - 08:12

[extension github](https://github.com/HubbleCommand/run).

Not sure why this isn't by default that we can run command from the vscode explorer.

I kept going to the terminal just to rund `build.sh`.

# 9 References

1. [the game](https://2tie.rustedlogic.net/games/adv/adventure.html) ^1

1. [lan-exp-scripts files](https://github.com/LanHikari22/lan-exp-scripts/tree/main/files/2025/persistent/000-mountain-n-dragon-ctf) ^2

 > 
 > Supplementary content and source files for this challenge.
