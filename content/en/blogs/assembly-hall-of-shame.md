---
title: "The Assembly Hall of Shame — or why making CPUs slow is an art form"
description: "A deep dive into the project that finds the slowest single x86 instructions. The current champion: fxrstor64 at 62 seconds. This is hardware poetry."
date: 2026-08-10
translationKey: "assembly-hall-of-shame"
image: "/images/assembly-hall-of-shame.svg"
tags:
  - "assembly"
  - "cpu"
  - "performance"
  - "security"
  - "hardware"
mathjax: false
---

## The project that asks: how slow can a single instruction be?

There's a GitHub repo that's been making the rounds on r/programming this week: [asm-hall-of-shame](https://github.com/xoreaxeaxeax/asm-hall-of-shame), by the legendary [xoreaxeaxeax](https://github.com/xoreaxeaxeax) (the same person behind the *Movfuscator* — a compiler that produces binaries using only `mov` instructions). 476 upvotes and a thread full of hardware nerds losing their minds.

The premise is beautifully simple: find the single slowest x86 instruction you can execute. Not a sequence of instructions. Not a program. **One instruction.** The current champion: `fxrstor64`, clocking in at **198 billion cycles** — or **62 seconds** — on an AMD Ryzen 7 5800H.

For comparison: a normal `nop` takes 1 cycle.

## How you make a CPU crawl for 62 seconds

The strategy behind `fxrstor64` is a masterclass in hardware sadism:

1. `fxrstor64` loads 512 bytes of FPU/MMX/XMM state from a memory address
2. That address is pointed at a **high-latency MMIO region** in the PCIe fabric — not RAM, but a memory-mapped I/O region that's deliberately slow
3. While the load is in flight, a fleet of "hammer cores" pounds a different high-latency MMIO register with tight 4-byte reads, **saturating the PCIe root complex**
4. CPU 0's `fxrstor64` must queue behind all that contending traffic

In other words: you don't just ask the CPU to do something slow. You *engineer a traffic jam* in the PCIe fabric and then push a 512-byte load through it. It's the hardware equivalent of ordering a single coffee at a packed Starbucks during rush hour and paying in pennies.

## The leaderboard tells a story

What makes the leaderboard fascinating isn't just the numbers — it's the narrative arc from simple to deranged:

| Rank | Instruction | Cycles |
|------|------------|--------|
| 27 | `nop` | 1 |
| 26 | `nop16` (long nop) | 20 |
| 25 | `rdtsc` (reference) | 49 |
| 24 | `idiv` | ... |
| ... | ... | ... |
| 🏆 1 | `fxrstor64` | 198,002,498,236 |

You can trace the progression: start with "let's make nothing take longer" (long nops), move to "legitimate but slow instructions" (idiv, rdtsc), then escalate to "instructions that touch external hardware," and finally land on "instructions that touch external hardware while we actively attack the bus from other cores."

There's something deeply satisfying about seeing a leaderboard where the goal is to be *last*.

## Why this matters beyond the meme

It's easy to dismiss this as a fun but useless project. That would be wrong for at least three reasons:

**1. It teaches you how modern CPUs actually work.** Reading about PCIe contention and MMIO regions is abstract until you see a 62-second-long instruction. The leaderboard entries are annotated with strategies that explain *why* each instruction is slow — and in doing so, teach more about CPU microarchitecture than any textbook diagram.

**2. It's adjacent to serious security research.** The same repo author used a spec-violating unaligned `vmovdqu` load (Honorable Mention on the leaderboard) to break System Management Mode — the most privileged execution context on x86 — in a project called [smiiiiiiiiiiiiiiii](https://github.com/xoreaxeaxeax/smiiiiiiiiiiiiiiii). Understanding how to make hardware behave unexpectedly is the foundation of hardware-level exploitation.

**3. It's a reminder that performance isn't just about speed.** We spend so much time optimizing for *minimum* latency that we forget the hardware has fascinating behavior at the other extreme. Sometimes understanding the worst case is more important than optimizing the average case — especially if you're designing real-time systems or hardening against side-channel attacks.

## The rules are what make it interesting

The project has four rules:

1. Only a single instruction is scored — you can set up whatever you want, but only one instruction counts
2. Trapped/emulated/virtualized instructions only time the trap, not the handler (no cheating with `int3`)
3. Instructions must not be interruptible — `rep movs`, `pause`, etc. are disqualified
4. All platforms must be in factory stock configuration — no hardware mods

These constraints force creativity. You can't just trigger a page fault and count the handler time. You can't wire up external hardware to stall the bus. You have to find instructions that are *legitimately* slow on unmodified consumer hardware. The 62-second `fxrstor64` does exactly that.

---

If you've never spent an afternoon staring at Intel's instruction latency tables and thinking "how could I abuse this?", the Assembly Hall of Shame is your gateway drug. The repo is well-documented, the strategies are explained, and it's genuinely educational wrapped in the skin of a shitpost.

That's the best kind of project.
