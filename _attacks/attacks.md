---
title: Attacks
html-id: attacks
---

We discovered an ECB encryption oracle under a target user's master key in the MEGA system. The oracle arised in the MEGAdrop feature, which was supposed to be independent of the authentication protocol, yet used the
same master key. This oracle provided an adversary with the ability to partially overwrite a target user's RSA private key with chosen data, a powerful capability that we use in our attacks.

Our attacks allow an adversary to decrypt blocks encrypted using AES-ECB under the target user's master key. In the MEGA infrastructure, this enables an attacker to recover blocks of the target user's RSA private key. To recover the entire key, running either of our attacks to get four specific blocks is sufficient when combined with lattice techniques. Once the private key is recovered, the adversary could trivially recover the keys needed to decrypt any files shared with the target user as well as recover individual file encryption keys directly.

We present two distinct types of attack, each type exploiting different error conditions arising in the sanity checks and in subsequent cryptographic processing during MEGA's user authentication procedure. The first type appears to be novel and exploits the manner in which the MEGA code handles modular inversion when recomputing $$ u = q^{−1} \bmod p $$. The second can be viewed as a small-subgroup attack. We prototyped the attacks to show that they work in practice on the MEGA webclient.

As a side contribution, we show how to improve the [original RSA key recovery attack](https://mega-awry.io/#rsa-key-recovery) against the unpatched version of MEGA to require only 2 logins instead of the original 512.

The following diagram shows the encoding format of RSA private keys in MEGA.
<picture>
    <source media="(max-width: 1000px)" srcset="img/secret_key_encoding.svg">
    <img id="key-encoding" alt="MEGA's RSA private key encoding" class="img-fluid" style="max-width: 100%" src="img/secret_key_encoding.svg">
</picture>

## Attack based on modular inverse computation

The first attack exploited an implicit error in the computation of modular inverses when sanity checking the RSA private key. It was an (un)fortunate consequence of an otherwise harmless bug in the code (not checking whether an inverse exists) which was caught by the client and reported to the server. The malicious server could use this oracle repeatedly to learn the value of the target AES-ECB plaintext block modulo a number of small primes, which enabled recovery of the full block using the Chinese Remainder Theorem (CRT). On average, the attack requires 627 login attempts per recovered AES-ECB plaintext block and 66 ECB encryption oracle queries per attacked user.

To obtain the entire private RSA key, the attack can be repeated on a block-by-block basis. If the attack was used directly to e.g. recover all of $$ p $$, the number of blocks needed would be 9, but using lattice techniques this can be reduced to 4. The attack complexity of recovering the full RSA private key using our first attack is then 2508 login attempts on average.

## Attack based on small subgroups

The second attack relied on how RSA decryption was carried out by the client during user authentication. It exploited a legacy artefact in the code that changed the resulting RSA plaintext length if a certain byte condition on the plaintext did not hold, in combination with an explicit error arising from a plaintext length check that was again reported to the server. The attack is a form of a [small order subgroup attack](https://doi.org/10.1007/BFb005224), in that it relies on the ECB encryption oracle to overwrite the RSA primes $$ p, q $$ with values such that $$ (p − 1)(q − 1) $$ has known small prime factors. This forced the client's decryption to take place in one of several small subgroups. 

The attack also overwrites the user's private key value $$ d $$ with a value that is completely
known except in the target plaintext block. Then, the malicious server could use the length check oracle
repeatedly to learn the value of $$ d $$, and hence the target plaintext block, modulo each of the small
primes. The final step again combines these values using the CRT to recover the target block.
We present two versons of this attack, requiring on average 2419 and 3169 login attempts per block, respectively, and up to 15 ECB encryption oracle queries per attacked user. This attack exploits different errors and checks from
the first one: we include it to showcase that the existence of such checks and differentiated error
reporting increases the attack surface.

