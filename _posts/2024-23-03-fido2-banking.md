---

layout: post

title:  "Fido2 and banking use cases"

date:   2024-03-24 10:45:58 +0700

author: severus

categories: Security Privacy

tags: fido2 security banking signing

---

## Fido2 is not only passwordless authentication, but also it can sign anything you need.
Nowadays, we know Fido2 as passwordless authentication - which initializes authorized session. But in cryptography world, it's useful for different purposes.

In my previous post, we know how fido2 works and its concept. If we see the signing method in a different way, we can use it for advanced authorization, and replace OTP - which something you know of.

## Fido2 and banking domain, how does it protect users?
If you use fido2 as passwordless authentication, how do you know that only authorized users connect to your system?  
In my previous post, again, when you implement fido2 on client side, you must define the way user uses platform authenticator or security key: user verification instead of user presence. It leads to choosing method of user verification.  
PIN and Password are something you know, only biometric is something you have. Then implementation must define biometric as primary of user verification.  
CTAP 2.1 supports enterprise assertion, bank will know the key they manage and deliver to user as RSA token before.  
For bank with custom implementation of Fido2 in their applications, they must allow biometric user verification only.  
State of Bank Vietnam released new document about authentication method included fido2 when making transaction. It's widely adopted now.  

## Fido2: the replacement of OTP
When customer uses OTP as their verification method, OTP is reused somewhere, and that's why malwares on Android or IOS need to read OTP code from sms or smart otp application.  
You need to remember that: what you know is what you reuse somewhere.  
But someone said that: OTP is something you have. No, it's totally wrong. OTP belongs to the OTP system, not you. You are revealed to know and send it back to server like other secrets: password. That's why it's named: one time password/pin.  

Based on fido2's implementation, when user makes transaction, the challenge is sent to customer, and they prove that they have private key, and sign this challenge for transaction - it likes advanced OTP when user received the challenge, it's combined with static secret key and generate 6-8 OTP number but in fido2, user never knows the signed message and cannot reuse it somewhere, thanks to signature counter parameter.  

## Conclusion
When State of Bank Vietnam adopts fido2 as standard, developer should know how to implement it correctly. Modern secure system bases on something users have, behaviour detection, so on...You should get rid of password in the future.   
 
