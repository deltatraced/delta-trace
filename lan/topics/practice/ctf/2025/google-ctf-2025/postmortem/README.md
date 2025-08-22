# 1 What is this?

This is a google event running Jun 2025.

See The [beginners-quest](https://capturetheflag.withgoogle.com/beginners-quest).

And the competition event: [capture the flag](https://capturetheflag.withgoogle.com/).

# 2 Journal (Postmortem)

## 2.1 What happened?

2025-06-29 Wk 26 Sun - 21:37

Welcome to postmortem!

This time, we didn't get any flags. I think we got close. I attempted basically two challenges:

* [MULTIARCH-1](https://github.com/delta-domain-rnd/delta-trace/blob/main/by%20topic/practice/ctf/2025/by%20topic/reversing/entries/000%20Multiarch.md)
* [NUMEROLOGY](https://github.com/delta-domain-rnd/delta-trace/blob/main/by%20topic/practice/ctf/2025/by%20topic/crypto/entries/003%20NUMEROLOGY.md)

Both I have made progess in, more so NUMEROLOGY.

I could not put as much time and focus as I would like. And my one teammate was mostly unavailable. Hopefully next time I can do this in a more coordinated fashion.

But this is also my first CTF contest. I had an objective, to get one flag. I did succeed. However, I have this journal as my trail to learn and share. And now, I can still look for flags in postmortem!

If you want the data folder to reproduce my work before postmortem, feel free to request.

Now all changes will be done in `postmordem/`. The other files will be frozen, unless they have to be edited for other administrative purposes.

# 3 Issues

## 3.1 Fix git history for adding big attachment

2025-06-29 Wk 26 Sun - 21:51

I attempted to archive my data folder and post it here.

````sh
tar -czvf ctf-2025-06.tar.gz -C 2025-06/ .
nautilus .
````

But it is large (Around 300 MB).

So now I have to delete that from the git history!

````
![[ctf-2025-06.tar.gz]]
````

````sh
sudo apt install git-filter-repo

````

The tool tells us we need to operate on a fresh clone. But we are not remote... For precaution, let's just copy the repo elsewhere.

````sh
mkdir -p /tmp/delete
cd /tmp/delete
cp -r ~/src/cloned/gh/delta-domain-rnd/delta-trace .
cd delta-trace

git filter-repo --path attachments/ctf-2025-06.tar.gz --invert-paths --force

# in /home/lan/src/cloned/gh/delta-domain-rnd/
mkdir -p ~/data/backups/2025-06-delta-trace
mv delta-trace ~/data/backups/2025-06-delta-trace
git clone git@github.com:delta-domain-rnd/delta-trace.git
````

`ctf-2025-06.tar.gz` has been removed. We can leave the broken link as a trace for now.

OK
