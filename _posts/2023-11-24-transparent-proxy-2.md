---

layout: post

title:  "DNS Security Part 2: Is ECH saving us from MITM?"

date:   2023-11-24 14:15:58

author: severus

categories: Security

tags: privacy dns security

---

## ECH is final piece of big privacy picture.
When we connect to website, the TLS helps us with confidence, security but no privacy. The eyes of ISP still see your target server via SNI. They could terminate the connection if target is forbidden.     
ECH is new technique to save us from the eyes of ISP. It encrypts real SNI (inter SNI) when fake SNI ( outer SNI ) is public. No one could see real SNI if they don't private ech key. A request has 2 SNI, one for real connection, one for public announcement.

When I read RFC, I find that ECH will help us on intercepting, filtering the connections we connect to. But I'm wrong. The RFC is still a proposal. And I have some questions, I test it and I found that some engineers implement it blindly.

## The problem with ECH: split mode is nice but...
ECH has 2 modes: shared mode and split mode. In shared mode, backend and frontend are on same server. It doesn't introduce any risk, because a connection must verify TLS after decrypting the ECH.  
In split mode, ECH is only useful for decrypting real SNI. Therefore, if we implement transparent proxy, we would decrypt ECH with 2 conditions: we control type65  (https resource record) dns request and we modify the record content with our ech public key.

### What's transparent proxy?
Funny, nginx stream is one of transparent proxy, but some engineers said: no, it is not transparent proxy. What does make it transparent?  
Now, we talk about the transparent proxy: the connection is not modified through proxy and proxy does not terminate TLS connection. Wait, the connection is coming from proxy, not client, it's not transparent proxy, right? No, it's still transparent proxy. It likes NAT mode, with src address is modified and fallbacks to real client, the confidence is mutual agreement. If you need send client ip to target, some extra steps need to be setup. In this case, the geo bypass is broken.

### Split mode and problems  
Now, if someone intercepts type65 dns request and modifies ech and hint ip to their server, how could we prevent this?  
In split mode, the frontend verifies ECH, decrypts it and send connection to backend. It likes ECH termination, when backend must verify connection from frontend.  
DNS over HTTPS is required for ECH.

## Support for sites without supporting ECH
In previous article, if the site supports ECH, we forward the stream to target, it's so simple. If the sites don't support ECH. How could we support them?  
The RFC said that frontend would terminate the TLS connection and act as proxy. But if we use stream module, and decrypt only ECH on stream, then stream TLS to backend, it should work. The ECH, by the way, after decrypting to real SNI, it doesn't involveconnection.  
With this idea, [sftcd](https://github.com/sftcd) project makes some modifications on [openssl](https://github.com/sftcd/openssl) and [nginx](https://github.com/sftcd/nginx) to support ech termination only with stream module.  
Build with these repo, we setup new ECH split mode. The frontend decrypts only ECH, then streams the connection to target. The configuration looks like: 
```
stream {
    map $ssl_preread_server_name $targetBackend {
        foo.example.com  127.0.0.1:9444;
        hidden.infosec.xyz  157.245.205.238:443;
    }

    log_format basic '$ssl_preread_server_name '
                 '$ssl_preread_alpn_protocols '
                 '$remote_addr [$time_local] '
                 '$protocol $status $bytes_sent $bytes_received '
                 '$session_time "$upstream_addr" '
                 '"$upstream_bytes_sent" "$upstream_bytes_received"';

    access_log fe/logs/access.log basic;
    ssl_preread on;
    ssl_echkeydir /root/ech-test/openssl/esnistuff/keys;
    server {
        listen  443;
        # proxy_connect_timeout 1s;
        # proxy_timeout 3s;
        #proxy_bind $remote_addr transparent;
        proxy_pass $targetBackend;

    }
}
```

To support transparent proxy, which sends client ip to backend, setup proxy_bind with extra network configuration.

Now, as we see in this blog, the RFC said that ECH does not support transparent proxy, it's totally wrong. 
Real engineer implements something with RFC referrence, but RFC is changing day by day. We use it and improve it. Don't implement it blindly, use it with your mindset. We're engineers, we changes everything what we want. We hack it for better world.
