---


title:  "Fido notes"

date:   2024-02-27 10:45:58 +0700

author: severus

categories: Security

tags: fido2 webauthn u2f

---

## U2F vs Fido2
Fido U2F is renamed to CTAP1. 
    
FIDO UAF: FIDO Universal Authentication Framework.  
   
WebAuthn: New API supports passwordless login, compatible with Fido U2F/UAF.  
  
Fido2: CTAP2 + Webauthn + UserVerification.  
  
CTAP2: CTAP1 + resident key. Because it does not have key managment, when it reachs limitation of keys's number, it must be reset.    
  
CTAP2.1: CTAP2 + resident key management + authenticator attestion.  
  
WebAuthn: authentication implementation supports passwordless login, multi factor authentication.   
  

The flow:  
Relying Party <---> WebAuthn <---> Browser/App <----> CTAP <----> Authenticator  
  
## Non-Resident Key vs Resident Key
Non-Resident Key: Authenticator stores master key and creates seed to derive sub key (ephemeral key). The encrypted seed and public key are sent to server. Authenticator does not store any seed or information about Relying Party, then it can generate unlimited keys.  
  
Resident Key: Authenticator stores master key and private key to storage. Based on limitation of storage, amount of Resident Keys are limited: eg 25 for Yubikey and 50 for VinCSS Fido2 key.  
  
 
### Implementation of Non-Resident Key and Resident Key
Non-Resident Key requires Relying Party lookup Credential ID on their side. Then user must enter their username before looking up. Server must verify that authenticator has any public key for current user unless user sends unlimited keys and performance would be degraded.   
  
With FIDO2, Relying Party sends list of Credential ID and Authenticator looks up on their side. It improves time and authentication process. Relying Party must allow user to choose public key for multiple users on same authenticator like: acme@gmail.com or severus@gmail.com.  

 
