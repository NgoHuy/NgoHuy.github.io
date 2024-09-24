---

layout: post

title:  "Google BeyondCorp Walkthrough"

date:   2023-10-09 10:45:58 +0700

author: severus

categories: Security

tags: google IAP security

---

## ZeroTrust is just a access context management
Some years ago, I heard that zerotrust would become the future of security. Nowaday, I see zerotrust in every security product advertisment.
From my perspective, the architecture of zerotrust is just combining multiple way to identify the user at specific step. It reduces the risk by: defining the context: where user is coming from, which should be validated from request, privilege should be granted on this context or not...

It is context access managament, for simply understanding.

## BeyondCorp and the incomplete trust.
BeyondCorp Enterprise (BCE) provides the Identity-Aware Proxy (IAP) and Access Context Manager (ACM). The IAP replies on ACM to validate user before accessing the resoures.

### The design of architecture
The BCE is powered by Chrome Browser to delivery the policy, enforce workflows.

Let digging it in deep:  
Chrome has extension: Endpoint Verification and Chrome's sync is builtin. 
When user logged in Chrome's sync, a set of policies are pushed to user from ACM included: disable proxy on chrome, some enforcements,...
Enpoint Verification has run independently of Chrome's sync.

### Nothing is connected with each other
User must sign in chrome's sync to obtain the policy. Endpoint verification doesnot use chrome's sync. Then no policy is distributed to users. It leads to all problems below.

#### Cloning Device with Endpoint Verification
When admin uploads device's infomation to ACM, the data is static. Microsoft Intune is example here - will collect and validate the compliance  of device. Google syncs device's status and sends to ACM.

Endpoint verification collects: encrypted disk status, mac address, serial number, device model, os version and sends to Google. It will compare with existed device's information then assign context to a resource.

ACM has 3 levels, when you impersonate device's infomation, no difference between them.

#### Endpoint verification can work without Chrome's sync
The flow to avoid syncing policies:  
users installed extension on chromium based browsers => login to extension not chrome's sync feature => wait for device's info synced completely => IAP validates submission then decides access context.

Look at http requests, I found 2 endpoints allowing me to impersonate device with those values above. 

This [enroll endpoint](https://secureconnect-pa.clients6.google.com/v1:enrollDevice?key=AIzaSyAJhvxfD777039sTg-pLourELbcDu0nDc4) allows user to register device.

This [reportDeviceState](https://secureconnect-pa.clients6.google.com/v1:reportDeviceState?key=AIzaSyAJhvxfD777039sTg-pLourELbcDu0nDc4) report device's infomation per 15 minutes.

The body is encrypted with key which is rotated every month.

The magic here, when obtain the valid body request, anyone of corporation can register as owner this device and allowed to access internal resources.

#### Start connection is just OpenVPN

Enpoint Verification has another feature: start connection which is based on OpenVPN, only available on Windows and MacOS.

On Mac/Windows, Chrome uses OpenVPN to acces internal resources. On Linux, it creates ec384 key pair at `$XDG_HOME/google/Endpoint Verification/certs/`, but I don't see it is used anywhere. Extension's code shows that it's used as client authentication.

BCE Admin Helper on Mac/Windows uses OpenVPN and creates OpenVPN Management Interface to operate with Endpoint Extension. 

The flow of OpenVPN:   
Now, it uses Chrome's sync here, the refresh token is obtained from Chrome's sync and send 2 different requests: which obtains auth for issuetoken.google.com and 1 token for authenticating with OpenVPN.

Endpoint verification sends username and 1 time password to BCE Admin Helper through socket. The password is very short TTL.

## Conclusion
As I see, I can obtain the body of register/report device state and relay to any device. In the dashboard, admin only see my authorized device. Intune only helps check compliance, the data is static even it is encrypted. No way to check the request is coming from authorized devices.

BCE is not perfect as I know, we cannot rely on single factor: Endpoint verification. Look at the http requests, more problem I discovered when attacker can obtain device info via assets management team and 1 account was compromise. Nothing's secure here. Corporation must use Role Based Access Control, IAP is only additional enhancement.

I reported to Google and they acknowledged this problem.
In the future, I think I would like to chain some problem to access something.
