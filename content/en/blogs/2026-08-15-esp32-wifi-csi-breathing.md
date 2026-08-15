---
title: "A $4 ESP32 can now see you breathe through walls — and that's both a miracle and a problem"
description: "A new open-source project turns any WiFi router into a contactless baby breathing monitor using Channel State Information and a $4 ESP32. The DSP is elegant, the cause is noble — and the privacy implications are exactly as alarming as they sound. Your walls have never been as solid as you think."
date: 2026-08-15
translationKey: "2026-08-15-esp32-wifi-csi-breathing"
image: "/images/2026-08-15-esp32-wifi-csi-breathing.svg"
tags:
  - "esp32"
  - "hardware"
  - "dsp"
  - "wifi"
  - "privacy"
  - "open-source"
  - "security"
mathjax: false
---

## The README line that made me put down my coffee

There's a GitHub project that surfaced on r/github this week, and it opens with a sentence that reads like it was written by someone who has fully understood the implications of what they built and decided to lead with the facts:

*"Turn any WiFi router into a contactless baby breathing monitor. Just an ESP32 ($4), your existing router, and physics."*

The project is called BabyGuard, it's MIT-licensed, and its premise is this: every WiFi signal bouncing around your home is being subtly, measurably distorted by the rise and fall of your chest. Those distortions are encoded in something called the Channel State Information of each packet — and a four-dollar microcontroller, if you know how to listen, can extract your breathing rate from them. Through walls. Without a camera. Without a wearable. Without touching you.

I've been reading about WiFi sensing for years — it's a whole subfield of research with papers like Wi-Sleep and BreathJunior — but something about seeing it packaged as *a $4 device with a Python backend and 32 passing unit tests* changes the temperature of the thing. This is the moment a technology stops being a research curiosity and becomes something you can build in an afternoon. And that cuts in two directions at once.

---

## How it actually works: your lungs are a radio station

Let me explain the physics, because it's genuinely beautiful and almost nobody outside a niche gets to appreciate it.

When your router talks to a device, the radio waves don't travel in a clean straight line. They bounce off the walls, the floor, the furniture, and — crucially — off *you*. A human body is mostly water, and water does very specific, very predictable things to a 2.4 GHz signal: it reflects it, absorbs it, and slightly changes its phase. When you breathe, your chest moves a few centimeters. That tiny movement is enough to measurably change the way the room's WiFi signals bounce around.

The catch is that "measurably" here doesn't mean what you'd think. The change is microscopic — a fraction of a fraction of a wavelength. But WiFi is a modern enough protocol that the chip can tell you, for every single packet, exactly how the signal arrived: the amplitude and phase across **64 subcarriers** of the channel. That's the Channel State Information, and the ESP32 can read it out for free, on hardware you already own.

So the BabyGuard setup is almost insultingly simple. The ESP32 pings your router fifty times a second. Every response carries those 64 subcarrier readings, each one a slightly different picture of how the signal was disturbed. The chest's rise and fall imprints itself on those readings as a faint, repeating wobble — the same way a seismograph picks up footsteps. The signal was always there. The router has been broadcasting the fact of your breathing for years. You just needed a $4 chip to hear it.

---

## The DSP pipeline is where the real craft lives

Here's the part I actually want to talk about, because this is what separates a gimmick from an engineering project, and it's the reason I find myself genuinely impressed.

The raw CSI signal is a mess. It's buried under noise, thermal drift, the ESP32's own automatic gain control (which causes sudden amplitude jumps that look exactly like breathing if you're not careful), and interference from every other thing in the room. Turning that into a reliable breaths-per-minute number is a classic digital signal processing problem, and the README walks through the pipeline like someone who actually understands why each stage exists:

1. **Hampel outlier removal** — kill the glitches without smoothing away the signal.
2. **Bandpass filtering at 0.3–1.2 Hz** — isolate exactly the frequency band where infant breathing lives (a baby breathes 30–60 times a minute; an adult, 12–20). Everything else is noise.
3. **FFT plus autocorrelation** — estimate the breathing rate two independent ways, so one catches what the other misses.
4. **PCA subcarrier fusion** — instead of picking the best of the 64 subcarriers, take the principal component, which captures the *coherent* motion (the breathing) and rejects the uncorrelated noise.
5. **First-peak detection** — because the autocorrelation of a periodic signal has harmonics, and a naive `argmax` will lock onto one and report *half* the real breathing rate. The author caught this. That's not luck; that's having done this before.

There's more — a **motion rejection** step (a variance-ratio detector that pauses the apnea alarm when someone walks through the room, so a person moving doesn't mask a baby *stopping*), **gain compensation** in the firmware to normalize the AGC jumps, and a **watchdog** that alerts the parents if the ESP32 itself dies, because, in the author's words, *"silent failure is the most dangerous failure mode."*

That last line is the one that told me this project is real. The person who writes "silent failure is the most dangerous failure mode" in a baby monitor README is not someone LARPing as an engineer. They've thought about what happens at 3 a.m. when the thing you're trusting with your child's life just... stops.

---

## The noble reason it exists

And here's where the story gets its heart, because it's easy to be glib about a $4 gadget and miss why someone built it in the first place.

Roughly **3,400 infants die of sudden unexpected infant death in the United States every year** — and a meaningful share of those deaths are the kind a monitoring device might catch in time. The existing options are all bad in one way or another: wearables you strap onto a newborn (uncomfortable, and the very thing a sleep-safety guideline tells you not to put in the crib), cameras (surveillance of your own child, and useless in the dark), and proprietary medical monitors (thousands of dollars, subscriptions, black boxes).

The pitch of BabyGuard is that the fundamental capability — detecting whether a chest is still rising and falling — shouldn't require any of that. It should cost $4, run on hardware you already own, and give you its data in a form you control. The apnea detector watches the breathing waveform, and if the energy drops below a threshold for more than twelve seconds — early warning, ahead of the clinical twenty-second definition — it raises an audible alarm and pushes a notification to your phone.

There's a line at the bottom of the README, under the license, that I'll admit got me: *"MIT — use it however you want. If this saves even one baby, it was worth building."* It's the kind of sentence that would be saccharine if the engineering weren't there to back it up, and infuriatingly moving because it is.

---

## Now the part that should keep you up at night

I've spent five hundred words telling you this is a beautiful, humane, open-source baby monitor. It is. Now let me tell you why it's also the single clearest privacy warning I've read this year.

Everything I just described — the CSI readout, the subcarrier perturbations, the breathing extraction — works on **any** WiFi signal, including the one that passes through your neighbor's wall into your apartment. It doesn't need a camera. It doesn't need a microphone. It doesn't need consent, a warrant, or even line-of-sight. Radio waves pass through drywall like it isn't there, and the very feature that makes BabyGuard a better baby monitor — no camera, no wearable, nothing to point at the child — is the exact same feature that makes it a perfect surveillance tool.

Let me be very concrete about what this means. A $4 ESP32, a Python script, and a laptop is now the full bill of materials for a device that can tell, from outside your home, whether someone is in a room, whether they're moving, roughly where they are, and — with the right processing — whether they're breathing, and how fast. Ten years ago, through-wall human detection was the province of military-grade radar and law-enforcement equipment with a price tag that ended in many zeros. Today it's an afternoon project with a BOM cheaper than the coffee I drank while reading the README.

The relevant research, by the way, goes well beyond breathing. The same CSI technique is described in the README's "beyond baby monitoring" section for **sleep apnea detection, elderly fall detection, room-level presence sensing, stress monitoring, and pet monitoring**. In other words: who's home, where in the house they are, whether they've moved in the last three hours, what their baseline anxiety level is. All of it, passively, from the signal your router has been emitting this whole time.

---

## Privacy through obscurity is dead — it was never alive

There's a temptation to respond to all this with "well, my walls are solid enough" or "who cares, I'm not interesting." I want to push back on both, because they're the same error wearing different clothes.

The first is just physics ignorance. Your walls are not a barrier to 2.4 GHz — they're barely a speed bump. The signal your router produces is, right now, leaving your home, crossing your neighbor's living room, and carrying with it a faint but perfectly legible record of the fact that you are sitting here, breathing, reading this. You didn't opt into that. It's just what WiFi *is*.

The second is the more dangerous one. "I'm not interesting" is the argument people make when a technology is rare and expensive and aimed at high-value targets. It stops working the moment the technology costs four dollars and fits in a drawer. The point of cheap, passive, through-wall sensing isn't that someone is going to point it at *you specifically*. It's that the capability now exists, in bulk, at a price where *everyone* can afford it — landlords, stalkers, jealous partners, "smart" buildings, and yes, companies whose entire business model is knowing where you are and what you're doing. You don't have to be interesting. You just have to be in range.

This is what I mean when I say privacy through obscurity was never really alive. For most of the internet's history, a lot of "privacy" was actually just *impracticality* — surveillance was possible in theory but too expensive to apply to everyone. The history of the last twenty years is the history of that impracticality collapsing, one $4 chip at a time. WiFi CSI sensing is just the newest, and in some ways the starkest, example: the physical world, the inside of your home, the rhythm of your own breathing, has become legible to commodity hardware. There is no un-seeing that.

---

## What I actually think

I want to hold both things at once without collapsing into either, because I think that's the only honest way to think about this project.

BabyGuard is a genuinely good thing. It's exactly the kind of project I keep this blog around to celebrate: one person, a real problem, a four-dollar piece of hardware, a DSP pipeline you can read and understand and audit, and a cause that's hard to argue with. The fact that a parent can now build a contactless apnea monitor for the price of a coffee, from open source, without a subscription or a walled garden, is the open-source ethos working exactly as advertised.

And it's also, simultaneously, a warning that the world we're building makes it trivially easy for anyone to watch anyone through walls. These two facts are not in tension. They're the same fact. The exact property that makes the technology *good* — passive, invisible, cheap, works through walls — is the property that makes it *dangerous*. You cannot have the baby monitor without the surveillance tool. They are the same radio waves, read by the same chip, processed by the same FFT.

So no, I'm not going to tell you to panic, and I'm not going to tell you it's fine. What I'll tell you is to notice the pattern. Every few months now, some elegant open-source project demonstrates that a capability which used to require a nation-state now fits in a breadboard, and we all do the same dance: marvel at the cleverness, briefly feel the chill of the implications, and then go back to assuming our walls are solid. They aren't. They never were. The signal has always been leaving your house. The only thing that changed this week is how cheap it got to read it.

*BabyGuard lives at [github.com/mohosy/baby-monitor-wifi-csi](https://github.com/mohosy/baby-monitor-wifi-csi). It builds on research including Wi-Sleep, BreathJunior, and Espressif's own ESP-CSI framework — the whole field is worth a rabbit hole, if you want to understand exactly how readable your living room already is.*
