---
context_type: entry
---

Parent: [[lan/2026/main/task/001 Install a new Gentoo system/001 Install a new Gentoo system]]

Spawned by: [[lan/2026/main/task/001 Install a new Gentoo system/entry/002 Quick new Installs for Gentoo System]]

Spawned in: [[lan/2026/main/task/001 Install a new Gentoo system/entry/002 Quick new Installs for Gentoo System#^spawn-entry-72fffc|^spawn-entry-72fffc]]

# Journal

2026-08-06 Wk 32 Thu - 18:26 +03:00

https://wiki.gentoo.org/wiki/Sway#Switching_Keyboard_Layouts

https://wiki.gentoo.org/wiki/Keyboard_layout_switching

https://wiki.gentoo.org/wiki/Localization/Guide

Find codes with `find /usr/share/keymaps/i386/ | grep 'fr-'`

```sh
# in /home/lan/src/cloned/cb/lan22h/dotfiles/etc/sway/config {
	input type:keyboard {
		xkb_layout "us,ar"
		xkb_options "grp:alt_shift_toggle"
	}
# }
```