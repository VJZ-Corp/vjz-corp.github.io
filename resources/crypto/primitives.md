---
title: Cryptographic Primitives
layout: default
filename: primitives.md
---

# Cryptographic Primitives
Knowing which concept to use to protect your information is crucial when applying cryptography on production systems. Not all primitives are built equally, and their guarantees need to be understood to prevent catastrophic mistakes. Before diving in to the details, consider your end goal for the data that you protect:

- *Secrecy*: An adversary with access to a piece of data should not be able to extract any meaningful information out of it.
- *Authenticity*: An adversary should not be able to forge a message or spoof the identity of the original author.
- *Verification*: How can one party trust the other when interacting for the first time?

There are other goals, but the main ones fall into the three categories shown above. The primitives presented here address all of them, along with some nuances and consequences. These primitives make up the vast majority of modern safe digital communication.

## Ciphers
Ciphers are just algorithms that obfuscate and transform data into a format that is designed to be hard to extract meaningful data from. There are countless examples of ciphers used in history, such as the well-known Caeser cipher. Ciphers can be built many ways (a lot are not as secure as they seem), but this guide will only focus on key-based algorithms.

### Symmetric Encryption

<img width="796" height="435" alt="image" src="https://github.com/user-attachments/assets/da92ab6c-0c52-44ed-8b7d-3e53236e9026" />

Symmetric-key encryption involves a pair of functions $E, D$ and a secret key $k$. The functions take in a plaintext message $m$ and produce a ciphertext $c$:

$$
E(m, k) = c \neq m,
$$

$$
D(E(m, k), k) = m.
$$

In order for $E$ to somewhat maintain secrecy, it cannot produce $c = m$. Otherwise, the whole purpose of encryption is defeated. A common example of this is $\text{XOR}(m, 0) = m$. The decryption function $D$ should undo the encryption and retrieve $m$ in its original form. Notably, $k$ used for both encryption **and** decryption and is shared between both parties. 

Symmetric-key encryption can use either stream ciphers or block ciphers. Stream ciphers encrypt the bytes of a message one at a time. Block ciphers take a number of bits and encrypt them in a single unit, padding the plaintext to achieve a multiple of the block size. Examples of popular symmetric-key algorithms include Twofish, AES (Rijndael), Camellia, ChaCha20, Blowfish, RC4, DES, and 3DES. The details of each algorithm could be further explored in a distinct lesson.

### Asymmetric Encryption
These types of algorithms belong to a broader field of public-key cryptography. As the name implies, rather than having a shared secret key, asymmetric encryption uses a keypair $(k_{pub}, k_{pri})$, where $k_{pub}$ is the public key and $k_{pri}$ is the private key. Each party has their own keypair making the total number of keys twice the number of parties involved. Using math that will not be discussed in detail here, the public key can be freely shared with anybody in the world, including the adversary. The private key must be kept secret at all times, however. Below shows an example of how asymmetric encryption can be used:

<img width="800" height="782" alt="image" src="https://github.com/user-attachments/assets/6e5756ce-fc20-46e8-a0f1-309d0ce7d472" />

1. Bob wants to send plaintext $m$ to Alice.
2. Bob encrypts with Alice's public key: $E(m, k^{(A)}_{pub}) = c$.
3. Bob sends ciphertext $c$ to Alice. Any adversary positioned along this path would be able to intecept, but not decrypt $c$.
4. Alice receives $c$ and decrypts it using her own private key: $D(m, k^{(A)}_{pri}) = m$.
5. $m$ should be decrypted and readable to Alice.

Some famous asymmetric encryption algorithms include the Diffie–Hellman key exchange protocol, DSS (Digital Signature Standard), ElGamal, Elliptic-curve cryptography, and RSA. The details of each algorithm could be explored in a distinct lesson.

## Initialization Vectors
Currently, our encryption algorithm $E(m, k)$ always produces the same $c$. This may not seem problematic at first, but consider the following scenario:

1. Alice sends numerous messages with the same key $k$ to Bob.
2. Among those messages, $c' = E(m', k)$ is send a bunch of times.
3. The adversary sees that the contents matching $c'$ were sent repeatedly.
4. This does not directly leak $m'$, but may be consequential in some circumstances.
5. Even though the adversary cannot directly read the plaintext, they can still learn patterns and structures over time.

IVs aim to prevent the above scenario by introducing randomness like so:

$$
E(m, k, IV_1) \neq E(m, k, IV_2).
$$

Namely, even if $m$ and $k$ are the exact same, the ciphertext generated would differ due to separate IVs. This ensures a randomized encoding of a message is fresh, so identical inputs never produce predictable or repeatable outputs.

## Hashing
A hash function takes an input of any size (generalized as a bit stream) and returns an output of a fixed size such that changing any bit of the input would also change the output. The output of a hash function is called the **digest**.

$$
H : \{0, 1\}^* \rightarrow \{0, 1\}^n,
$$

where $n$ is the digest length.

A crytographically-secure hash function is not feasibly invertible. That is, given the output of the hash function and the code of the function itself, finding an input that would generate that output requires work equivalent to brute forcing until the desired output is found.

