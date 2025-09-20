
[[Mn 09 September]]
# 1 Purpose

Capturing highlights of practices and lessons learned this month!

# 2 Entries

## 2.1 Streamlined environment variables in Rust

(1)

2025-09-05 Wk 36 Fri - 13:48

While going through the [diesel getting-started](https://diesel.rs/guides/getting-started) I learned about [dotenvy](https://github.com/allan2/dotenvy) for managing environmental variables in Rust.

We can create an `.env`  file, which is just a collection of key-value pairs, and its content will be treated as environmental variables within Rust when we run

```rust
use dotenvy::dotenv;
use std::env;

// [...]

dotenv().ok();

let my_env_var =  env::var("MY_ENV_VAR").expect("MY_ENV_VAR must be set");
```

We just need the dependency in `Cargo.toml`:

```sh
cargo add dotenvy
```

This helps keep environmental variables local to the project, but also give the user choice in how they are specified. Since it just uses `env::var`, the user has the choice to pass environmental variables explicitly themselves, or override defaults from the `.env` file.

# 3 Stream

(2)

2025-09-19 Wk 38 Fri - 09:56 +03:00

From [[001 Getting many debugging logs from rustyline while using shi#^learning-6c9d3b]],

We actually can do

```rust
#[error("Got ShiError: {0:?}")]
ShiError(#[from] ShiError),
```

And then we can do `?` on a `ShiError` directly! No need to map in the case it's a one to one correspondence.