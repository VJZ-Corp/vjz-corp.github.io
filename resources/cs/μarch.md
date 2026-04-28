---
title: Modern CPU Microarchitecture
layout: default
filename: μarch.md
---

# Modern CPU Microarchitecture
Take a look at this unassuming picture of AMD's Ryzen 9 9950X:

<img width="1050" height="591" alt="image" src="https://github.com/user-attachments/assets/a302b2d5-8a26-4db4-9a19-bb1eb85b4fa5" />

Billions of CPUs like the Ryzen 9 9950X (albeit not as powerful) sit in motherboards and PCBs all around the world. Ryzen 9 9950X belongs to the AMD Zen 5 microarchitecture. Here is what happens if you take the cover off:

<img width="1200" height="1200" alt="image" src="https://github.com/user-attachments/assets/c83b3a32-78ce-4053-bae7-63bfe715dba5" />

There are two dies in Zen 5 chips. The larger one in the top center supports PCIe and media encoding, and it contains the memory controller, the display engine, and many more modern components. The actual CPU itself is in the lower right, and here is the zoomed in labeled die shot:

<img width="1399" height="1200" alt="image" src="https://github.com/user-attachments/assets/aa7121c5-1efe-4aac-a746-a8bf305827d6" />

Why do caches take up so much space? What is inside of each core? How are modern CPUs faster than ever? These are some of the questions that we hope to answer in this walkthrough. Modern processors are not simple fetch, decode, and execute pipelines anymore. They often try to predict the future, execute out-of-order, and have support for executing several instructions in parallel. We will look at several mechanisms that enable this to work flawlessly for countless devices every second.
