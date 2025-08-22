---
has_goal: '[[000 First prototype of a publicly hosted obsidian-sourced website]]'
status: done
---

# 1 Objective

I need to have a domain name that I can configure for a self-hosted website to have a nice URL.

* [x] Purchased domain name
  Purchased www.deltatraced.com!

* [x] Hosted a website using it
  This is done! www.deltatraced.com  can be deployed via [wasmer](https://wasmer.io/)!

This task is part of the goal [000 First prototype of a publicly hosted obsidian-sourced website](../goals/2025/000%20First%20prototype%20of%20a%20publicly%20hosted%20obsidian-sourced%20website.md).

See also:

* [000 On Search Engine Optimization](../entries/2025/000%20On%20Search%20Engine%20Optimization.md)
* [001 Searching for services with free website hosting](../entries/2025/001%20Searching%20for%20services%20with%20free%20website%20hosting.md)
* [002 Learning about Astro and website deployment](../entries/2025/002%20Learning%20about%20Astro%20and%20website%20deployment.md)

# 2 Journal

2025-08-12 Wk 33 Tue - 21:18

To deploy changes to wasmer based on changes the first time,

````sh
curl https://get.wasmer.io -sSfL | sh
npm install
npm run build
wasmer deploy
````

This CLI tool immediately authenticates you via a web browser link if you're logged in already which is very cool.

From [wasmer docs cli](https://docs.wasmer.io/edge/cli),

Use this  to delete a deployed app:

````sh
wasmer app delete
````

But then you'd have to create a new app:

````sh
wasmer app create
````

But we have a website hosted on our own cool domain name now! Objective complete!

www.deltatraced.com

![Pasted image 20250813071716.png](../../../../../attachments/Pasted%20image%2020250813071716.png)

# 3 Tasks

# 4 Issues

# 5 HowTos

# 6 Investigations

## 6.1 On Domains and DDNS

2025-08-12 Wk 33 Tue - 17:08

From [hostinger domain name tutorial](https://www.hostinger.com/tutorials/what-is-a-domain-name),

Something like www.google.com is a domain name. It's broken into 3 levels:

* Top-Level Domain (TLD)

In our case, `www.google.com` -> `.com`.

It's also referred to as the domain name extension.

`.com` is very popular. These can have regulatory meanings like `.org`, `.uk`, `.gov`...

You can find a list of different top level domains in [wikipedia](https://en.wikipedia.org/wiki/List_of_Internet_top-level_domains) -> [iana.org domain db](https://www.iana.org/domains/root/db).

* Second-Level Domain (SLD)

In our case, `www.google.com` -> `google`.

* Third-Level Domain (subdomain)

This is the `www` in `www.google.com`, but it also could be the `howtos` in `howtos.mywebsite.com`

2025-08-12 Wk 33 Tue - 17:16

A Domain Name System (DNS) server needs to convert URLs like `www.google.com` into specific IP addresses for routing. A request  is made to a DNS server to convert a domain name to an IP address.

We can look up information on domain names using [ICANN lookup](https://lookup.icann.org/en/lookup).

2025-08-12 Wk 33 Tue - 17:30

Let's say we have a website with the URL `https://howtos.mywebsite.com/howtos/1234` to load a specific `HowTo` number 1234. The domain name part of this is `howtos.mywebsite.com`. It's using the HTTPS protocol, and has the page path `/howtos/1234`.

2025-08-12 Wk 33 Tue - 17:35

There are services that allow us to buy domain names like [hostinger](https://www.hostinger.com/domain-name-search).

2025-08-12 Wk 33 Tue - 17:36

So now we know that a DNS is a domain name server that is able to fetch domain names. So what's DDNS?

From [AWS post on dynamic dns](https://aws.amazon.com/what-is/dynamic-dns/),

so that stands for Dynamics DNS (DDNS).

A DDNS allows for dynamic rouiting of IP addresses to the same domain name.

This mentions DHCP use of reassignment of IP addresses to devices on a network, which could lead to issues with static DNS that expects static IPs.

# 7 Ideas

# 8 Side Notes

## 8.1 Firefox styling of domain name

![Pasted image 20250813064928.png](../../../../../attachments/Pasted%20image%2020250813064928.png)

It's interesting `www.` is shaded differently, I guess to signify it's a less important part of the domain name maybe? It can be omitted and inferred automatically as a subdomain

# 9 External Links

# 10 References
