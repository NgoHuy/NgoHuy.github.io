---

layout: post

title:  "New DNS Service announcement"

date:   2019-09-19 15:45:58

author: severus

categories: Security

tags: dns privacy security

---

[Updated on 8/12/2020] We migrated from dnsdist to nginx for perfomance.

DNS is one of important types on internet. It translated domain strings to ip address before application established the server. DNS based on udp and tcp protocol.

## Traditional DNS problems
With old type of DNS server, user and server have no way to check if the request or response was modified, it's easy to sniff, modify and see by some nodes when it was routed to.

## Encryption for security and privacy
To solve the dns's problems, [dns over tls (DoT)](https://tools.ietf.org/html/rfc7858), [dns over https (DoH)](https://tools.ietf.org/html/rfc8484) are built in standard of [ietf](https://ietf.org/). [Dnscrypt](https://dnscrypt.info/) was new protocol written by [Frank Denis](https://github.com/jedisct1) some years ago.

## dns.infosec.xyz
We supported 3 types of dns: dns over tls on standard port 853, dns over https on port 443 and normal dns service on port 53.

Because we're fans of opensource. We would like to open our architecture for trust and privacy.

We built our service on [nginx](https://nginx.org) for frontend and [dnscrypt-proxy](https://github.com/DNSCrypt/dnscrypt-proxy) for backend.

Requests from user should be encrypted with DoH or DoT, and our server will make dns request to provider via dnscrypt-proxy.

`Users <====> Our DNS server <====> Google/Cloudflare/Quad`

We don't log any dns query except blocked domain in our filter. The filter is published on our [main site](https://infosec.xyz/domains-blacklist.conf). We only filter ads/malware and tracking sites.

If you have any problem with your site, ping us on `telegram:severusvn` or `telegram:hoangdoanvss`
