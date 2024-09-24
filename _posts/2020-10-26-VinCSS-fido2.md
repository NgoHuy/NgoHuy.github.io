---


title:  "Login Linux bằng VinCSS Fido2 key "

date:   2020-10-26 11:00:00

author: severus

categories: security

tags: security VinCSS fido2

---

Fido2 là xu thế sử dụng passwordless đang được dùng rộng rãi. Nhân dịp vừa xin được  key dùng thử  thì mình  setup login  bằng  vinccss key luôn.

Trên linux cần cài pam-u2f và libfido2 packages từ Yubico.

Xem key info:   
`fido2-token -I /dev/hidraw0`

Setup pin cho VinCSS key   
`fido2-token -S /dev/hidraw0`

Setup token cho pam-u2f   
`pamu2fcfg -N -u [user] >> ~/.config/Yubico/u2f_keys`

Setup pam trong `/etc/pam.d/system-auth`   
`auth      sufficient pam_u2f.so pinverification=1 cue`

Test:    
`sudo pacman -Syu`
