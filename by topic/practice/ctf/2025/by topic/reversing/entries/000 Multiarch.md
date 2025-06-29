
# 1 Journal

![[Pasted image 20250628205318.png]]

See the challenge [here](<https://capturetheflag.withgoogle.com/challenges/rev-multiarch-1>).

2025-06-28 Wk 26 Sat - 20:59

```sh
mkdir -p ~/src/exp/puzzles/ctf/2025-06/multiarch-1
mv ~/Downloads/{id}.zip ~/src/exp/puzzles/ctf/2025-06/multiarch-1
cd /home/lan/src/exp/puzzles/ctf/2025-06/multiarch-1
unzip *.zip
cd rev-multiarch-1
```

```sh
ls -al
total 52
drwxrwxr-x 2 lan lan  4096 Jun 28 21:05 .
drwxrwxr-x 3 lan lan  4096 Jun 28 21:05 ..
-rw-r--r-- 1 lan lan   757 Dec 31  1979 crackme.masm
-rw-r--r-- 1 lan lan  1272 Dec 31  1979 Dockerfile
-rw-r--r-- 1 lan lan 26888 Dec 31  1979 multiarch
-rw-r--r-- 1 lan lan  1246 Dec 31  1979 nsjail.cfg
-rw-r--r-- 1 lan lan   301 Dec 31  1979 runner.py

```

```sh
file crackme.masm

# output
crackme.masm: data
```

```sh
strings crackme.masm                                                                                                                  
# output
MASM
0       Sg
Welcome to the multiarch of madness! Let's see how well you understand it.
Seems like you have some learning to do!
Congrats! You are the Sorcerer Supreme of the multiarch!
Challenge 1 - What's your favorite number? Challenge 2 - Tell me a joke: Challenge 3 - Almost there! But can you predict the future?
What number am I thinking of? 

```

![[Pasted image 20250628210849.png]]

MASM... Some assembly file...

The dockerfile uses the following:

```dockerfile
FROM gcr.io/kctf-docker/challenge@sha256:9f15314c26bd681a043557c9f136e7823414e9e662c08dde54d14a6bfd0b619f
```

```sh
file multiarch                                   

# output
multiarch: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=dc495115eb37cb56a37d5ac691cf406d06f185c7, for GNU/Linux 4.4.0, stripped
```


## 1.1 Running Docker and seeing the files

2025-06-28 Wk 26 Sat - 21:12

Let's try to run the docker file

```sh
sudo docker build .
```

```
ERROR: failed to solve: failed to compute cache key: failed to calculate checksum of ref f48d4e4a-6208-4e7f-a042-ff90ac2e79a0::lx7hnrvg2715mlpb0qkb8j9bc: "/flag": not found
```

It tries to copy flag...

```
COPY flag /
```

```sh
touch flag
sudo docker build .
```

![[Pasted image 20250628211623.png]]

```sh
sudo docker build -t "ctf" .
sudo docker images          

# output
REPOSITORY                        TAG       IMAGE ID       CREATED          SIZE
ctf                               latest    f1aa8f43c56b   53 seconds ago   690MB
```


```sh
sudo docker run ctf        

# output
+ mount -t proc none /kctf/.fullproc/proc
mount: /kctf/.fullproc/proc: permission denied.
       dmesg(1) may have more information after failed mount system call.
```

2025-06-28 Wk 26 Sat - 21:28

Modify runner.py

```sh
chmod +x runner.py
chmod +x multiarch

# MULTIARCH_PATH = "/home/user/multiarch"
MULTIARCH_PATH = "./multiarch"
# CRACKME_PATH = "/home/user/crackme.masm"
CRACKME_PATH = "./crackme.masm"
```

Hmm.  if you do `strings` on their provided multiarch you get `/lib64/ld-linux-x86-64.so.2`

This will likely not work on my system. I need to get docker working.

```sh
objdump -d crackme.masm
objdump: crackme.masm: file format not recognized
```

Teammate was able to disassemmble multiarch via IDA.

```sh
mkdir -p ~/src/exp/puzzles/ctf/2025-06/multiarch-1/data
cp ~/Downloads/multiarch.c ~/src/exp/puzzles/ctf/2025-06/multiarch-1/data
```

That file interprets, and it will print, via

```c
__int64 __fastcall sub_196B(__int64 a1)
{
  const char *v1; // rax

  v1 = (const char *)(*(__int64 (**)(void))(a1 + 40))();
  if ( v1 )
  {
    fprintf(stdout, "Here, have a flag: %s\n", v1);
    return 1;
  }
  else
  {
    *(_BYTE *)(a1 + 48) = 1;
    return 0;
  }
}
```


So I am able to launch the docker via

```sh
sudo docker run -it ctf /bin/bash
```

which is similar to what my teammate did.

```sh
sudo docker rmi ctf -f
sudo docker build -t "ctf" .
sudo docker run -it ctf /bin/bash
```

```
root@995ac194e58a:/home/user# cd /chroot/home/user/
root@995ac194e58a:/chroot/home/user# ls
crackme.masm  multiarch  runner.py
root@995ac194e58a:/chroot/home/user# cd /home/user/
root@995ac194e58a:/home/user# ls
nsjail.cfg

```

## 1.2 Challenge 1

```sh
root@995ac194e58a:/chroot/home/user# ./multiarch  crackme.masm
[I] initializing multiarch emulator
[I] executing program
Welcome to the multiarch of madness! Let's see how well you understand it.
Challenge 1 - What's your favorite number? 345
Seems like you have some learning to do!
[I] done!
```


We can run it quickly with

```sh
sudo docker run -it ctf /chroot/home/user/multiarch /chroot/home/user/crackme.masm
```

So we need to figure out this number thing.

The string `Challenge 1 - What's your favorite number?` is part of `crackme.masm` and not multiarch.

This actually just works on my machine:

```sh
./multiarch crackme.masm
```

We can disassemble it and look

```sh
objdump -d multiarch | less
```

### 1.2.1 Find out where Challenge 1 string is run

2025-06-28 Wk 26 Sat - 22:02

Let's first try to just step to it via gdb.

We can break in `.init`:

```
0000000000001000 <.init>:
    1000:       f3 0f 1e fa             endbr64
    1004:       48 83 ec 08             sub    $0x8,%rsp
    1008:       48 8b 05 c1 4f 00 00    mov    0x4fc1(%rip),%rax        # 5fd0 <rand@plt+0x4e10>
    100f:       48 85 c0                test   %rax,%rax
    1012:       74 02                   je     1016 <getenv@plt-0x1a>
    1014:       ff d0                   call   *%rax
    1016:       48 83 c4 08             add    $0x8,%rsp
    101a:       c3                      ret
```


```sh
gdb ./multiarch

# in gdb
>>> b .init
Function ".init" not defined.
Make breakpoint pending on future shared library load? (y or [n]) n
>>> info functions
All defined functions:
```

Hmm. 




# 2 Investigation

## 2.1 What are masm files? (PEND)

2025-06-28 Wk 26 Sat - 21:19

I am guessing this: [Wiki: Microsoft Macro Assembler](<https://en.wikipedia.org/wiki/Microsoft_Macro_Assembler>)

Teammate says it might stand for multiarch asm?

## 2.2 What is nsjail.fg? (PEND)

2025-06-28 Wk 26 Sat - 21:59

# 3 Tooling

## 3.1 Setup [yara](<https://github.com/VirusTotal/yara>)

```sh
mkdir -p ~/src/cloned/gh/VirusTotal/
pushd ~/src/cloned/gh/VirusTotal/
git clone git@github.com:VirusTotal/yara.git
```

2025-06-28 Wk 26 Sat - 22:19

```sh
# in /home/lan/src/cloned/gh/VirusTotal/yara
./build.sh

# output
checking for EVP_sha256 in -lcrypto... yes
checking that generated files are newer than configure... done
configure: creating ./config.status
config.status: error: cannot find input file: 'Makefile.in'
make: *** No targets specified and no makefile found.  Stop.
```

## 3.2 Install [Ghidra](<https://github.com/NationalSecurityAgency/ghidra>)

Get [JDK21](<https://adoptium.net/temurin/releases/?os=any&arch=any&version=21>)

![[Pasted image 20250628221153.png]]

```sh
cd ~/Downloads/
tar -xvjf ~/Downloads/OpenJDK21U-jdk_x64_linux_hotspot_21.0.7_6.tar.gz                                                   
# output
bzip2: (stdin) is not a bzip2 file.
tar: Child returned status 2
tar: Error is not recoverable: exiting now

```

via [this answer](<https://superuser.com/a/362537>),

```sh
tar xvf ~/Downloads/OpenJDK21U-jdk_x64_linux_hotspot_21.0.7_6.tar.gz
```

We now can use

```
/home/lan/Downloads/jdk-21.0.7+6/bin/java
```

Get Ghidra from the [releases](<https://github.com/NationalSecurityAgency/ghidra/releases>).

Latest is Ghidra 11.4 for me as of June 2025.

```sh
unzip ~/Downloads/ghidra_11.4_PUBLIC_20250620.zip
cd ghidra_11.4_PUBLIC
./ghidraRun
```

Set the JDK to `/home/lan/Downloads/jdk-21.0.7+6/bin/`

![[Pasted image 20250628222643.png]]

OK

![[Pasted image 20250628232152.png]]

# 4 Debugging

## 4.1 Looking into Ghidra analysis of multiarch

2025-06-28 Wk 26 Sat - 23:24

```sh
objdump -d multiarch | less
```

Shows us that we start with a section`.init` at address `0000000000001000`. 

In ghidra, `Window > Memory Map`,

![[Pasted image 20250628233443.png]]

|                    |                              |                              |        |       |       |       |       |       |       |         |       |                           |            |                                                                             |
| ------------------ | ---------------------------- | ---------------------------- | ------ | ----- | ----- | ----- | ----- | ----- | ----- | ------- | ----- | ------------------------- | ---------- | --------------------------------------------------------------------------- |
| segment_2.1        | 00100000                     | 0010034f                     | 0x350  | true  | false | false | false | false |       | Default | true  | multiarch[0x0, 0x350]     | Elf Loader | Loadable segment [0x0 - 0xcdf]                                              |
| .note.gnu.property | 00100350                     | 0010038f                     | 0x40   | true  | false | false | false | false |       | Default | true  | multiarch[0x350, 0x40]    | Elf Loader | SHT_NOTE [0x350 - 0x38f]                                                    |
| .note.gnu.build-id | 00100390                     | 001003b3                     | 0x24   | true  | false | false | false | false |       | Default | true  | multiarch[0x390, 0x24]    | Elf Loader | SHT_NOTE [0x390 - 0x3b3]                                                    |
| .interp            | 001003b4                     | 001003cf                     | 0x1c   | true  | false | false | false | false |       | Default | true  | multiarch[0x3b4, 0x1c]    | Elf Loader | SHT_PROGBITS [0x3b4 - 0x3cf]                                                |
| .gnu.hash          | 001003d0                     | 001003ff                     | 0x30   | true  | false | false | false | false |       | Default | true  | multiarch[0x3d0, 0x30]    | Elf Loader | SHT_GNU_HASH [0x3d0 - 0x3ff]                                                |
| .dynsym            | 00100400                     | 00100747                     | 0x348  | true  | false | false | false | false |       | Default | true  | multiarch[0x400, 0x348]   | Elf Loader | SHT_DYNSYM [0x400 - 0x747]                                                  |
| .dynstr            | 00100748                     | 001008c1                     | 0x17a  | true  | false | false | false | false |       | Default | true  | multiarch[0x748, 0x17a]   | Elf Loader | SHT_STRTAB [0x748 - 0x8c1]                                                  |
| .gnu.version       | 001008c2                     | 00100907                     | 0x46   | true  | false | false | false | false |       | Default | true  | multiarch[0x8c2, 0x46]    | Elf Loader | SHT_GNU_versym [0x8c2 - 0x907]                                              |
| .gnu.version_r     | 00100908                     | 00100967                     | 0x60   | true  | false | false | false | false |       | Default | true  | multiarch[0x908, 0x60]    | Elf Loader | SHT_GNU_verneed [0x908 - 0x967]                                             |
| .rela.dyn          | 00100968                     | 00100a6f                     | 0x108  | true  | false | false | false | false |       | Default | true  | multiarch[0x968, 0x108]   | Elf Loader | SHT_RELA [0x968 - 0xa6f]                                                    |
| .rela.plt          | 00100a70                     | 00100cdf                     | 0x270  | true  | false | false | false | false |       | Default | true  | multiarch[0xa70, 0x270]   | Elf Loader | SHT_RELA [0xa70 - 0xcdf]                                                    |
| .init              | 00101000                     | 0010101a                     | 0x1b   | true  | false | true  | false | false |       | Default | true  | multiarch[0x1000, 0x1b]   | Elf Loader | SHT_PROGBITS [0x1000 - 0x101a]                                              |
| .plt               | 00101020                     | 001011cf                     | 0x1b0  | true  | false | true  | false | false |       | Default | true  | multiarch[0x1020, 0x1b0]  | Elf Loader | SHT_PROGBITS [0x1020 - 0x11cf]                                              |
| .text              | 001011e0                     | 001030c3                     | 0x1ee4 | true  | false | true  | false | false |       | Default | true  | multiarch[0x11e0, 0x1ee4] | Elf Loader | SHT_PROGBITS [0x11e0 - 0x30c3]                                              |
| .fini              | 001030c4                     | 001030d0                     | 0xd    | true  | false | true  | false | false |       | Default | true  | multiarch[0x30c4, 0xd]    | Elf Loader | SHT_PROGBITS [0x30c4 - 0x30d0]                                              |
| .rodata            | 00104000                     | 001047e7                     | 0x7e8  | true  | false | false | false | false |       | Default | true  | multiarch[0x4000, 0x7e8]  | Elf Loader | SHT_PROGBITS [0x4000 - 0x47e7]                                              |
| .eh_frame_hdr      | 001047e8                     | 00104933                     | 0x14c  | true  | false | false | false | false |       | Default | true  | multiarch[0x47e8, 0x14c]  | Elf Loader | SHT_PROGBITS [0x47e8 - 0x4933]                                              |
| .eh_frame          | 00104938                     | 00104ee7                     | 0x5b0  | true  | false | false | false | false |       | Default | true  | multiarch[0x4938, 0x5b0]  | Elf Loader | SHT_PROGBITS [0x4938 - 0x4ee7]                                              |
| .note.ABI-tag      | 00104ee8                     | 00104f07                     | 0x20   | true  | false | false | false | false |       | Default | true  | multiarch[0x4ee8, 0x20]   | Elf Loader | SHT_NOTE [0x4ee8 - 0x4f07]                                                  |
| .init_array        | 00105dd0                     | 00105dd7                     | 0x8    | true  | false | false | false | false |       | Default | true  | multiarch[0x5dd0, 0x8]    | Elf Loader | SHT_INIT_ARRAY [0x5dd0 - 0x5dd7]                                            |
| .fini_array        | 00105dd8                     | 00105ddf                     | 0x8    | true  | false | false | false | false |       | Default | true  | multiarch[0x5dd8, 0x8]    | Elf Loader | SHT_FINI_ARRAY [0x5dd8 - 0x5ddf]                                            |
| .dynamic           | 00105de0                     | 00105fbf                     | 0x1e0  | true  | false | false | false | false |       | Default | true  | multiarch[0x5de0, 0x1e0]  | Elf Loader | SHT_DYNAMIC [0x5de0 - 0x5fbf]                                               |
| .got               | 00105fc0                     | 00105fe7                     | 0x28   | true  | false | false | false | false |       | Default | true  | multiarch[0x5fc0, 0x28]   | Elf Loader | SHT_PROGBITS [0x5fc0 - 0x5fe7]                                              |
| .got.plt           | 00105fe8                     | 001060cf                     | 0xe8   | true  | false | false | false | false |       | Default | true  | multiarch[0x5fe8, 0xe8]   | Elf Loader | SHT_PROGBITS [0x5fe8 - 0x60cf]                                              |
| .data              | 001060d0                     | 001060df                     | 0x10   | true  | true  | false | false | false |       | Default | true  | multiarch[0x60d0, 0x10]   | Elf Loader | SHT_PROGBITS [0x60d0 - 0x60df]                                              |
| .bss               | 001060e0                     | 0010610f                     | 0x30   | true  | true  | false | false | false |       | Default | true  | init[0x30]                | Elf Loader | SHT_NOBITS [0x60e0 - 0x610f]                                                |
| EXTERNAL           | 00107000                     | 001070f7                     | 0xf8   | true  | true  | false | false | true  |       | Default | false | uninit[0xf8]              | Elf Loader | NOTE: This block is artificial and allows ELF Relocations to work correctly |
| .comment           | .comment::00000000           | .comment::0000001a           | 0x1b   | false | false | false | false | false | OTHER | Default | true  | multiarch[0x60e0, 0x1b]   | Elf Loader | SHT_PROGBITS [not-loaded]                                                   |
| .shstrtab          | .shstrtab::00000000          | .shstrtab::00000105          | 0x106  | false | false | false | false | false | OTHER | Default | true  | multiarch[0x60fb, 0x106]  | Elf Loader | SHT_STRTAB [not-loaded]                                                     |
| _elfSectionHeaders | _elfSectionHeaders::00000000 | _elfSectionHeaders::000006ff | 0x700  | false | false | false | false | false | OTHER | Default | true  | multiarch[0x6208, 0x700]  | Elf Loader | Elf Section Headers                                                         |



This shows us all the sections analyzed. we can find `.init` here too. in 00101000.

In Ghidra, `g > 00101000`

this puts us in `void _DT_INIT(void)`.

Alt+Left to go back through references.

![[Pasted image 20250628234155.png]]

fprintf...

an xref:

|          |                     |               |      |
| -------- | ------------------- | ------------- | ---- |
| 00106058 | PTR_printf_00106058 | addr ::printf | DATA |


but the string `initializing multiarch emulator`... Maybe we can find this in Ghidra.

```sh
strings multiarch | less

# output
[...]
[I] initializing multiarch emulator
[...]
```

Ctrl+F, select all fields, and search it. And we find it.

```C
undefined8 FUN_00102f5e(int param_1,undefined8 *param_2)

{
  char cVar1;
  long lVar2;
  long lVar3;
  undefined8 uVar4;
  
  setbuf(stdin,(char *)0x0);
  setbuf(stdout,(char *)0x0);
  setbuf(stderr,(char *)0x0);
  if (param_1 < 2) {
    fprintf(stderr,"[E] usage: %s [path to .masm file]\n",*param_2);
    uVar4 = 2;
  }
  else {
    fwrite("[I] initializing multiarch emulator\n",1,0x24,stderr);
    lVar2 = FUN_00102dd9(param_2[1]);
    if (lVar2 == 0) {
      fwrite("[E] couldn\'t load multiarch program\n",1,0x24,stderr);
      uVar4 = 1;
    }
    else {
      lVar3 = FUN_00101319(lVar2);
      fwrite("[I] executing program\n",1,0x16,stderr);
      do {
        cVar1 = FUN_001029d1(lVar3);
      } while (cVar1 != '\0');
      if (*(char *)(lVar3 + 0x30) == '\0') {
        fwrite("[I] done!\n",1,10,stderr);
      }
      else {
        fwrite("[E] execution failed\n",1,0x15,stderr);
        FUN_00102a1e(lVar3,1);
      }
      FUN_00101427(lVar3);
      FUN_00102d8d(lVar2);
      uVar4 = 0;
    }
  }
  return uVar4;
}
```
^fn-2f5e


So we know this runs. It's using `fwrite`. 

So `FUN_00102f5e` is our main.

objdump is not able to recognize it as a function

```
    2f5e:       55                      push   %rbp
    2f5f:       53                      push   %rbx
    2f60:       48 83 ec 08             sub    $0x8,%rsp
    2f64:       89 fb                   mov    %edi,%ebx
```


## 4.2 Try to add a breakpoint to the point a message is printed

2025-06-28 Wk 26 Sat - 23:44

If we can break here via gdb, we could intercept when the messages are printed.

We can see it's actually part of the binary via

```sh
objdump -d multiarch | less

# output
[...]
00000000000010e0 <printf@plt>:
    10e0:       ff 25 72 4f 00 00       jmp    *0x4f72(%rip)        # 6058 <rand@plt+0x4e98>
    10e6:       68 0b 00 00 00          push   $0xb
    10eb:       e9 30 ff ff ff          jmp    1020 <getenv@plt-0x10>
[...]
```


```sh
gdb ./multiarch

# in gdb
>>> b printf
Breakpoint 1 at 0x10e0
>>> run crackme.masm

```

No good it doesn't break.

Now we know the via [[#^fn-2f5e]] to break there for the `fwrite`

```sh
gdb ./multiarch

# in gdb
>>> b *0x2f5e
>>> run crackme.masm
Starting program: /home/lan/src/exp/puzzles/ctf/2025-06/multiarch-1/rev-multiarch-1/multiarch crackme.masm
Warning:
Cannot insert breakpoint 1.
Cannot access memory at address 0x2f5e
```

Maybe we should do 00102f5e?

```sh
gdb ./multiarch

# in gdb
>>> b *0x00102f5e
>>> run crackme.masm
Starting program: /home/lan/src/exp/puzzles/ctf/2025-06/multiarch-1/rev-multiarch-1/multiarch crackme.masm
Warning:
Cannot insert breakpoint 1.
Cannot access memory at address 0x102f5e
```

Still...

If we just run it and then break, we see a backtrace

```
>>> bt
#0  __syscall_cancel (a1=<optimized out>, a2=<optimized out>, a3=<optimized out>, a4=a4@entry=0, a5=a5@entry=0, a6=a6@entry=0, nr=0) at ./nptl/cancellation.c:77
#1  0x00007ffff7d268ee in __GI___libc_read (fd=<optimized out>, buf=<optimized out>, nbytes=<optimized out>) at ../sysdeps/unix/sysv/linux/read.c:26
#2  0x00007ffff7c992e3 in _IO_new_file_underflow (fp=0x7ffff7e118e0 <_IO_2_1_stdin_>) at ./libio/libioP.h:1041
#3  0x00007ffff7c9c1c2 in __GI__IO_default_uflow (fp=0x7ffff7e118e0 <_IO_2_1_stdin_>) at ./libio/libioP.h:1041
#4  0x00007ffff7c70ef8 in __vfscanf_internal (s=<optimized out>, format=<optimized out>, argptr=argptr@entry=0x7fffffffd770, mode_flags=mode_flags@entry=2) at ./stdio-common/vfscanf-internal.c:676
#5  0x00007ffff7c63baf in __isoc99_fscanf (stream=<optimized out>, format=<optimized out>) at ./stdio-common/isoc99_fscanf.c:30
#6  0x0000555555555824 in ?? ()
#7  0x0000555555555ebf in ?? ()
#8  0x0000555555556a12 in ?? ()
#9  0x0000555555557007 in ?? ()
#10 0x00007ffff7c2a338 in __libc_start_call_main (main=main@entry=0x555555556f5e, argc=argc@entry=2, argv=argv@entry=0x7fffffffd9e8) at ../sysdeps/nptl/libc_start_call_main.h:58
#11 0x00007ffff7c2a3fb in __libc_start_main_impl (main=0x555555556f5e, argc=2, argv=0x7fffffffd9e8, init=<optimized out>, fini=<optimized out>, rtld_fini=<optimized out>, stack_end=0x7fffffffd9d8) at ../csu/libc-start.c:360
#12 0x0000555555555205 in ?? ()
```

![[Pasted image 20250629000146.png]]

Those addresses are very different: `0x00007fff f7c992e3`. 

![[Pasted image 20250629000819.png]]

2025-06-29 Wk 26 Sun - 00:09

Ghidra seems able to correspond between the two address spaces in its debugger!

![[Pasted image 20250629001051.png]]

We're able to break at `FUN_00102f5e`! It's currently at `0x5555 55556f5e`


I need to disable that `.gdbinit` I set for now just to make sure I see output there.

Argument needs to be be absolute since I put the image elsewhere for ghidra maybe:
```
/home/lan/src/exp/puzzles/ctf/2025-06/multiarch-1/rev-multiarch-1/crackme.masm
```

`stepi` is F8, `nexti` is F10, finish is F12.

Some functions we hit

- FUN_00101574, 

```
fprintf(stderr,"[E] invalid eva, can\'t read: %#x\n",param_2 & 0xffffffff);
```

2025-06-29 Wk 26 Sun - 00:24

We need to look for the string `Challenge 1 - What's your favorite number?`

Could it be found through ghidra debugger?

Not through Ctrl+F. It should be in-memory at some point. If I step enough I should eventually get there?


So we know this happens somewhere in the invocation of

```C
do {
cVar1 = FUN_001029d1(lVar3);
} while (cVar1 != '\0');
```

In `FUN_00102f5e`. This keeps repeating, `cVar1` seems to be often `0x01`.

Let's try again, and break somewhere there and see when we encounter Challenge 1.

```C
undefined8 FUN_001029d1(undefined8 param_1)

{
  char cVar1;
  undefined8 uVar2;
  
  cVar1 = FUN_001017da();
  if (cVar1 == '\0') {
    uVar2 = FUN_00101a56(param_1);
  }
  else if (cVar1 == '\x01') {
    uVar2 = FUN_00102052(param_1);
  }
  else {
    fwrite("[E] nice qubit\n",1,0xf,stderr);
    uVar2 = 0;
  }
  return uVar2;
}
```

So in `FUN_00102052`

437 line function. It's very busy.

Wait... breaking in `FUN_00102052` and running to it made it print everything already

```
Welcome to the multiarch of madness! Let's see how well you understand it.
Challenge 1 - What's your favorite number? 
```

So let's try `FUN_00101a56` instead

5 times hitting the breakpoint, this prints:

```
Welcome to the multiarch of madness! Let's see how well you understand it.
```

9 times, we get:

```
Challenge 1 - What's your favorite number?
```

This may be running some instructions. That would be helpful to figure out, but we can also find out exactly when it prints this.

2025-06-29 Wk 26 Sun - 00:39

We can set the `Watch` `Type` to `char[10]` for variables we expect to be strings. Seems we have to reflect in the expression too, `*:10`

There is a struct here

```C
void FUN_001016ff(long param_1,undefined8 param_2)
```

Ghidra is able to auto-create struct! Just right clicking on param_1 in the decompiler.

2025-06-29 Wk 26 Sun - 01:20

```C
        case 2:
          cVar1 = FUN_00101731(param_1,&local_2c);
          if (((cVar1 != '\0') && (cVar1 = FUN_001016ff(param_1,&local_30), cVar1 != '\0')) &&
             (cVar1 = FUN_001018c5(param_1,local_2c,(undefined1)local_30), cVar1 != '\0'))
          goto LAB_00101c1d;
          param_1->field_0x30 = 1;
          break;
```

```
                             switchD_00101e7f::caseD_2                       XREF[1]:     00101e7f(j)  
        00101f02 48 8d 74        LEA        RSI=>local_2c,[RSP + 0xc]
                 24 0c
        00101f07 48 89 df        MOV        RDI,RBX
        00101f0a e8 22 f8        CALL       FUN_00101731                                     undefined FUN_00101731()
                 ff ff
        00101f0f 84 c0           TEST       AL,AL
        00101f11 75 06           JNZ        LAB_00101f19
```

This is the place where it gets printed.

```
Challenge 1 - What's your favorite number?
```


Yes! When breaking on `00101f02`, then we get the two prints on two continues only!

```
Breakpoint 1, 0x0000555555555f02 in ?? ()
Welcome to the multiarch of madness! Let's see how well you understand it.

Breakpoint 1, 0x0000555555555f02 in ?? ()
Challenge 1 - What's your favorite number? 
```

## 4.3 Try to find where it reads from stdin for Challenge 1

2025-06-29 Wk 26 Sun - 01:28

So let's try `FUN_00102052` now.

It also has many cases in a switch. Break on all of them and see what happens.

 FUN_00101a56

| case0    | case1    | case2    | case3    | case4    | case5    | case6    | default  |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
| 00101eb2 | 00101edf | 00101f02 | 00101f45 | 00101f6d | 00101f9d | 00101fb6 | 00101ffb |
 

FUN_00102052 Cases

| case0    | case1    | case2    | case3    | case4    | case5    | case6    | default  |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
| 001023c2 | 001023ea | 0010240c | 0010242e | 0010243b | 00102463 | 0010248e | 001024b9 |

Now we trace what happens, stdout and what cases in both functions are hit


| Continue (#) | Case           | Notes (On F5 again)                                                        |
| ------------ | -------------- | -------------------------------------------------------------------------- |
| 1            | 1-2 (00101f02) | Welcome to the multiarch of madness! Let's see how well you understand it. |
| 2            | 1-2 (00101f02) | Challenge 1 - What's your favorite number?                                 |
| 3            | 1-0 (00101eb2) | Program received signal SIGTTIN, Stopped (tty input).                      |
| 4            | 1-0 (00101eb2) | Program received signal SIGTTIN, Stopped (tty input).                      |
| 5            | 1-0 (00101eb2) |                                                                            |


case 1-2, 555555555f02. case 1-0, 555555555eb2

```gdb
(gdb) run crackme.masm
Starting program: /home/lan/src/exp/puzzles/ctf/2025-06/multiarch-1/rev-multiarch-1/multiarch crackme.masm
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
[I] initializing multiarch emulator
[I] executing program

Breakpoint 1, 0x0000555555555f02 in ?? ()
(gdb) c
Continuing.
Welcome to the multiarch of madness! Let's see how well you understand it.

Breakpoint 1, 0x0000555555555f02 in ?? ()
(gdb) c
Continuing.
Challenge 1 - What's your favorite number? 
Breakpoint 2, 0x0000555555555eb2 in ?? ()
(gdb) c
Continuing.
44
Seems like you have some learning to do!
[I] done!
[Inferior 1 (process 3153351) exited normally]
```

2025-06-29 Wk 26 Sun - 01:59

Let's load gdb with breakpoints at all the relevant cases

```sh
gdb ./multiarch \
	-ex "b *0x555555555edf" \
	-ex "b *0x555555555eb2" \
	-ex "b *0x555555555f02" \
	-ex "b *0x555555555f45" \
	-ex "b *0x555555555f6d" \
	-ex "b *0x555555555f9d" \
	-ex "b *0x555555555fb6" \
	-ex "b *0x555555555ffb" \
	-ex "b *0x5555555563c2" \
	-ex "b *0x5555555563ea" \
	-ex "b *0x55555555640c" \
	-ex "b *0x55555555642e" \
	-ex "b *0x55555555643b" \
	-ex "b *0x555555556463" \
	-ex "b *0x55555555648e" \
	-ex "b *0x5555555564b9" \
	-ex "run crackme.masm"
```

```sh
gdb ./multiarch \
	-ex "b *0x555555555edf" \ # case 0
	-ex "b *0x555555555eb2" \ # case 1
	-ex "b *0x555555555f02" \ # case 2
	-ex "b *0x555555555f45" \ # case 3
	-ex "b *0x555555555f6d" \ # case 4
	-ex "b *0x555555555f9d" \ # case 5
	-ex "b *0x555555555fb6" \ # case 6
	-ex "b *0x555555555ffb" \ # default
	-ex "b *0x5555555563c2" \ # case 0
	-ex "b *0x5555555563ea" \ # case 1
	-ex "b *0x55555555640c" \ # case 2
	-ex "b *0x55555555642e" \ # case 3
	-ex "b *0x55555555643b" \ # case 4
	-ex "b *0x555555556463" \ # case 5
	-ex "b *0x55555555648e" \ # case 6
	-ex "b *0x5555555564b9" \ # default
	-ex "run crackme.masm"
```

```gdb
Starting program: /home/lan/src/exp/puzzles/ctf/2025-06/multiarch-1/rev-multiarch-1/multiarch crackme.masm
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
[I] initializing multiarch emulator
[I] executing program

Breakpoint 3, 0x0000555555555f02 in ?? ()
(gdb) c
Continuing.
Welcome to the multiarch of madness! Let's see how well you understand it.

Breakpoint 3, 0x0000555555555f02 in ?? ()
(gdb) c
Continuing.
Challenge 1 - What's your favorite number? 
Breakpoint 2, 0x0000555555555eb2 in ?? ()
(gdb) c
Continuing.
55

Breakpoint 11, 0x000055555555640c in ?? ()
(gdb) c
Continuing.
Seems like you have some learning to do!
[I] done!
[Inferior 1 (process 3274387) exited normally]
```


```gdb
gdb ./multiarch

b *0x555555555f02 
b *0x555555555eb2
```
# 5 References
1. https://scrapco.de/ghidra_docs/Debugger/DebuggerWatchesPlugin/DebuggerWatchesPlugin.html ^1