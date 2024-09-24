---

layout: post

title:  "[2023] New DNS Service announcement"

date:   2023-04-05 21:45:58

author: severus

categories: Security

tags: dns privacy security

---

In late March, we migrated from nginx to knot-resolver. Because of some problems with nginx, user's switching network from LTE to wifi and vice versa cannot keep connection to dns server over tls.

With knot-resolver, we use Adguard's DoT as backend, we plan to use other to backup soon.

## Change

We donot use dnscrypt as brigde anymore. We use knot-resolver's DoT as client to connect backend.

Because we cannot run DoH on same port with nginx, we change DoH's port to 4443. New address should be https://dns.infosec.xyz:4443/dns-query and boostrap address is 157.245.205.238/2400:6180:0:d1::873:7001

