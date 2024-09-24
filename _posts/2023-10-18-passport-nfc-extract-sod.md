---

layout: post

title:  "Extract passport NFC's SoD certificate"

date:   2023-10-18 10:45:58 +0700

author: severus

categories: Security

tags: passport nfc SoD extraction

---

NFC data is followed ICAO's [specification](https://www.icao.int/publications/documents/9303_p10_cons_en.pdf).

We must verify the Security Object Document (SoD), it contains signature, data and certificate of Document Signer (DS).

Wait, what's Document Signer Certificate (DSC)? DSC is intermediate certificate, which is signed by Country Signing Certification Authority (CSCA). DSC is in SoD, CSCA should be public, so everyone can verify the SoD independently.

Look at SoD, we have someway to inspect it.

Extract SoD, we have base64 data, then convert it to der format: 
```bash
cat sod.txt| base64 -d > sod.der
```

strip out SoD header, we have pkcs7 format: 
```bash
openssl asn1parse -inform der -in sod.der -strparse 4 -noout -out sod.pkcs7
```

with sod.pkcs7 we have all data group's hashes.
```bash
openssl cms -inform der -noverify -verify -in sod.pkcs7 -out sod.message
openssl asn1parse -inform der -in sod.message
```

Extract the certificate from SoD: 
```bash
openssl pkcs7 -inform der -print_certs -in sod.pkcs7
```

Inspect certificate from SoD: 
```bash
openssl pkcs7 -inform der -print_certs -in sod.pkcs7 | openssl x509 -noout -text
```
 

We know the CSCA location from DSC's content.

If we have CSCA, we can verify SoD from our end independently: 
```bash
openssl cms -inform der  -verify -certfile CSCA.cert -in sod.pkcs7 -noout
```

If we have CSCA - which should be public by default, we will validate without problem on our side.
