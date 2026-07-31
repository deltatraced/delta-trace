---
context_type: entry
---

Parent: [[lan/2026/main/task/001 Install a new Gentoo system/001 Install a new Gentoo system]]

Spawned by: [[lan/2026/main/task/001 Install a new Gentoo system/001 Install a new Gentoo system]]

Spawned in: [[lan/2026/main/task/001 Install a new Gentoo system/001 Install a new Gentoo system#^spawn-entry-232dbc|^spawn-entry-232dbc]]

# Journal

2026-07-27 Wk 31 Mon - 09:01 +03:00

Link obsidian over to `/usr/local/bin`:

```sh
# in /home/lan/data/releases/gh/obsidianmd/obsidian-releases/obsidian-1.12.7
su
ln -s /home/lan/data/releases/gh/obsidianmd/obsidian-releases/obsidian-1.12.7/obsidian /usr/local/bin
```

If you mess up like I did and do `ln -s obsidian /usr/local/bin` and do ls on that it is in red bg and white/gray flashing like a siren that it's a bad link!

But now we can just do win+d obsidian.

2026-07-27 Wk 31 Mon - 10:40 +03:00

https://wiki.gentoo.org/wiki/Discord

```sh
# in /etc/portage/package.accept_keywords/net-im/discord {
	net-im/discord ~amd64
# }

# in /etc/portage/package.license/net-im/discord {
	net-im/discord all-rights-reserved
# }

su
emerge --ask net-im/discord
```

OK

2026-07-28 Wk 31 Tue - 06:23 +03:00

Installing `ripgrep`,

```sh
cargo install ripgrep
```

We need `/home/lan/.cargo/bin` in `$PATH`.

```sh
# in /home/lan/.bashrc
source $HOME/.path

# in /home/lan/.path
PATH=$PATH:/home/lan/.cargo/bin
```

before/after starting a new terminal pane:

```diff
echo $PATH

# out
-/usr/local/sbin:/usr/local/bin:/usr/bin:/opt/bin:/usr/lib/llvm/22/bin
+/usr/local/sbin:/usr/local/bin:/usr/bin:/opt/bin:/usr/lib/llvm/22/bin:/home/lan/.cargo/bin
```

```sh
rg --version

# out
ripgrep 15.2.0

features:-pcre2
simd(compile):+SSE2,-SSSE3,-AVX2
simd(runtime):+SSE2,+SSSE3,+AVX2

PCRE2 is not available in this build of ripgrep.
```

OK

2026-07-28 Wk 31 Tue - 22:48 +03:00

Installing `tree`,

https://packages.gentoo.org/packages/app-text/tree

```sh
su
emerge --ask app-text/tree
```

```
2026-07-28 22:50:03 (165 KB/s) - ‘/var/cache/distfiles/unix-tree-2.2.1.tar.bz2.__download__’ saved [56345/56345]

 * unix-tree-2.2.1.tar.bz2 BLAKE2B SHA512 size ;-) ...                                                                                                                                                                                   [ ok ]
```

Interesting wink.

OK

2026-07-30 Wk 31 Thu - 09:34 +03:00

Installing `mgba`,

https://packages.gentoo.org/packages/games-emulation/mgba

```sh
equery uses games-emulation/mgba

# out
[ Legend : U - final flag setting for installation]
[        : I - package is installed with flag     ]
[ Colors : set, unset                             ]
 * Found these USE flags for games-emulation/mgba-0.11.0_pre20260101:
 U I
 - - debug                    : Enable extra debug codepaths, like asserts and extra output. If you want to get meaningful backtraces see https://wiki.gentoo.org/wiki/Project:Quality_Assurance/Backtraces
 - - discord                  : Enable Discord RPC support
 - - elf                      : Enable the use of elf utils via dev-libs/elfutils
 - - ffmpeg                   : Enable ffmpeg/libav-based audio/video codec support
 - - gles2                    : Enable GLES 2.0 (OpenGL for Embedded Systems) support (independently of full OpenGL, see also: gles2-only)
 - - gles3                    : Build OpenGL ES 3.x RenderSystem
 + + gui                      : Enable support for a graphical user interface
 - - libretro                 : Build libretro port
 - - lua                      : Enable Lua scripting support
 - - lua_single_target_lua5-3 : Build for Lua 5.3 only
 - - lua_single_target_lua5-4 : Build for Lua 5.4 only
 + + opengl                   : Add support for OpenGL (3D graphics)
 + + sdl                      : Add support for Simple Direct Layer (media library)
 + - sqlite                   : Add support for sqlite - embedded sql database
 - - test                     : Enable dependencies and/or preparations necessary to run tests (usually controlled by FEATURES=test but can be toggled independently)
```

```
The above constraints are a subset of the following complete expression:
    gui? ( any-of ( gles2 gles3 opengl ) sqlite gles3? ( any-of ( gles2 opengl ) ) ) lua? ( exactly-one-of ( lua_single_target_lua5-3 lua_single_target_lua5-4 ) )
```

```sh
# in /etc/portage/package.use/games-emulation/mgba {
	games-emulation/mgba elf debug lua lua_single_target_lua5-4
	
	# required by virtual/minizip-1.3.1::gentoo[-static-libs]
	# required by games-emulation/mgba-0.11.0_pre20260101::gentoo
	# required by games-emulation/mgba (argument)
	>=sys-libs/zlib-1.3.2-r1 minizip
# }

su
emerge --ask games-emulation/mgba
```

OK

2026-07-30 Wk 31 Thu - 16:56 +03:00

Installing gdb,

https://wiki.gentoo.org/wiki/GDB

```sh
equery uses dev-debug/gdb

# out
[ Legend : U - final flag setting for installation]
[        : I - package is installed with flag     ]
[ Colors : set, unset                             ]
 * Found these USE flags for dev-debug/gdb-17.2:
 U I
 - - babeltrace                      : Enable dev-util/babeltrace support
 + + cet                             : Enable Intel Control-flow Enforcement Technology.
 + - debuginfod                      : Enable debuginfod support via dev-libs/elfutils libdebuginfod
 - - guile                           : Add support for the guile Scheme interpreter
 - - guile_single_target_2-2         : Build only for GNU Guile 2.2.
 + + guile_single_target_3-0         : Build only for GNU Guile 3.0.
 - - lzma                            : Support lzma compression in ELF debug info
 - - multitarget                     : Support all known targets in one gdb binary
 + + nls                             : Add Native Language Support (using gettext - GNU locale utilities)
 + - python                          : Enable support for the new internal scripting language, as well as extended pretty printers
 - - python_single_target_python3_12 : Build for Python 3.12 only
 - - python_single_target_python3_13 : Build for Python 3.13 only
 + + python_single_target_python3_14 : Build for Python 3.14 only
 - - rocm                            : Enable support for AMD GPU debugging via dev-libs/rocdbgapi
 + - server                          : Install the "gdbserver" program (useful for embedded/remote targets)
 - - sim                             : Build gdb's simulators for various hardware platforms. See https://sourceware.org/gdb/wiki/Sim.
 - - source-highlight                : Enable listing highlighting via dev-util/source-highlight
 - - test                            : Enable dependencies and/or preparations necessary to run tests (usually controlled by FEATURES=test but can be toggled independently)
 - - vanilla                         : Do not add extra patches which change default behaviour; DO NOT USE THIS ON A GLOBAL SCALE as the severity of the meaning changes drastically
 + + xml                             : Support parsing XML data files needed (at least) for cpu features, memory maps, and syscall tracing
 - - xxhash                          : Use dev-libs/xxhash to speed up internal hashing.
 - - zstd                            : Enable support for ZSTD compression
```

```sh
# in /etc/portage/package.use/dev-debug/gdb {
	dev-debug/gdb multitarget
# }

su
emerge --ask dev-debug/gdb
```

OK

2026-07-30 Wk 31 Thu - 17:37 +03:00

Installing ctags,

https://packages.gentoo.org/packages/dev-util/ctags

```sh
su
emerge --ask dev-util/ctags
```
