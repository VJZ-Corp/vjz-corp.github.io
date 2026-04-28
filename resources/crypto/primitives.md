---
title: Cryptographic Primitives
layout: default
filename: primitives.md
---

# Cryptographic Primitives
Knowing which concept to use to protect your information is crucial when applying cryptography on production systems. 
Not all primitives are built equally, and their guarantees need to be understood to prevent catastrophic mistakes.
Before diving in to the details, consider your end goal for the data that you protect:

- *Secrecy*: An adversary with access to a piece of data should not be able to extract any meaningful information out of it.
- *Authenticity*: An adversary should not be able to forge a message or spoof the identity of the original author.
- *Verification*: How can one party trust the other when interacting for the first time?

There are other goals, but the main ones fall into the three categories shown above.
The primitives presented here address all of them, along with some nuances and consequences.
These primitives make up the vast majority of modern safe digital communication.

## Ciphers
Ciphers are just algorithms that encrypt/decrypt plaintext.
There are countless examples of ciphers used in history, such as the well-known Caeser cipher.
Ciphers can be built many ways (a lot are not as secure as they seem), but this guide will only introduce key-based algorithms.

### Symmetric Encryption
Symmetric-key encryption involves a pair of functions $E, D$ and a secret key $k$. 
The functions take in a plaintext message $m$ and produce a ciphertext $c$:

$$
E(m, k) = c \neq m,
$$
$$
D(E(m, k), k) = m.
$$

In order for $E$ to be somewhat secure, it cannot produce $c = m$. Otherwise, the whole purpose of encryption is defeated. A common example of this is $\text{XOR}(m, 0) = m$. The decryption function $D$ should undo the encryption and retrieve $m$ in its original form. Notably, $k$ used for both encryption **and** decryption and is shared between both parties. 

Symmetric-key encryption can use either stream ciphers or block ciphers. Stream ciphers encrypt the bytes of a message one at a time. Block ciphers take a number of bits and encrypt them in a single unit, padding the plaintext to achieve a multiple of the block size. Examples of popular symmetric-key algorithms include Twofish, AES (Rijndael), Camellia, ChaCha20, Blowfish, RC4, DES, and 3DES.
