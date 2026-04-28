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

