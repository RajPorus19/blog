---
title: "Kattruko — Devlog 1: Idea and Product Design"
description: "Why I built a study companion connected to my Obsidian vault, and the design choices that shaped it."
date: 2026-06-09
translationKey: "devlog-1-kattruko"
image: "/images/kattruko-devlog-1.svg"
tags:
  - "devlog"
  - "kattruko"
  - "design"
  - "spaced-repetition"
mathjax: false
---

## The problem: notes you never revisit

I've been growing an Obsidian vault for years. Hundreds of notes on biology, chronobiology, Buddhist philosophy, history — everything I'm passionate about. But there's a silent issue with this kind of vault: **you write, you archive, you forget.**

A note on leptin and ghrelin regulation? Fascinating the day you write it. Two months later, you remember it exists, but you couldn't explain how the two hormones interact. In real life, what you don't use, you lose.

Flashcard apps exist — [Anki](https://apps.ankiweb.net/), Quizlet, etc. But they all share a fundamental flaw: **you have to recreate everything.** Copy your notes into another format, another tool, another database. That's insurmountable friction over time. And once you have 500 notes, it's too late.

The idea behind Kattruko was simple: **what if the app that quizzes you was directly plugged into your vault?**

### The origin of the name

Kattruko (கற்றுக்கொ) means "learn" in Tamil — a Dravidian language from southern India and Sri Lanka. It's a contraction of *katrukol* (to learn, to study). The name came naturally: the app exists to learn. Not to manage tasks, not to generate statistics — to learn. The word is short, it sounds good, and it says exactly what the project does.

---

## Why not an existing solution?

I spent a fair amount of time looking at what's out there. Anki is the gold standard, but:

- **Obsidian to Anki** — plugins exist, but they're fragile, depend on AnkiConnect, and break with every update on either side
- **[RemNote](https://www.remnote.com/)** — all-in-one but closed, no local vault, no data control
- **[Notion](https://www.notion.so/)** — no native spaced repetition, no auto-generated quizzes

The real need is an app that **reads your notes directly**, understands them, and transforms them into study material without any manual work. No off-the-shelf solution does that well.

---

## A "study companion", not a productivity dashboard

Early on, I made a design decision that influenced everything after: **Kattruko is not a productivity tool.** No study time counters, no leaderboards, no aggressive gamification, no streaks that guilt-trip you for missing a day.

It's a study companion. Calm, soothing, showing you what's worth reviewing today without pressure.

This changes everything in the design:

- **No aggressive push notifications** — the app speaks when you open it, not when it decides
- **Suggestions are invitations, not commands** — "here's what would be good to review" not "YOU HAVE 47 OVERDUE CARDS"
- **The design should make you want to stay** — not just be functional

This last point drove the visual direction of the project.

---

## The Matcha Latte philosophy

The palette name says it all: a fresh green, warm tones, an atmosphere that evokes a quiet café and a notebook. I wanted the app to have **personality** — not just a default Material 3 theme.

The resulting choices:

- **Inter typography** — legible, technical without being cold, used by Figma, GitHub. A font that says "serious tool" without saying "hospital"
- **Dual palette (light/dark)** — not a simple color invert. Both modes have their own personality: light feels fresh and airy like a sheet of paper, dark feels deep like a reading lamp at night
- **Documented design system** — every color, every spacing, every font-size has a reason. DESIGN.md is a spec, not a sticky note

The technical challenge: making this design system work consistently on both mobile (native Flutter) and desktop (web Flutter). Material 3 helps, but fine customization means overriding everything correctly.

---

## Why Flutter + Django DRF?

The most important technical choice after the concept itself.

**Flutter for the frontend:**
- Cross-platform without sacrificing quality
- Hot reload for fast design iteration
- [Material 3](https://m3.material.io/) native — the design spec I wanted is literally in the framework

**Django DRF for the backend:**
- Python, the ecosystem I know best
- DRF is mature and well-documented — no surprises
- Relational model is perfect for the domain (notes, flashcards, quizzes, SM-2 scheduling)
- Celery for async tasks without reinventing the queue

The real challenge: **avoiding the "microlith" trap** — a simple app that becomes complex because you over-split it. I kept the backend monolithic (one Django app) with clearly separated service modules.

---

## The hardest challenge: importing notes

The problem that took the most thought: how do you scan an Obsidian vault and extract meaning from it?

Obsidian is just markdown files in a folder. Simple on the surface. But in practice:

- **YAML frontmatter** — some notes have metadata (tags, dates, status), others don't
- **Wikilinks** — `[[Linked note]]` everywhere, needing parsing and resolution
- **Inline tags** — `#tag` in text, different from frontmatter tags
- **Embedded flashcards** — you can write `Q::question` / `R::answer` directly in a note, but how do you detect them without breaking the parser?
- **Binary files** — images, PDFs to filter out
- **Orphan notes** — unlinked files with useful content

The solution: a multi-pass scanner. First pass to list valid files and extract metadata. Second pass to parse content with a proper state machine — not just regex — to distinguish inline flashcards from regular text. Finally, a wikilink resolution pass to build the note graph.

This system was rewritten twice. V1 was too optimistic and broke on complex notes. V2 is more modular, with unit tests on every step.

---

## What's next

The [Devlog 2](/en/blogs/devlog-2-matcha-latte/) talks about the **design system and visual identity** — the palette choices, the typography, and how it all fits into a complete charter.

For now, Kattruko is in closed development. No public repo, no open beta — it's a personal tool built to solve *my* problem. If it helps others someday, great, but that's not the goal today.
