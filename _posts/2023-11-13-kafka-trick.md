---

layout: post

title:  "Retrieve kafka stream from unavailable place."

date:   2023-11-13 10:45:58 +0700

author: severus

categories: Security

tags: kafka configuration security

---
## Problem
I discovered some Kafka brokers. And when I consume topic, I get the error: `failed to get offset for topic pushgatway_send Partition 3: dial tcp 172.18.19.58:9092: i/o timeout` or `failed to get offset for topic __consumer_offsets Partition 17: dial tcp: lookup broker on 127.0.0.1:53: no such host`.
How can we retrieve kafka stream?

## Again, dns and network
Kafka broker handles all requests from clients to read and write events. The topic may be on different partition.
These above errors show that `no dns and no route to internal broker`.

Now, as network engineer, what's in your mind?  
DNS is alias of ip address. We remap DNS to exact ip show `no such host`.

How's about i/o timeout? redirect the traffic to this ip with your state full firewall. Because broker may have multiple network interfaces, and it exposed 1 interface, redirect from public interface then it should redirect to internal interface.
```bash
iptables -t nat -A POSTROUTING -p tcp --dport 9092 -j MASQUERADE
iptables -t nat -A OUTPUT -p tcp --dport 9092 -j DNAT --to-destination $kafka_public_ip:$port
```

This code should work, except the host is `127.*.*.*`. Kernel prevents reroute localnet by default. Then we need other trick
```bash
sysctl -w net.ipv4.conf.all.route_localnet=1
```
Then if the partition is on broker, data should be retrieved.  
