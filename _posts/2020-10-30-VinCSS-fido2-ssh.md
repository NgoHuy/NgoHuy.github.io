---

layout: post

title:  "Login SSH bằng VinCSS Fido2 key "

date:   2020-10-30 11:00:00

author: severus

categories: security VinCSS fido2

tags: security VinCSS fido2

---

Từ bản openssh 8.2 hỗ trợ sử dụng FIDO/U2F. VinCSS key chỉ hỗ trợ key dạng  ecdsa-sk.

Generate key:
`ssh-keygen -v -t ecdsa-sk -f ~/.ssh/vincss_key`

Test:
`ssh infosec -i ~/.ssh/vincss_key`
