---
title: Modern CPU Microarchitecture
layout: default
filename: μarch.md
---

# Modern CPU Microarchitecture
Take a look at this unassuming picture of AMD's Ryzen 9 9950X:

<img width="825" height="465" alt="image" src="https://github.com/user-attachments/assets/a302b2d5-8a26-4db4-9a19-bb1eb85b4fa5" />

Billions of CPUs like the Ryzen 9 9950X (albeit not as powerful) sit in motherboards and PCBs all around the world. Ryzen 9 9950X belongs to the AMD Zen 5 microarchitecture. Here is what happens if you take the cover off:

<img width="825" height="825" alt="image" src="https://github.com/user-attachments/assets/c83b3a32-78ce-4053-bae7-63bfe715dba5" />

There are two dies in Zen 5 chips. The larger one in the top center supports PCIe and media encoding, and it contains the memory controller, the display engine, and many more modern components. The actual CPU itself is in the lower right, and here is the zoomed in labeled die shot:

<img width="825" height="707" alt="image" src="https://github.com/user-attachments/assets/aa7121c5-1efe-4aac-a746-a8bf305827d6" />

Why do caches take up so much space? What is inside of each core? How are modern CPUs faster than ever? These are some of the questions that we hope to answer in this walkthrough. Modern processors are not simple fetch, decode, and execute pipelines anymore. They often try to predict the future, execute out-of-order, and have support for executing several instructions in parallel. We will look at several mechanisms that enable this to work flawlessly for countless devices every second.

### Disclaimer
Modern Intel and AMD processors are extremely complex to understand. There are many proprietary technologies at play and these companies really want to make it very hard to reverse engineer. x86 is also a variable-length ISA, making decoding more complex than RISC architectures. x86 decoding may warrant itself its own article, so will not be discussed here. Instead, we will use Microprocessor without Interlocked Pipelined Stages (MIPS) in running examples and diagrams.

## Single-Cycle Processor
These are very basic processors that nobody uses nowadays outside of teaching examples. Each instruction gets executed in one cycle, which may seem great, but cycle times are horrendously long. Consider the following delays:

Subsystem | Delay
--- | ---
Instruction memory access | 100 ps
Register file read | 200 ps
ALU | 150 ps
Data memory access | 350 ps
Register file write | 175 ps

Single-cycle processors determine the clock cycle time by the sum of all delays. So even though a instruction like `addu r1, r1, r2` never uses data memory, it still takes 975 picoseconds to complete.

<img width="825" height="601" alt="image" src="https://github.com/user-attachments/assets/fcc3b2ba-c627-4519-9fbd-82b12f523c44" />

The only upside of single-cycle processors are their simple designs as seen from the above diagram. That is why they are primarily used in classrooms. Their inefficiency would not make them perform well on any real workloads.

## Pipelined Processor
The biggest shortcoming of the single-cycle processor was the lack of utilization. An instruction would only occupy one component at a time, leaving the other components sitting around and doing nothing. Pipelined processors instead group the components by stages. A classic version has 5 stages: (F)etch, (D)ecode, (E)xecute, (M)emory, and (W)riteback. As seen in the diagram below, pipelined processors are way more complicated in design:

<img width="825" height="464" alt="image" src="https://github.com/user-attachments/assets/b426b3c3-643a-4cb9-9e30-ec46d4d60ea7" />

With pipelined processors, the clock cycle time reduces to 350 ps, an almost 3x speedup. To see why this is the case, consider the following simple workload:

```
add r4, r2, r1
sw r2, 2(r5)
slt r3, r5, r4
```

In the single-cycle processor, the following diagram illustrates why it takes 15 cycles:

cycle # | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ---
`add r4, r2, r1` | F | D | E | M | W |
`sw r2, 2(r5)`   |   |   |   |   |   | F | D | E | M | W
`slt r3, r5, r4` |   |   |   |   |   |   |   |   |   |   | F | D | E | M | W

Whereas a pipelined processor would only take 7 cycles:

cycle # | 1 | 2 | 3 | 4 | 5 | 6 | 7
--- | --- | --- | --- | --- | --- | --- | ---
`add r4, r2, r1` | F | D | E | M | W |
`sw r2, 2(r5)`   |   | F | D | E | M | W
`slt r3, r5, r4` |   |   | F | D | E | M | W

Even though the forwarding unit and hazard detector handle most dependency situations, sometimes the processor still has to stall. A notable example is a **load-use hazard**:

cycle # | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- 
`add r1, r4, r5` | F | D | E | M | W
`sw r2, 2(r1)`   |   | F | D | E | M | W
`lw r3, 8(r6)`   |   |   | F | D | E | M | W
`add r5, r1, r4` |   |   |   | F | D | D** | E | M | W
`sub r1, r8, r2` |   |   |   |   |   | F | D | E | M | W

**The decode stage stalls because the previous instruction (`lw`) cannot forward to execution without completing the memory stage first.

## Superscalar Processor
A natural way to further speed up execution is to widen the pipeline. Rather than fetching one instruction at a time, we can fetch two or more instructions. This makes the processor superscalar. For example, consider a 2-wide superscalar processor running these instructions:

cycle # | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- 
`loop:  add r3, r2, r6` | F | D | E | M | W
`lw r1, 10(r5)`         | F | D | E | M | W
`sub r4, r4, r1`        |   | F | D | D | E | M | W
`and r6, r4, r2`        |   | F | D | D | D | E | M | W
`or r4, r6, r3`         |   |   | F | F | D | D | E | M | W
`addi r5, r5, #1`       |   |   | F | F | F | D | E | M | W
`bne r5, r8, loop`      |   |   |   |   | F | F | D | E | M | W
