---
title: Modern CPU Microarchitecture
layout: default
filename: μarch.md
---

# Modern CPU Microarchitecture
Take a look at this unassuming picture of AMD's Ryzen 9 9950X:

<img width="900" height="507" alt="image" src="https://github.com/user-attachments/assets/a302b2d5-8a26-4db4-9a19-bb1eb85b4fa5" />

Billions of CPUs like the Ryzen 9 9950X (albeit not as powerful) sit in motherboards and PCBs all around the world. Ryzen 9 9950X belongs to the AMD Zen 5 microarchitecture. Here is what happens if you take the cover off:

<img width="900" height="900" alt="image" src="https://github.com/user-attachments/assets/c83b3a32-78ce-4053-bae7-63bfe715dba5" />

There are two dies in Zen 5 chips. The larger one in the top center supports PCIe and media encoding, and it contains the memory controller, the display engine, and many more modern components. The actual CPU itself is in the lower right, and here is the zoomed in labeled die shot:

<img width="900" height="771" alt="image" src="https://github.com/user-attachments/assets/aa7121c5-1efe-4aac-a746-a8bf305827d6" />

Why do caches take up so much space? What is inside of each core? How are modern CPUs faster than ever? These are some of the questions that we hope to answer in this walkthrough. Modern processors are not simple fetch, decode, and execute pipelines anymore. They often try to predict the future, execute out-of-order, and have support for executing several instructions in parallel. We will look at several mechanisms that enable this to work flawlessly for countless devices every second.

### Disclaimer
Modern Intel and AMD processors are extremely complex to understand. There are many proprietary technologies at play and these companies really want to make it very hard to reverse engineer. x86 is also a variable-length ISA, making decoding more complex than RISC architectures. x86 decoding may warrant itself its own article, so will not be discussed here. Instead, we will use Microprocessor without Interlocked Pipelined Stages (MIPS) in running examples and diagrams.

## Single-Cycle Processor
These are very basic processors that nobody uses nowadays outside of teaching examples. Each instruction gets executed in one cycle, which may seem great, but cycle times are horrendously long. Consider the following delays:

Subsystem | Delay
--- | ---
Instruction Memory | 400 ns
Register File | 100 ns
ALU | 100 ns
Data Memory | 800 ns

Single-cycle processors determine the clock period by the slowest instruction. So even though a instruction like `addu $1, $1, $2` never uses data memory, it still takes 800 picoseconds to complete.

<img width="900" height="655" alt="image" src="https://github.com/user-attachments/assets/fcc3b2ba-c627-4519-9fbd-82b12f523c44" />

The only upside of single-cycle processors are their simple designs as seen from the above diagram. That is why they are primarily used in classrooms. Their inefficiency would not make them perform well on any real workloads.
