---

layout: post

title:  "Certificate Transparency and Mobile Security"

date:   2024-03-21 10:45:58 +0700

author: severus

categories: Mobile

tags: certificate transparency mobile 

---

## Introduce about Certificate Transparency
Web Public Key Infrastructure (Web PKI) helps us on privacy and security. Trusted Certificate Authorities issue certificates and we trust them by default. Although they must follow policy before they issue any cetificate,  they can still issue the certificate for wrong usage as: impersonate website or man in the middle.   

Cetificate transparency allows us to monitor issued certificates and inspect them, detect any misissued certificate. Because it only appends logs, we can use this for many purposes: track history of certificates, detect new domain in real time, terminate insecure connection,…

## Mobile security and Certificate Transparency
Purpose: replacement of SSL Pinning.

SSL Pinning is good choice, but when we replace the certificate, we must rebuild our application. Dynamic SSL Pinning is another choice to resolve this issue, but we need to maintain new PKI infra. In deep dive, dynamic ssl pinning embedded 1 static hash and pulls new hash, after that, it binds new hash to hash array.

The mobile app is limited version of web browser, then many features like OCSP, Certificate transparency are missing. Banking/Finance applications must check enviroment before running, and when it's patched to fake safe environment, we need to prevent man in the middle between our application and api server. But how do we make it transparency without SSL Pinning? 

In previous blog, I showed that JA3 can help us on our purpose. But it needs to rework TLS handshake algo. We have another choice, better implementation: Certificate Transparency.

When application initializes connection, after it received certificates from server, it needs to check Signed Certificate Timestamp (SCT). Google Chrome only allows certificate with atleast 2 SCT, mobile applications should follow this rule.

By default, all new certificates will include 2 SCT in their attribute.

With custom CA and self signed certificates, they doesn't contain those SCTs. Therefore, it needs more step to break this protection.

 


