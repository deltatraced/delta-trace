---
parent: "[[002 Move credit_store_demo project to deltachives]]"
spawned_by: "[[000 Modularize shi shell use in credit store demo]]"
context_type: issue
status: todo
---

Parent: [[002 Move credit_store_demo project to deltachives]]

Spawned by: [[000 Modularize shi shell use in credit store demo]] 

Spawned in: [[000 Modularize shi shell use in credit store demo#^spawn-issue-229e4a|^spawn-issue-229e4a]]

# 1 Journal

So I enabled trace logs:

```rust
drivers::logging::init_logging_with_level(log::LevelFilter::Trace);
```

But now I'm getting many debug logs while typing each character from rustyline:

```
a[2025-09-19 04:16:18 DEBUG /home/lan/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/rustyline-7.1.0/src/tty/unix.rs:649] key: KeyEvent(Char('b'), NONE)
[2025-09-19 04:16:18 DEBUG /home/lan/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/rustyline-7.1.0/src/keymap.rs:584] Emacs command: SelfInsert(1, 'b')
[2025-09-19 04:16:18 DEBUG /home/lan/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/rustyline-7.1.0/src/undo.rs:151] Changeset::insert(1, 'b')
b[2025-09-19 04:16:19 DEBUG /home/lan/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/rustyline-7.1.0/src/tty/unix.rs:649] key: KeyEvent(Char('c'), NONE)
[2025-09-19 04:16:19 DEBUG /home/lan/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/rustyline-7.1.0/src/keymap.rs:584] Emacs command: SelfInsert(1, 'c')
[2025-09-19 04:16:19 DEBUG /home/lan/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/rustyline-7.1.0/src/undo.rs:151] Changeset::insert(2, 'c')
c
```

2025-09-19 Wk 38 Fri - 04:31 +03:00

Spawn [[000 Can eliminate all logging calls with cargo feature]] ^spawn-idea-732f18

2025-09-19 Wk 38 Fri - 04:42 +03:00

This [rust forum post](https://users.rust-lang.org/t/how-to-exclude-a-library-log-in-tracing-subsciber/121106/2) discusses that but for a [docs.rs tracing_subscriber](https://docs.rs/tracing-subscriber/latest/tracing_subscriber/index.html) crate.

And this [reddit post](https://www.reddit.com/r/rust/comments/85qp50/how_to_disable_logging_for_certain_crates/) has discussion on using various other logging crates to solve the issue... Like [gh rust-cli/env_logger](https://github.com/rust-cli/env_logger) which can be configured via environment variables.

2025-09-19 Wk 38 Fr - 05:15 +03:00i

Should this library even be emitting these debug messages? I filed an issue [gh Utagai/shi #11](https://github.com/Utagai/shi/issues/11) noting the old versions of [rustyline](https://github.com/kkawakam/rustyline).

Let's attempt to upgrade. This involves breaking change.

See [[000 Attempting to upgrade rustyline for shi]]
