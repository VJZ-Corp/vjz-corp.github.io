---
title: Modern CPU Microarchitecture
layout: default
filename: μarch.md
---

# Modern CPU Microarchitecture
Take a look at this unassuming picture of AMD's Ryzen 7 9700X:

<img width="534" height="532" alt="image" src="https://github.com/user-attachments/assets/71f0c2bf-d68b-4b86-9ac9-98e279934524" />

Billions of CPUs like the Ryzen 7 9700X (albeit not as powerful) sit in motherboards and PCBs all around the world. The 9700X belongs to the AMD Zen 5 microarchitecture. Here is what happens if you take the cover off:

<img width="825" height="825" alt="image" src="https://github.com/user-attachments/assets/c83b3a32-78ce-4053-bae7-63bfe715dba5" />

There are two dies in Zen 5 chips. The larger one in the top center supports PCIe and media encoding, and it contains the memory controller, the display engine, and many more modern components. The actual CPU itself is in the lower right, and here is the zoomed-in labeled die shot:

<img width="825" height="707" alt="image" src="https://github.com/user-attachments/assets/aa7121c5-1efe-4aac-a746-a8bf305827d6" />

Why do caches take up so much space? What is inside of each core? How are modern CPUs faster than ever? These are some of the questions that we hope to answer in this walkthrough. Modern processors are not simple fetch, decode, and execute pipelines anymore. They often try to predict the future, execute out-of-order, and have support for executing several instructions in parallel. We will look at several mechanisms that enable this to work flawlessly for countless devices every second.

### Disclaimer
Modern Intel and AMD processors are extremely complex to understand. There are many proprietary technologies at play and these companies really want to make it very hard to reverse engineer. x86 is also a variable-length ISA, making decoding more complex than RISC architectures. x86 decoding may warrant itself its own article, so will not be discussed here. Instead, we will use Microprocessor without Interlocked Pipelined Stages (MIPS) in running examples and diagrams.

## Single-Cycle Processor
These are very basic processors that nobody uses nowadays outside of teaching examples. Each instruction gets executed in one cycle, which may seem great, but cycle times are horrendously long. Consider the following delays:

- Instruction memory access: 100 ps
- Data memory access: 350 ps
- Register file read: 200 ps
- Register file write: 175 ps
- ALU execution: 150 ps

Single-cycle processors determine the clock cycle time by the sum of all delays. So even though a instruction like `addu r1, r1, r2` never uses data memory, it still takes 975 picoseconds to complete.

<img width="825" height="601" alt="image" src="https://github.com/user-attachments/assets/fcc3b2ba-c627-4519-9fbd-82b12f523c44" />

The only upside of single-cycle processors are their simple designs as seen from the above diagram. That is why they are primarily used in classrooms. Their inefficiency would not make them perform well on any real workloads.

## Pipelined Processor
The biggest shortcoming of the single-cycle processor was the lack of utilization. An instruction would only occupy one component at a time, leaving the other components sitting around and doing nothing. Pipelined processors instead group the components by stages. A classic version has 5 stages: (F)etch, (D)ecode, (E)xecute, (M)emory, and (W)riteback. As seen in the diagram below, pipelined processors are way more complicated in design:

<img width="825" height="464" alt="image" src="https://github.com/user-attachments/assets/b426b3c3-643a-4cb9-9e30-ec46d4d60ea7" />

If we apply the above delays to our pipelined processor, the clock cycle time reduces to 350 ps, an almost 3x speedup. To see why this is the case, consider the following simple workload:

```
add  r4, r2, r1
sw   r2, 2(r5)
slt  r3, r5, r4
```

In the single-cycle processor, the following diagram illustrates why it takes 15 cycles:

cycle # | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ---
`add  r4, r2, r1` | F | D | E | M | W |
`sw   r2, 2(r5)`   |   |   |   |   |   | F | D | E | M | W
`slt  r3, r5, r4` |   |   |   |   |   |   |   |   |   |   | F | D | E | M | W

Whereas a pipelined processor would only take 7 cycles:

cycle # | 1 | 2 | 3 | 4 | 5 | 6 | 7
--- | --- | --- | --- | --- | --- | --- | ---
`add  r4, r2, r1` | F | D | E | M | W |
`sw   r2, 2(r5)`   |   | F | D | E | M | W
`slt  r3, r5, r4` |   |   | F | D | E | M | W

Even though the forwarding unit and hazard detector handle most dependency situations, sometimes the processor still has to stall. A notable example is a **load-use hazard**:

cycle # | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- 
`add  r1, r4, r5` | F | D | E | M | W
`sw   r2, 2(r1)`   |   | F | D | E | M | W
`lw   r3, 8(r6)`   |   |   | F | D | E | M | W
`add  r5, r1, r3` |   |   |   | F | D | D** | E | M | W
`sub  r1, r8, r2` |   |   |   |   | F | F | D | E | M | W

**The decode stage stalls because the previous instruction (`lw`) cannot forward to the next instruction's (`add`) execute stage since `add` needs the result of `lw` which is only produced after the memory stage.

## Superscalar Processor
A natural way to further speed up execution is to widen the pipeline. Rather than fetching one instruction at a time, we can fetch two or more instructions. This makes the processor superscalar. For example, consider a 2-wide superscalar processor running these instructions:

cycle # | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ---
`add   r3, r2, r6` | F | D | E | M | W
`lw    r1, 10(r5)`  | F | D | E | M | W
`sub   r4, r4, r1` |   | F | D | D | E | M | W
`and   r6, r4, r2` |   | F | D | D | D | E | M | W
`or    r4, r6, r3`  |   |   | F | F | D | D | E | M | W
`addi  r5, r5, #1`|   |   | F | F | F | D | E | M | W
`slt   r9, r7, r6` |   |   |   |   | F | F | D | E | M | W

Even though more instructions can be fetched at a time, dependencies are still the biggest bottleneck. In the above example, more than 70% of instructions stalled due to dependent instructions. Wouldn't it make more sense to execute independent instructions that do not depend on each other first instead of stalling? That is exactly what processor designers sought to answer, which is the main topic of the next section.

## Out-of-Order Processor
Up until now, our optimizations all boil down to one goal: keeping all components of the CPU busy. With pipelining, we managed to continuously feed instructions to the processor rather than waiting for the previous one to fully complete. Nevertheless, dependent instructions lead to hazards, which partially negated the benefit of pipelining. Consider the following program plagued with load-use hazards and its pipeline diagram:

cycle # | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ---
`lw   r1, 0(r10)`  | F | D | E | M | W
`add  r2, r1, r3`  |   | F | D | D | E | M | W
`lw   r4, 4(r10)`  |   |   | F | F | D | E | M | W
`add  r5, r4, r6`  |   |   |   |   | F | D | D | E | M | W
`lw   r7, 8(r10)`  |   |   |   |   |   | F | F | D | E | M | W
`add  r8, r7, r9`  |   |   |   |   |   |   |   | F | D | E | M | W

We can change the ordering of these instructions and remove all stalls:

cycle # | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ---
`lw   r1, 0(r10)`  | F | D | E | M | W
`lw   r4, 4(r10)`  |   | F | D | E | M | W
`lw   r7, 8(r10)`  |   |   | F | D | E | M | W
`add  r2, r1, r3`  |   |   |   | F | D | E | M | W
`add  r5, r4, r6`  |   |   |   |   | F | D | E | M | W
`add  r8, r7, r9`  |   |   |   |   |   | F | D | E | M | W

The programmer could reorder instructions themselves to realize this speedup, but that is extremely tedious and difficult. Instead, most modern processors automatically do this for you. This is the origin of the name "out-of-order," describing how CPUs would internally schedule instructions in an order that was different than written. We will see several ways the hardware enables this to happen.

### Register Renaming
The first major problem to tackle is the limited amount of registers available. Consider the following program:

```
add  r1, r2, r3
mul  r4, r1, r5
add  r1, r6, r7
sub  r8, r1, r9
```

What dependencies exist?
1. `mul` needs `r1` from the first `add`
2. `sub` needs `r1` from the *second* `add`

This creates two types of hazards:
- (W)rite (A)fter (W)rite - when two writes go to the same register.
- (W)rite (A)fter (R)ead - when later write might overwrite before earlier read.

But notice: `r1` gets completely recomputed in the second `add` instruction. The first version of `r1` is different from the second version of `r1`. This is where register renaming would be useful. Since different microarchitectures have different amounts of registers, program compatibility quickly breaks if we do not maintain two types of registers:

- *Architectural register* - these registers are specified by the ISA. These represent abstract registers guaranteed to the programmer. For example, MIPS has `r1` through `r31` and x86 has `rax, rbx, rcx, rdx, rdi, ...`
- *Physical register* - these represent the real registers inside the processor. There are often hundreds of them. Zen 5 CPUs have 240 physical registers for integer instructions.

The processor keeps track of which architectural register maps to which physical register using the register alias table. The processor also has a free list of registers that it can use. Going back to our earlier example, let us assume this is the alias table at the start of the program:

arch reg | phys reg
--- | ---
`r1` | `t11`
`r2` | `t12`
`r3` | `t13`
`r4` | `t14`
`r5` | `t15`
`r6` | `t16`
`r7` | `t17`
`r8` | `t18`
`r9` | `t19`

The free list at this point starts at `t20` and goes onwards. We can rename the program:

```
add  t20, t12, t13
mul  t21, t20, t15
add  t22, t16, t17  # can run early since t22 and t20 are independent of each other
sub  t23, t11, t19
```

The renaming process is straightforward and successfully removes the hazards that we were talking about. Each time a new result is produced, we grab a free register and update the register alias table. The following changes are made:

arch reg | phys reg
--- | ---
`r1` | ~~`t11`~~, ~~`t20`~~, `t22`
`r2` | `t12`
`r3` | `t13`
`r4` | ~~`t14`~~, `t21`
`r5` | `t15`
`r6` | `t16`
`r7` | `t17`
`r8` | ~~`t18`~~, `t23`
`r9` | `t19`

You might think that this is wasteful since we keep using up free registers. At a certain point, physical registers are freed to be used again. We will discuss about that below (see section on commit stage). In OoO processors, register renaming is itself a stage, commonly just called "rename." Besides doing renaming, it also has a few other responsibilities...

### Zeroing Idioms and Move Elimination
Two clever optimization ideas stem from the fact we can now rename registers. Imagine a common pattern for clearing a register generated by compilers:

```
add  r1, r2, r3   # do stuff with r1
xor  r1, r1, r1   # set r1 to 0
```

Modern CPUs automatically know that `r1 ^= r1` will always result in `r1 = 0`. This is known as a zeroing idiom. The rename stage can recognize that given the pattern `xor rX, rX, rX`, it can update the corresponding physical register representing `rX` to 0. The instruction never makes it to the ALU as there is no need, meaning it was never executed at all! Some more zeroing idioms:

- `xor  rX, rY, rY`
- `sub  rX, rY, rY`
- `add  rX, r0, r0`
- `and  rX, rY, r0`
- `mul  rX, rY, r0`

Depending on the specifics, all of the above instruction patterns never actually get executed, making them near zero-cost. 

Move elimination is a similar idea. Any instructions that effectively copy the contents of two registers can be eliminated by renaming. Consider the following:

```
move r2, r1
```

When the processor updates the register alias table, the following happens:

arch reg | phys reg
--- | ---
`r1` | `t45`
`r2` | also `t45`

The `move` instruction disappears since the processor knows that `r2 = r1` and `r1` maps to `t45`. By the transitive property, the processor maps `r2` to `t45` as well.

### Re-order Buffer
