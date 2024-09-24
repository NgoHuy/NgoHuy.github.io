---


title:  "DNS Security: Part 1 which can we do if we manage the dns?"

date:   2023-07-30 21:45:58

author: severus

categories: security

tags: privacy dns security

---

If you think dns only resolved the records, think it again. In IoT's world, it involved more than a resolver.

## The question
When the DNS server is controlled by someone, what can they do with our traffic?

## The problem
With the resolver, we can redirect user to any ip which hosted our application. And thanks to PKI infrastructure, if nothing goes wrong, no one can intercept our traffic.

But we need to redirect user to somewhere without modifying their traffic for privacy, even if they have TLS connection and ECH enabled traffic, what should we do?

## The idea
We know that layer 4 proxy is streaming from user to target server without modification. But layer 4 does not know how to handshake. The handshake must be initiated in application's side. Then if we control DNS we can point user to our proxy layer 4, and completely hide user from original place or bypass GEO blocking (also bypass SNI filter with ECH). This is the way ControlD supports users with only DNS.

But wait, why dont we use HTTP CONNECT?  
Because almost servers block CONNECT method for security purpose.

## The implement
I demonstrate with nginx stream configuration.

```
server {
        resolver 127.0.0.1;
        listen 443;
        ssl_preread on;
        proxy_connect_timeout 5s;
        proxy_pass ip:port;
    }
```

when user resolved ip to our layer 4 proxy the connection should be initiated from client side and forward to our proxy, then our proxy will pass it to backend. The hanshake initiated without modification.

## The limitation
If some servers which do not support ECH, we need to fallback to traditional plain SNI handshake.  
If backend servers block layer 4 proxy (almost because of heavy traffic), we need an efficient way to connect them rather than changing the proxy's ip.

## Next post
I will explain the handshake of TLS and some idea with HTTPS  Resource Record can abuse them.
