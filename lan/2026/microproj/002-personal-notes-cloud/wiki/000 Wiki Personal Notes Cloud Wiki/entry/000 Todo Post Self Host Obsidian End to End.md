---
context_type: entry
---

Parent: [[000 Wiki Personal Notes Cloud Wiki]]

Spawned by: [[000 Wiki Proc Personal Notes Cloud]]

Spawned in: [[000 Wiki Proc Personal Notes Cloud#^spawn-entry-568fa0|^spawn-entry-568fa0]]

---

# Why not Obsidian Sync?

As of this writing, obsidian sync's $4/mo plan only gives us 1GB of storage, and it seems service limited. We can do better. If you wish to support the obsidian team for their free note taking app, you can donate to them directly.


# Obtain a Server

First, check if you are able to obtain a static IP via your ISP, or get a `$5/mo` VPS like https://www.ionos.com/servers/vps (recommended for the 90GB storage in it. See notes for some specs: [[000 PNC What services can we make use of?]])

The following assumes an ubuntu 26.04 instance:

```sh
$ lsb_release -a

No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 26.04 LTS
Release:        26.04
Codename:       resolute
```

# Configure syncthing

Follow similar process to

1. [[001 Setup port forwarding with iptables  vps provider]]
2. [[000 Setup private file sync with synththing]]

Once syncthing is accessible through your VPS you can use SSH tunneling to open the config UI in your host via something like

```sh
ssh -L 8001:localhost:8384 user@mysite.com
```

Then whether it's your phone or host, both can be configured by adding a device and your target folder where your obsidian vault is. You can also set the untrusted option and use a password so they are encrypted on the VPS. Since the VPS is planned to be the central device that would share between your hosts across networks, then you will have to decrypt on those trusted peripheral devices.

This is a general method to share files in a self-hosted manner, and since obsidian is just markdown files, it should work with it as well.

The password thing seems to be a one-time done as you add a folder to a trusted device to decrypt encrypted data from an untrusted device so it's not so bad.