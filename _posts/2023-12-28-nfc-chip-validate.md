---


title:  "How do we validate NFC ID card correctly?"

date:   2023-12-28 22:15:58

author: severus

categories: Security

tags: nfc chip id cccd security

---

## NFC ID Card - new challenge for fraudsters.
NFC ID card is new challenge for fraudsters and banking/fintech. Based on cryptography, the data on card must be consistent.  

NFC ID card overview: 
- Data is split into 16 groups (from dg1 to dg16) and SoD. 
- SoD contains data hash from dg1-dg16 with signed message. 
- Private key is residing in NFC chip, which can not be exported. 
- Public key is on data, which is used to verify challenge - clone detection. 
- Basic Access Control (BAC): the data point on Machine Readable Zone (MRZ) lines which is read by OCR to authenticate with NFC chip.
- Password Authenticated Connection Establishment (PACE): 6 digits code on front card ID, which read by OCR to authenticate with NFC chip. 
- BAC and PACE protect citizen from mass scan NFC tags. 

Fraudsters must bypass SoD and BAC/PACE. 

Does NFC ID Card secure onboarding process? The answer is yes and no, based on your process validation.

## The attack on NFC ID Card.
In my [previous post](https://infosec.xyz/security/passport-nfc-extract-sod/), we know the way to extract data from SoD. This poste will decribe new ways to attack NFC ID Card. 

### Passive Authentication, Active Authentication and Chip Authentication
Passive Authentication only validates SoD signed message and compares hash from dg1-16 values. If fraudsters generate new certificate and signed with this certificate, the party must validate again with Country Signing Certification Authority (CSCA). Because Document Signer Certificate (DCS) is intermediate certificate, it must be validated with CSCA or maintained with well known trusted list. This is mandatory requirement if the party validates passive authentication only. 

Active Authentication validates authentic chip by sending some random challenge to private key and gets signed message from it. There is a problem that if the private key is DSC, fraudsters could send raw SoD data and get signed SoD. This leads to generate any valid SoD. The private key must be not DSC private key. If fraudsters are rich enough, they can create a card with their own private key, the party must validate again with well known DSC, known chip public key or CSCA. 

Chip Authentication is mutual authentication between NFC chip and terminal. It validates both terminal and NFC chip before reading data. Both parties share their public keys and validate the signed message with trusted public key. The trusted public key is stored in NCF chip and terminal. This is the most secure way to validate data on NFC ID card. 

If many NFC ID Cards are stolen and they are used to onboard new customers, the party must validate information with liveness detection. And server side must use vector model instead of 2D image. 

With all information I share, not only banking/fintech must validate NFC ID card carefully, but also goverment needs to seperate the key for internal chip and the key for signing document.
