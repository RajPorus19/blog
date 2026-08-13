---
title: "Thermite SIMD — write your kernel once, let Rust melt every CPU you own"
description: "Thermite is a portable SIMD library that compiles one generic Rust kernel to every instruction set and vector width, and computes its own derivatives as a bonus. After a six-year rewrite, it might be the most interesting single-machine HPC project in Rust."
date: 2026-08-13
translationKey: "2026-08-13-thermite-simd"
image: "/images/2026-08-13-thermite-simd.svg"
tags:
  - "rust"
  - "simd"
  - "hpc"
  - "performance"
  - "open-source"
mathjax: false
---

## "Melt your CPU" is a joke. The library is not.

I have a soft spot for projects whose tagline is slightly unhinged. Thermite SIMD, a Rust library that popped up on r/rust this week, describes itself with four words: *"Melt your CPU."* The README then immediately clarifies that this is "just a fun tagline, obviously," and that the real goal is "to get as much useful work out of one [CPU] as it can physically do."

That tension — the joke and the dead-serious engineering underneath it — is exactly what the project is about. And I want to talk about it, because I think Thermite is doing something genuinely rare: it's building **single-machine HPC** tooling, in a world that has spent the last decade convincing itself that the only interesting problems live on clusters.

The pitch is deceptively simple. You write a numeric kernel **once**, generic over a small trait hierarchy. That one function compiles to high-performance code for every instruction set at every vector width — AVX2, SSE2, NEON, WASM SIMD128, you name it. Unchanged, the *same function* also computes its own derivatives, runs in double-double precision, or evaluates over the complex plane.

Three meanings, one function body, zero edits. That sentence alone made me sit up.

---

## The kernel that doesn't know what a CPU is

Here's the canonical example from the README, slightly trimmed:

```rust
#[thermite::dispatch(V)]
fn gaussian<V: FloatVector + TranscendentalMath>(x: V) -> V {
    (-(x * x)).exp()
}
```

That's the whole kernel. Look at what it *doesn't* name: no instruction set, no lane count, no element type, no `target_feature`, no `unsafe`. It says "negate x squared, then exponentiate," and nothing else.

What you instantiate it with decides what you get. Feed it a plain SIMD vector and you get a vectorized `exp`. Wrap it in `thermite_dual`'s multidual type and the same function produces the value *and* its gradient in one pass. Wrap it in `thermite_compensated` and it evaluates to a ~106-bit significand. Feed it complex numbers and it runs over the complex plane. The kernel never changes.

This is the kind of abstraction that normally costs you performance — and in the SIMD world, the cost is usually *catastrophic*. A generic body that forgets to inline, or that crosses a call boundary without its target features, silently compiles to baseline scalar code. Correct, but uselessly slow. The distance between "generic and safe" and "fast" has historically been a canyon.

Thermite's answer is a proc macro called `#[dispatch]`, and understanding it is the key to understanding the whole project.

---

## `#[dispatch]`: fixing Rust's `target_feature` amnesia

Rust has a well-known footgun around `#[target_feature(enable = "...")]`. If a function carrying those features is ever *not* inlined, it "forgets" them. The optimization evaporates. For a program built at baseline SSE2 that wants to support modern hardware through dynamic dispatch, this is a disaster waiting to happen: either everything inlines always (massive code bloat), or the whole thing degrades into deoptimization the moment the compiler makes a different inlining choice.

`#[dispatch]` rewrites functions so that target features propagate automatically and **statically** across call boundaries. After dead-code elimination, it's effectively zero-cost. It can sit on a function, an `impl` block, a trait, or a whole `mod`, and it's applied to all the `*Math` traits so they stay optimized.

The README is blunt about what happens if you forget it: "A generic SIMD body with no `#[dispatch]` above it compiles featureless: every intrinsic becomes an out-of-line call, with nothing optimized across them. It compiles, it's correct, it's catastrophically slow."

I love that framing because it names a real failure mode of Rust's own feature model instead of hand-waving around it. A lot of "fast in Rust" libraries pretend `target_feature` isn't a minefield. Thermite built the mine-detector.

---

## The trait hierarchy: an interface, not a wrapper

Underneath, Thermite is a `GenericVector -> NumericVector -> FloatVector` hierarchy. A kernel is bounded on the weakest trait it actually needs and names nothing else. The `Vector` type implements that hierarchy for every backend at every lane count — so `f32x8` on AVX2 and `f64x2` on NEON present the same interface and the same semantics.

The clever bit is that the hierarchy is *an interface, not a wrapper over one machine's types*. That's why autodiff duals, compensated arithmetic, and complex numbers can all implement the same traits and reuse every kernel, math library included. Composability falls out of the design instead of being bolted on.

Then there's the backends table, which is worth reading slowly:

| Backend | ISA | Status |
|---|---|---|
| `scalar` | none, 1 lane | Always available — the ragged loop ends |
| `x86_v1` | SSE2 | Complete |
| `x86_v2` | SSE4.2 | Complete |
| `x86_v3` | AVX2 + FMA | Complete, primary optimization target |
| `x86_v4` | AVX-512 | Not implemented yet — runs AVX2 for now |
| `neon` | AArch64 AdvSIMD | Complete |
| `wasm` | SIMD128 | Complete |
| `spirv` | SPIR-V | Experimental, a compile error in release |

All three x86 backends compile unconditionally into a single binary, and `dispatch_dyn!` picks the best one at runtime. One binary, every Intel and AMD chip from the last fifteen years, no rebuild.

I'll be honest about what jumps out: **AVX-512 isn't done.** The README even reserves `avx512-tier1` through `tier4` feature names so the tier names stay stable when the backend lands. That's a project being honest about its roadmap instead of pretending the hard part is finished. I respect that more than a README full of checkmarks.

---

## A real vectorized math library — and a policy system

Most "SIMD math" libraries give you `exp`, `log`, and the trig functions and call it a day. Thermite goes further: `erf`, `gamma`, elliptic integrals (all five Carlson symmetric primitives), Lambert W (both real branches, interleaved so the second is nearly free), orthogonal polynomials, the exponential integral. All vectorized rather than looped over lanes.

What's genuinely unusual is the **policy** system. Every function has a `_p` form taking a compile-time policy, so the same `exp` call is tunable from `UltraPerformance` to `Reference` at the call site, rather than through a second set of function names. You don't pay for accuracy you didn't ask for — and when you *do* ask for it, you get the full thing.

There's also a pile of stuff that shows this is built by someone who actually writes kernels: stream compaction, lane prefix scans, conflict detection, gather/scatter, branchfree dividers, packed fp16/bf16/fp8 storage formats that hold half the bytes while arithmetic runs at full precision. A `SimdSlice` that handles the aligned/unaligned/streaming prologue-epilogue dance so you don't hand-write it per loop.

---

## The ten-year rewrite nobody wants to talk about

Here's the part of the story that matters to me most, because it's the part that's easiest to skip.

The README's history section is short and unusually honest. Thermite was first conceived while working on the Raygon renderer, when the author decided they needed a state-of-the-art SIMD library for SoA algorithms. The first prototype was flawed — "too many leaky abstractions" — and back in 2020, Rust itself was too limited to express what the design needed.

So the author shelved it. In 2025, with mature const generics, a better trait solver, edition 2024, and much wider stable-intrinsics coverage, they redesigned it from the ground up. The line that stuck with me: **"Rust is powerful enough to express all of this safely on stable, but it took me ten years of writing it (and one failed prototype) to figure out how."**

Ten years, one failed prototype, and a language that had to grow up in the meantime. That's the *actual* cost of the abstractions Thermite provides. Nobody writes a README like that unless they've genuinely paid it.

It also explains the project's self-imposed constraints, which read like a manifesto: `no_std` by default, a small dependency tree, a stable toolchain, no build scripts, no codegen step, no FFI in the core crate. The author frames it as targeting "the library I always wanted for single-machine HPC and could never find."

---

## Single-machine HPC is a real, neglected thing

That last line is the thesis, and it's worth sitting with. The numeric-computing world, the author argues, "splits into cluster-scale frameworks on one side and thin SIMD wrappers on the other, with a swamp of software bloat in between."

He's right. We've spent a decade making it easy to rent a thousand GPUs and genuinely hard to squeeze a single beefy workstation to its last drop. Everyone reaches for the cluster because the tooling for the single machine is a mess: intrinsics are unsafe and unportable, the wrappers are thin, and the abstractions leak.

Thermite's bet is that if you make the single machine *feel* as tractable as the cluster — write once, dispatch at runtime, get derivatives and precision for free — then a lot of people who never needed the cluster will stop paying for it. I don't know if that bet pays off, but it's a bet worth making, and I haven't seen anyone else make it this cleanly in the Rust ecosystem.

There's also something quietly subversive here about the relationship between **safety and speed.** The default Rust story is that you can have memory safety, and you can have performance, but the latter means dropping into `unsafe` and hand-rolling intrinsics. Thermite's entire premise is that you can have a *safe, generic, `no_std`* kernel that still melts the CPU — because the compiler does the unsafe plumbing once, correctly, behind a trait boundary.

---

## What I actually think

I'm not going to pretend Thermite is finished. AVX-512 is missing, 32-bit ARM isn't supported, RISC-V V hasn't been started, and the SPIR-V backend is literally a compile error in release builds. The project is 222 stars, three forks, zero open issues — which means it's one person's labor of love, not an ecosystem.

And I love it precisely because of that. This is the kind of project that only exists because someone needed a tool, spent a decade learning how to build it, and then just... built it. No company behind it, no growth round, no "we're building the future of compute" landing page. A tagline that says *melt your CPU* and a codebase that can back it up.

The thing I keep coming back to is that ten-year rewrite. We talk endlessly about "rewriting in Rust" as a meme, but Thermite is the rare project where the rewrite was *into* a language that had to become capable of the project first. Const generics, the trait solver, edition 2024, stable intrinsics — these aren't incidental features. They're the load-bearing walls that made the abstraction possible. Thermite is, in a real sense, a receipt for Rust's maturity.

So no, I don't think you should go rewrite your production kernels in Thermite tomorrow. But I think you should read the README, read the guide, and pay attention to what this project is asserting: that the gap between "generic and safe" and "as fast as the silicon allows" is smaller than we've been told, and that closing it is a design problem, not a hardware problem.

If you have one machine and a numerically heavy problem, Thermite is the argument that you deserve better tooling than a cluster-shaped hammer. And if nothing else, the tagline is excellent.

---

*Thermite SIMD lives at [github.com/raygon-renderer/thermite](https://github.com/raygon-renderer/thermite), with the full tour in the `thermite::guide` module. The rewrite branch was pushed this week — which is, I suspect, exactly why it surfaced on r/rust.*
