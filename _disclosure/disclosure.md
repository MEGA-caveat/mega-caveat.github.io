---
title: Disclosure 
html-id: disclosure
---

We informed MEGA about the vulnerabilities in their system on 29 September 2022, which MEGA acknowledged on 30 September 2022. We suggested mitigations, stressing the importance of providing proper cryptographic integrity for data stored under users' master keys. We recommended auditing or replacing [MEGA's asmcrypto.js](https://github.com/meganz/webclient/blob/v4.21.4/js/vendor/asmcrypto.js), the low-level library used by the MEGA webclient (an old, custom copy of [asmcrypto.js](https://github.com/asmcrypto/asmcrypto.js)), in which we found several bugs during our analysis. MEGA informed us about their planned overhaul of the protocol that should address the issues we highlighted and we provided feedback. We agreed on a 90-day disclosure period and MEGA awarded a bug bounty. 