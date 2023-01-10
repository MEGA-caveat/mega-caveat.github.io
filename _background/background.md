---
title: Background
html-id: background
---

[MEGA](https://mega.io) is a cloud storage and communication platform with over 265 million user accounts, advertising itself as secure and private by design. On MEGA, user files should remain confidential even if the storage provider is malicious or has been compromised through a breach, implying security in a strong threat model. The security of MEGA in this setting was recently analysed in detail by [Backendal, Haller and Paterson](https://mega-awry.io), who described five attacks on the cryptographic protocol used by MEGA to authenticate users and encrypt user data. The first two of these attacks completely broke the confidentiality of user files. Further, [Ryan and Heninger](https://eprint.iacr.org/2022/914) significantly improved the first attack, reducing its requirement of 512 user logins to just 6.

At their heart, the original attacks exploit the lack of both key separation and integrity protection
for stored keys in the MEGA design. A single user master key is used to encrypt both the user's
RSA private key and the user's file encryption keys themselves, while AES in ECB mode is used for the encryption. 
The authors of the original attacks proposed an immediate and non-invasive mitigation step in the form of
adding a MAC to the existing construction. In response, MEGA chose to not implement this or any
of the other originally suggested countermeasures. Instead, they added [extra sanity checks](https://github.com/meganz/webclient/commit/d2a0d054d4dbb90f035b3b4b421f780adafaa78e) in the client software to do more validation of payloads during or after decryption. These checks were sufficient to prevent the previous attacks. 

Shortly after, MEGA made one other change which further increased the attack surface of their code: they added [detailed error reporting](https://github.com/meganz/webclient/commit/cd4ab89b2cd0e388b0ea55753b86c8808f810138) during the decryption and sanity checking processes done by the client as part of the authentication protocol. The errors produced during these steps were mostly distinguishable from one another and the error messages were sent to the server in place of the usual authentication response. A malicious storage provider could exploit this, triggering the errors by supplying specially crafted inputs in an attempt to learn something about the decrypted data.
