

# 1 Journal

## 1.1 What happened?

2025-06-29 Wk 26 Sun - 21:37

Welcome to postmortem!

This time, we didn't get any flags. I think we got close. I attempted basically two challenges: 
- NUMEROLOGY
- MULTIARCH-1

Both I have made progess in, more so NUMEROLOGY. 

I could not put as much time and focus as I would like. And my one teammate was mostly unavailable. Hopefully next time I can do this in a more coordinated fashion.

But this is also my first CTF contest. I had an objective, to get one flag. I did succeed. However, I have this journal as my trail to learn and share. And now, I can still look for flags in postmortem!

If you want the data folder to reproduce my work before postmortem, feel free to request.


# 2 Issues

## 2.1 Fix git history for adding big attachment

2025-06-29 Wk 26 Sun - 21:51

I attempted to archive my data folder and post it here. 

```sh
tar -czvf ctf-2025-06.tar.gz -C 2025-06/ .
nautilus .
```

But it is large (Around 300 MB).

So now I have to delete that from the git history!

![[ctf-2025-06.tar.gz]]


```sh
sudo apt install git-filter-repo

```

The tool tells us we need to operate on a fresh clone. But we are not remote... For precaution, let's just copy the repo elsewhere.

```
mkdir -p /tmp/delete
cd /tmp/delete
cp -r ~/src/cloned/gh/delta-domain-rnd/delta-trace .
cd delta-trace

git filter-repo --path attachments/ctf-2025-06.tar.gz --invert-paths
```
