---
title: Modern CPU Microarchitecture 101
layout: default
filename: μarch-101.md
---

# Modern CPU Microarchitecture 101
Take a look at this unassuming picture of AMD's Ryzen 7 9700X:

<img width="534" height="532" alt="image" src="https://github.com/user-attachments/assets/71f0c2bf-d68b-4b86-9ac9-98e279934524" />

Billions of CPUs like the Ryzen 7 9700X (albeit not as powerful) sit in motherboards and PCBs all around the world. The 9700X belongs to the AMD Zen 5 microarchitecture. Here is what happens if you take the cover off:

<img width="825" height="825" alt="image" src="https://github.com/user-attachments/assets/c83b3a32-78ce-4053-bae7-63bfe715dba5" />

There are two dies in Zen 5 chips. The larger one in the top center supports PCIe and media encoding, and it contains the memory controller, the display engine, and many more modern components. The actual CPU itself is in the lower right, and here is the zoomed-in labeled die shot:

<img width="825" height="707" alt="image" src="https://github.com/user-attachments/assets/aa7121c5-1efe-4aac-a746-a8bf305827d6" />

Why do caches take up so much space? What is inside of each core? How are modern CPUs faster than ever? These are some of the questions that we hope to answer in this walkthrough. Modern processors are not simple fetch, decode, and execute pipelines anymore. They often try to predict the future, execute out-of-order, and have support for executing several instructions in parallel. We will look at several mechanisms that enable this to work flawlessly for countless devices every second.

### Disclaimer
Modern Intel and AMD processors are extremely complex to understand. There are many proprietary technologies at play and these companies really want to make it very hard to reverse engineer. x86 is also a variable-length ISA, making decoding more complex than RISC architectures. x86 decoding may warrant itself its own article, so will not be discussed here. Instead, we will use Microprocessor without Interlocked Pipelined Stages (MIPS) in running examples and diagrams.

# Single-Cycle Processor
These are very basic processors that nobody uses nowadays outside of teaching examples. Each instruction gets executed in one cycle, which may seem great, but cycle times are horrendously long. Consider the following delays:

- Instruction memory access: 100 ps
- Data memory access: 350 ps
- Register file read: 200 ps
- Register file write: 175 ps
- ALU execution: 150 ps

Single-cycle processors determine the clock cycle time by the sum of all delays. So even though a instruction like `addu r1, r1, r2` never uses data memory, it still takes 975 picoseconds to complete.

<img width="825" height="601" alt="image" src="https://github.com/user-attachments/assets/fcc3b2ba-c627-4519-9fbd-82b12f523c44" />

The only upside of single-cycle processors are their simple designs as seen from the above diagram. That is why they are primarily used in classrooms. Their inefficiency would not make them perform well on any real workloads.

# Pipelined Processor
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

# Out-of-Order Processor
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

## Register Renaming
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

Depending on the specifics, all of the above instruction patterns never actually get executed, making them near zero-cost. Move elimination is a similar idea. Any instructions that effectively copy the contents of two registers can be eliminated by renaming. Consider the following:

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
The last major responsiblity of the rename stage is to allocate an ROB entry. The re-order buffer is a very important structure responsible for ensuring instructions get retired correctly. Essentially, each entry contains:

- *Destination architectural register #* - this allows the commit stage to know which architectural register the instruction writes to.
- *Destination physical register #* - this register holds the actual computed result generated by the functional units.
- *Is entry ready?* - this is a bit representing if the entry is ready to be committed. If the results are not ready, then this is set to 0.

To allocate a new ROB entry, the rename stage takes the instruction and extracts the destination architectual registers and the destination physical registers. Except for zeroing idioms and move elimination, the ROB entry is marked as **not** ready. Zen 5 cores have ROBs with 448 entries.

## Instruction Dispatch
The renamed instructions get added to a queue and sent along to the dispatch stage. This stage is responsible for scheduling instructions to different functional units by keeping track of a scoreboard:

phys reg | status
--- | ---
`t1` | ready
`t2` | pending
`t3` | pending
`t4` | ready
`t5` | ready
... | ...

The scoreboard tells the processor which physical registers have already been written back to. The dispatch stage uses this information to check the instruction queue for instructions to "wake up." For example, the dispatcher can tell `add  t2, t1, t5` to proceed but not `or  t6, t2, t5` because `t2` is still marked as pending. The dispatch stage also keeps track of which functional units are free or in progress. If all functional units are occupied, the processor stalls.

## Functional Units
In modern microarchitectures like the Zen 5, there are a few types of components that actually carry out the execution:
- *Arithmetic Logic Units* - these components perform standard mathematical operations on operands.
- *Load Units* - these components interface with the cache to read from data memory.
- *Floating Point Units* - these components deal with floating point numbers. They also deal with vector operations and SIMD instructions, a topic we may cover in another article.
- *Address Generation Units* - their sole job is to compute effective memory addresses for loads and stores.

There may be some specialized functional units not listed, but these are the common ones. After execution, functional units can broadcast results thorugh the bypass network or common data bus. This allows forwarding to be done.

## Writeback Stage
The writeback stage takes the result from functional units and writes it into the corresponding physical register. It also updates the ROB entry's ready bit to be 1, indicating that the result can be committed. After the result is written, this stage additionally updates the scoreboard so the dispatcher knows which physical registers have results available.

## Commit Stage
This is the last stage to process the instruction. Even though the instructions were actually executed out-of-order, the commit stage needs to retire them *in order*. Otherwise, the program's flow from an external view would appear wrong, and debugging it would be a nightmare. The whole point of microarchitecture is to make optimizations without breaking the ISA's guarantees. The commit stage selects the oldest ROB entry and retires it using the following steps:
1. Write the result from the ROB's destination physical register to the destination architectural register.
2. If the instruction stores data to memory, actually write the result to the cache here. This is a common pattern where AGUs compute the effective store address, but not actually store it until the instruction is ready to commit. The reason lies in the fact that cache updates are sequential, meaning writing to them out-of-order would be a memory consistency violation.
3. Recycle the physical register into the free list after writing to the architectural register.
4. Retire the ROB entry and clear it.

## Full OoO Example
Here is a full example of how an OoO core might execute this program for two iterations:

```
  add   r7, r0, r0
loop:
  add   r11, r7, r1
  add   r12, r7, r5
  beq   r7, r6, end
  lw    r10, 0(r11)
  lw    r11, 1(r11)
  sub   r11, r10, r11
  sw    r11, 0(R12)
  addi  r7, r7, #2
  beq   r0, r0, loop
end:
```

Program after register renaming:

```
  add   t2, t1, t1
loop:
  add   t4, t2, t3
  add   t6, t2, t5
  beq   t2, t7, end
  lw    t8, 0(t4)
  lw    t9, 1(t4)
  sub   t10, t8, t9
  sw    t10, 0(t6)
  addi  t11, t2, #2
  beq   t1, t1, loop
loop:
  add   t12, t11, t3
  add   t13, t11, t5
  beq   t11, t7, end
  lw    t14, 0(t12)
  lw    t15, 1(t12)
  sub   t16, t14, t15
  sw    t16, 0(t13)
  addi  t17, t11, #2
  beq   t1, t1, loop
```

Register alias table after renaming:

arch reg | phys reg
--- | ---
`r0` | `t1`
`r1` | `t3`
`r5` | `t5`
`r6` | `t7`
`r7` | ~~`t2`~~, ~~`t11`~~, `t17`
`r10` | ~~`t8`~~, `t14`
`r11` | ~~`t4`~~, ~~`t9`~~, ~~`t10`~~, ~~`t12`~~, ~~`t15`~~, `t16`
`r12` | ~~`t6`~~, `t13`

Pipeline diagram of back end execution engine (assuming 2-wide superscalar processor):

cycle # | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ---
`add   t2, t1, t1`   | D | E | W | C
`beq   t1, t1, loop` | D | E | W |  |  |  |  |  |  | C
`add   t4, t2, t3`   |   | D | E | W | C
`add   t6, t2, t5`   |   | D | E | W | C
`beq   t2, t7, end`  |   |   | D | E | W | C
`lw    t8, 0(t4)`    |   |   | D | E | W | C
`lw    t9, 1(t4)`    |   |   |   | D | E | W | C
`addi  t11, t2, #2`  |   |   |   | D | E | W |  |  | C
`sub   t10, t8, t9`  |   |   |   |   | D | E | W | C
`add   t12, t11, t3` |   |   |   |   | D | E | W |  |  | C
`sw    t10, 0(t6)`   |   |   |   |   |   | D | E | W | C
`add   t13, t11, t5` |   |   |   |   |   | D | E | W | | | C
`beq   t11, t7, end` |   |   |   |   |   |   | D | E | W | | C
`lw    t14, 0(t12)`  |   |   |   |   |   |   | D | E | W | | | C
`lw    t15, 1(t12)`  |   |   |   |   |   |   |   | D | E | W | | C
`addi  t17, t11, #2` |   |   |   |   |   |   |   | D | E | W | | | | C
`sub   t16, t14, t15`|   |   |   |   |   |   |   |   | D | E | W | | C
`beq   t1, t1, loop` |   |   |   |   |   |   |   |   | D | E | W | | | C
`sw    t16, 0(t13)`  |   |   |   |   |   |   |   |   |   | D | E | W | C

# Branch Prediction
So far, we have been talking about improving performance through instruction execution. At a certain point, even OoO processors hit a limit in speedup. That is why modern CPUs do something dramatic: they try to predict the future using a technique called speculative execution. One common category that CPUs speculate on is control flow. These are ubiquitous in modern programming: every conditional, loop, and function uses control flow. The general term in microarchitecture is branching, as in code execution paths can branch at certain instructions. Branch instructions cannot be executed out-of-order in the traditional sense because the CPU cannot know the outcome of where to redirect the program counter *until* the instruction runs. Therefore, the CPU has to guess if the branch is either taken or not taken. If that guess is wrong, it will be very detrimental as the CPU has to rollback the wrong branch. However, branch predictors are specialized units that try to minimize guessing wrong as much as possible.

## Static Predictor
This type of predictor operates on a static rule:

> If branch jumps *forward* (target > PC), do not take; else backwards taken.

The reason why this is usually the rule is because of loops. If the branch instruction jumps to earlier code (backwards), chances are it is a loop. Accordingly, we should take the branch since most likely the loop (backwards) repeats more than the exit condition (forward). Nevertheless, this basic rule yields fairly mediocre results, which is why it is not seen in modern microarchitectures.

## 1-Bit History Predictor
The premise of this one is simple: repeat the last prediction. This gives our predictor some history. The rationale behind history-based predictors is that conditionals usually aren't decided in isolation. Usually, they need prior state to decide whether or not a branch should be taken. A 1-bit predictor just remembers the last outcome:

<img width="825" height="445" alt="image" src="https://github.com/user-attachments/assets/3b3d556d-be58-4671-a50b-dc3191a8cb81" />

These predictors aren't very good either since two branches could have the same hashed PC. Nothing in the table tells us about this. Adding tags would make the table much larger and slower (almost becomes a cache).

## Markov Predictor
These predictors utilize Markov chains to model branches as a stochastic process. Usually a table keeps track of the pattern seen, the next bit, and frequencies:

pattern | next bit | frequency count
--- | --- | ---
00 | 0/1 | 0/0
01 | 0/1 | 2/1
10 | 0/1 | 0/3
11 | 0/1 | 1/0

## Bimodal Saturating Counter
Now we move on to 2-bit predictors. This predictor uses a state machine to keep track of predictions:

<img width="825" height="708" alt="image" src="https://github.com/user-attachments/assets/3504e660-7bce-4d27-aa91-2b0f5a9e737d" />

The predictor initializes to weakly not taken. Each time it observes a branch taken, it transitions closer to strongly taken, reinforcing its decision. The opposite is also true for not taken. This helps the branch predictor "learn" patterns given what it has seen before.

## Local Predictor
It seems like storing history is the right way to go about predicting branches. The local predictor is a two-level indexing scheme where the program counter is used to index into a pattern history table. The PHT contains some patterns of taken/not taken seen around the PC (hence local). The pattern history table maps to a branch history table where the bimodal counter states from above are used. Here is an example showing the second iteration of a pattern:

<img width="825" height="411" alt="image" src="https://github.com/user-attachments/assets/e17dfd66-0a0a-437a-9401-9f0ff03e4f74" />

These local predictors could keep going, but structures are finite. There is not a set limit on the number of potential branches and patterns. This could cause some conflicts:

- *Constructive aliasing* - two branches strengthen each others' biases.
- *Destructive aliasing* - two branches weaken each others' biases.
- *Neutral aliasing* - two branches do not have a particular effect on each other.

## Correlating Predictor
We can level up the local predictor by combining it with global history. In fact, *gshare* is a branch predictor that combines a global history register and the PC. Correlating predictors roughly look like this:

<img width="747" height="425" alt="image" src="https://github.com/user-attachments/assets/8344c400-f80c-4b6d-b797-8708efeb7bd7" />

Here is how they work:

1. Access the global history register.
  - The PC contains the instruction address of the branch.
  - The GHR contains the pattern history of the last few branches that we have executed.
2. Use the pattern in the GHR to index into the BHT.
  - Make prediction using the BHT entry.
  - BHT entry contains the branch bias (strongly/weakly taken/not taken).
3. When the actual outcome becomes available...
  - `GHR <<= 1`: this will effectively remove the oldest history entry.
  - Replace the LSB of the GHR with the actual branch outcome.

## Tagged Geometric History Length Predictor
The predictors that we have talked about so far are fundamental, but not what is used today. TAGE is a modern predictor that uses multiple PHTs indexed by GHRs of different lengths. It intelligently allocates PHT entries to different branches based on their history requirements.

<img width="825" height="569" alt="image" src="https://github.com/user-attachments/assets/e21d2523-6eff-4fa2-9774-a722c93b8faa" />

Advantages of TAGE:
- It will choose the best history length to predict each branch.
- Consequently, it also enables long branch history lengths.

Disadvantages of TAGE:
- It is complex to implement in hardware.
- It need to choose a good hash function and table sizes to maximize accuracy and minimize latency.

## Perceptron Predictors
This very modern and advanced prediction technique uses neural networks to predict whether or not a branch will be taken. Unlike traditional predictors that rely on history patterns, the perceptron predictor learns and adapts based on past outcomes and the GHR, similar to how perceptrons update in machine learning.

<img width="527" height="150" alt="image" src="https://github.com/user-attachments/assets/c4d85cb9-09ee-4af0-bbf2-ff84183a756d" />

Each branch is associated with a perceptron carrying a set of weights. Each weight corresponds to a bit in the GHR. This approach represents how much the bit is correlated with the direction of the branch.

## Branch Target Buffer
We talked a lot about how to predict the outcome of a branch, but the other part of this is predicting where that branch would take us. The branch target buffer is a cache that stores predicted addresses given our current PC:

<img width="825" height="198" alt="image" src="https://github.com/user-attachments/assets/9e6c544d-6099-45f9-aba1-b1ad6c9445b4" />

The branch target buffer uses a similar tag-index-offset scheme seen in caches. Instead of passing the memory address, we break the PC down into its appropriate tag, index, and offset instead.

## Return Address Stack
A similar variant of branch target buffers exists for function calls. Return address stacks are structures used to predict where return instructions would go. Here is a simple 4-entry RAS:

<img width="825" height="408" alt="image" src="https://github.com/user-attachments/assets/be574b83-324a-40cd-a3d4-0cf82f620a74" />

On a `call` instruction, we increment the index and save return address in that slot. On a `ret` instruction, we read prediction from the index and decrement it. *Note: this is a different structure than the regular call stack, which also has return addresses. That stack lives in RAM while this is baked inside the CPU internals.*

## Modern Speculation
Real branch predictors like the ones found inside Zen 5 are often proprietary. After all, speculation is one of the main elements that drive competition in the processor market. Designing a fast CPU requires it to guess many times and close to correctly each time. Regardless, here is what a modern CPU fetch stage might look like:

<img width="816" height="576" alt="image" src="https://github.com/user-attachments/assets/184d6900-4e8c-4cb8-9f2a-d9859882d8b6" />

Besides predicting branches, modern processors also have a ton of more advanced ways to speculate:
- *Instruction length prediction* - guesses how big the next instruction is so the CPU can read it efficiently.
- *Loop stream detection* - spots loops and reuses already-fetched instructions instead of fetching them again.
- *Load/Store address prediction* - predicts where memory reads/writes will go before the calculation finishes.
- *Memory disambiguation* - guesses whether a memory read depends on earlier writes so it can run safely in parallel.
- *Value prediction* - guesses the result of an instruction before it actually runs.
- *Cache hit/miss prediction* - predicts whether needed data is already in cache or must be fetched from memory.
- *Way prediction* - predicts which part of the cache likely holds the data to speed up lookup.
- *Unit criticality prediction* - identifies which operations matter most for performance so they get priority.
- *Phase prediction* - detects “modes” of program behavior and adjusts CPU strategy accordingly.

# Caching
One major bottleneck of modern computer architecture is relatively slow speed of random-access memory. Now that we have our blazingly fast OoO core doing speculation, taking hundreds of cycles to access DRAM just is not going to cut it anymore. Therefore, we need to start caching data so the CPU does not stall waiting for more to come. If you think about it, RAM itself acts as a cache for data on the disk, which is *even slower*. The concept of caching is nothing new, and performance gains have been shown in many applications. That is why CPUs dedicate so much space to the cache. At a certain point, when the functional units cannot be meaningfully optimized further, the rest of the space goes to the cache. There are a few goals that make CPU caches different from other types of caches:

- *Spatial locality* - we want to cache data near each other in RAM. If a program accesses data at a certain address, there is a high likelihood that neighboring data will get accessed as well.
- *Temporal locality* - we want data that was recently accessed to stay in the cache longer. Loops are some of the ways the same piece of data gets accessed by the CPU many times.

## Placement Policies
If we want to maximize temporal and spatial locality, we can split the memory address into a particular format consisting of three parts: address = [tag | index | offset]

- *Tag* - this number represents the rest of the address, after you have taken away the index and offset.
- *Index* - this number represents the set the block occupies.
- *Offset* - this number determines the position of the byte in a block.

To best model how each type of cache behaves, concrete examples will be shown instead of theoretical properties.

### Direct-Mapped Cache
Direct-mapped caches are the simplest type of CPU caches. If $i$ bits contribute to the index, and $f$ bits contribute to the offset, then the cache is:

set | entry
--- | ---
0 | [valid bit] / [tag bits] / [block of $2^f$ bytes]
1 | [valid bit] / [tag bits] / [block of $2^f$ bytes]
2 | [valid bit] / [tag bits] / [block of $2^f$ bytes]
... | ...
$2^i-1$ | [valid bit] / [tag bits] / [block of $2^f$ bytes]

effective data capacity $= 2^{i+f}$ bytes

### Set-Associative Cache
Set-associative caches introduce $w$ ways, which serve as columns to our table:

set | way 0 | way 1 | ... | way $w-1$
--- | --- | --- | --- | ---
0 | [valid bit] / [tag bits] / [block of $2^f$ bytes] | [valid bit] / [tag bits] / [block of $2^f$ bytes] | ... | [valid bit] / [tag bits] / [block of $2^f$ bytes]
1 | [valid bit] / [tag bits] / [block of $2^f$ bytes] | [valid bit]/[tag bits] / [block of $2^f$ bytes] | ... | [valid bit] / [tag bits] / [block of $2^f$ bytes]
... | ... | ... | ... | ...
$2^i-1$ | [valid bit] / [tag bits] / [block of $2^f$ bytes] | [valid bit] / [tag bits] / [block of $2^f$ bytes] | ... | [valid bit] / [tag bits] / [block of $2^f$ bytes]

effective data capacity $= w \times 2^{i+f}$ bytes

### Fully Associative Cache
This type of cache only has one set:

set | way 0 | way 1 | ... | way $w-1$
--- | --- | --- | --- | ---
0 | [valid bit] / [tag bits] / [block of $2^f$ bytes] | [valid bit] / [tag bits] / [block of $2^f$ bytes] | ... | [valid bit] / [tag bits] / [block of $2^f$ bytes]

effective data capacity $= w \times 2^f$ bytes

## Eviction Policies
There are several methods to decide which way gets evicted when a set is full and all ways are occupied:

- *Random* - this policy picks a random way in the range $[0, w)$ and evicts the block. This was actually used in ARM Cortex-R series processors.
- *FIFO* - with this algorithm, the cache behaves like a FIFO queue; it evicts blocks in the order in which they were added, regardless of how often or how many times they were accessed before.
- *LRU* - this approach evicts the least recently used block. Any time a way in a particular set is accessed, it switches the LRU to the way that was not accessed for the longest time. It accomplishes this using extra metadata:

set | way 0 | way 1 | LRU
--- | --- | --- | ---
0 | [valid bit] / [tag bits] / [block of $2^f$ bytes] | [valid bit] / [tag bits] / [block of $2^f$ bytes] | 0 (way 1 was just accessed)
... | ... | ... | ...
$2^i - 1$ | [valid bit] / [tag bits] / [block of $2^f$ bytes] | [valid bit] / [tag bits] / [block of $2^f$ bytes] | 1 (way 0 was recently accessed)

## Write Policies
How to keep RAM and cache mirrored?
- *Write-through* - all writes reflect in both cache and RAM.
- *Write-back* - writes only go to cache and not RAM immediately. Modified cache blocks are written back to RAM when the block is replaced. A dirty bit tracks if the block is modified relative to RAM.

Make room in cache when a write results in a miss?
- *Write-allocate* - on a miss, bring written block into cache.
- *Write-around* - on a miss, bypass the cache completely and the write goes directly to RAM without loading the block into the cache.

You can mix these policies by choosing one from the first grouping and another from the second. Most modern caches are write-back and write-allocate.

## Cache Hierarchy
There are different levels of caches depending on microarchitecture. Introducing several levels of caches mean there exists inclusivity policies. Inclusive caches mean that larger caches (closer to RAM) must include all blocks present in the smaller caches (closer to execution). Exclusive caches mean that blocks in one level must not be present in another. Non-inclusive caches mean that inclusivity is not guaranteed but not necessarily exclusive. Each level serves a different purpose:

<img width="825" height="403" alt="image" src="https://github.com/user-attachments/assets/c8178779-ca56-43c6-a3b0-5ac647ef648a" />

1. The *level 1 (L1)* cache is split into an instruction cache and a data cache. The fetch stage reads from L1i and the memory stage accesses L1d. Modern CPUs hold virtual addresses in their registers and rely on the translation lookaside buffer to cache physical addresses. Paged memory deserves its own article but for the purposes of this one, L1 caches are usually virtually indexed and physically tagged (VIPT), meaning the index bits come from the virtual address, but the tag is derived from the physical address (requires TLB).
2. The *level 2 (L2)* cache is unified in most designs. It is a per-core cache that helps support the L1 cache and is physically indexed and physically tagged (PIPT), meaning it completely operates on physical addresses.
3. The *level 3 (L3)* cache is usually the last-level cache. It is shared between cores and serves as a general cache for the entire chip.
4. *Victim caches* are fully associative caches that sit in between two levels of cache (usually between L1 and L2). When a miss occurs, evicted blocks are placed in this cache to give it a "second chance." This is best for useful blocks that are evicted not because they have gone cold, but because the main cache is out of space.

### Cache Coherence
The L3 cache has a unique problem: if multiple cores can access the same memory location, how can we ensure correctness? There are two techniques that tackle this:

- *Directory-based* - Have a logically-central directory that keeps track of where the copies of each cache block reside. Caches consult this directory to ensure coherence. On read, set the cache's bit and arrange the supply of data. On write, invalidate all caches that have the block and reset their bits.
- *Bus snooping* - Each cache broadcasts its read/write operations on a common bus. Then, all caches snoop all other caches' read/write requests and keep the cache coherent. Each cache block has "coherence metadata" associated with it.

Here is the metadata that each block contains:
- *Modified* - cache block is the only cached copy and is dirty.
- *Shared* - cache block is one of potentially several cached copies and is clean.
- *Exclusive* - cache block is the ONLY cached copy and is clean.
- *Invalid* - cache block is not present in this cache.

The cache controller can transition between metadata states using this state machine:

<img width="825" height="477" alt="image" src="https://github.com/user-attachments/assets/fd16bd91-52b4-4b2d-85ee-88a14f9b4ec6" />

# Zen 5 Specs
After going through how superscalar OoO cores speculate and several types of caches and their purpose, you hopefully have the knowledge to understand the majority of this diagram:

<img width="825" height="556" alt="image" src="https://github.com/user-attachments/assets/2a245d44-9685-4a97-afc8-6a679b9ef3af" />

We covered many topics related to the microarchitectural design of Zen 5, but some notable advanced topics *not* covered in this tutorial:
- *Indirect Target Array* - predictor structure that caches likely targets of indirect branches (function pointers, virtual calls, etc.) to avoid misprediction penalties.
- *BTB Levels* - multiple tiers of branch target buffers that store recent branch destinations at different capacities/latencies to improve prediction coverage and speed.
- *ITLB* - caches address translations for instruction fetches to avoid page table walks.
- *Decoder Internals* - ISA-specific hardware logic that translates instructions into internal micro-operations (μops), often handling variable-length decoding and instruction fusion.
- *μOp Cache* - cache of already-decoded micro-operations that lets the CPU bypass the decode stage and fetch μops directly for faster front-end throughput.
- *Taken Branch Buffer* – a small structure that quickly supplies targets for recently taken branches to keep the fetch pipeline from stalling.
- *Forwarding Network* – internal bypass paths that route execution results directly between pipeline stages to avoid waiting for register writeback.
- *Load/Store Queues* – buffers that track in-flight memory operations, enforce ordering rules, and enable memory disambiguation and speculation.
- *FP/Vector Operations* – execution units and pipelines that handle floating-point and SIMD instructions for parallel numerical computation.
- *DTLB* – caches address translations for data pages.
- *Miss Address Buffer* – a structure (often the (M)iss (S)tatus (H)olding (R)egister) that tracks outstanding cache misses and coordinates data fills when memory responses return.
- *Infinity Fabric* – high-speed interconnect used in AMD systems to link CPU cores, caches, memory controllers, and other components.

Computer microarchitecture is a broad and evolving area, and this overview only covers a small subset of the concepts involved. In practice, real designs vary widely and are tightly linked to performance, power efficiency, and cost, with decisions at this level shaping everything from product competitiveness to broader shifts in the computing market.
