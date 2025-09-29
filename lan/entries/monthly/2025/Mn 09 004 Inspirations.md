
[[Mn 09 September]]

# 1 Purpose

Things that have inspired me throughout the month deserve to be highlighted and captured!

# 2 Inspirations

## 2.1 Inspiring CLI Graphics

(1)

2025-09-05 Wk 36 Fri - 12:28

![[Pasted image 20250905122827.png]]

oh-my-zsh asks to update everytime on its own to keep the users up to date. The changelog is organized and disciplined, and well formatted here that it reads easily. They have features and bug fixes separated, and they have the modules detailed, and they link to specific issue IDs for everything which means we can inspect where exactly in the code the changes were made, or what the proposal/motivation was.

They also have social information included, and a way to view the entire changelog from the app itself with `omz changelog`. If you say no to updating Oh My Zsh, it reminds you you can always update yourself with `omz update`.

This is a cool way to keep users engaged and up to date! And the logo is simply and colorful!

You can learn more about it in [ohmyz.sh](https://ohmyz.sh/).

# 3 Stream

(2)

2025-09-23 Wk 39 Tue - 00:15 +03:00

Found another developer who also likes to put timesheets publicly! [Bryon timesheets 2025](https://github.com/Byron/byron/blob/main/timesheets/2025.csv). The project, client, and purpose are clear. Can be an inspiration for what I do!

This inspired me to add a focus column to overview to give a key highlight, just like a git commit for the day!

(3)

2025-09-23 Wk 39 Tue - 01:06 +03:00

We learned during contributing to [gh Utagai/shi](https://github.com/Utagai/shi) about [docs.rs thiserror](https://docs.rs/thiserror/latest/thiserror/) and the ability to combine error messages and codes in one enum. Also its use of `#[from]` shortened many functions that would otherwise be mapping from one error type to another all the time. If a function can output an Io error, then it may have its own error type with `Io` variant, and using `#[from]` conversion happens automatically with `?`!