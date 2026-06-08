---
title: "Discord Forbidden API"
description: "Automated Discord message synchronization toolchain with REST API, live watchdog, and local storage"
date: 2026-06-07
image: "/blog/images/project-placeholder.svg"
badges:
  - "Python"
  - "Flask"
  - "API"
  - "DevOps"
tags:
  - "dev"
  - "backend"
  - "api"
  - "devops"
links:
  - icon: fab fa-github
    url: https://github.com/RajPorus19
showInHome: true
---

## 📌 Summary

A self-hosted toolchain that syncs Discord channels to local storage, exposes a REST API for reading and sending messages, and notifies you when something new happens — fully automated on a cron schedule.

Built to solve a simple need: **don't miss messages across 12+ channels, even when you're not watching Discord.** It runs 24/7 inside a Docker container, syncing every 5 minutes, and only pings you when there's actually something new.

### 🎯 Goals

- ✅ Automatically sync messages from all Discord channels to local JSONL files
- ✅ Expose a REST API to read, search, and send messages programmatically
- ✅ Fire notifications only on *new* messages — no spam, no noise
- ✅ Run 24/7 with zero manual intervention (Docker + cron)

### 🧰 Stack

- **Python + Flask** — REST API (health, channels, messages CRUD)
- **JSONL** — lightweight append-only message storage (one file per channel)
- **Discord API (self-bot)** — message reading and sending
- **Docker** — containerized deployment with docker-compose
- **Hermes Agent cron** — 5-minute watchdog cycle for new message detection

### 🔧 How it works

```
Discord API  ──→  main.py (sync loop)  ──→  chatlogs/messages_in_*.jsonl
                                                    │
                                                    ▼
                                              app.py (Flask API)
                                           ┌─────────────────────┐
                                           │ GET  /health        │
                                           │ GET  /channels      │
                                           │ GET  /channels/X/   │
                                           │      messages       │
                                           │ POST /channels/X/   │
                                           │      send           │
                                           └─────────────────────┘
                                                    │
                                           watchdog (cron/5min)
                                           ─→ notifies on new messages
```

1. **Sync loop** (`main.py`) polls Discord every 5 minutes and appends new messages to `messages_in_{channel}.jsonl`
2. **Flask API** (`app.py`) serves these files over HTTP — you can curl for the latest messages, or send replies via POST
3. **Watchdog** (cron job) compares last message IDs against stored state; if something is new, it alerts you directly — otherwise stays silent (zero token cost)

### 📖 Lessons Learned

- **Append-only storage is underrated.** JSONL gives you fault-tolerant, grep-able message history without a database. If the container crashes, you lose nothing.
- **Watchdog silence matters as much as alerts.** A cron job that only speaks when there's news is more useful than one that says "nothing new" every 5 minutes. The `no_agent: True` pattern (script runs, stdout decides if anything gets delivered) saves tokens and attention.
- **Apostrophes are the enemy of inline curl.** Learned the hard way: always write your JSON payload to a temp file and use `-d @file` instead of `-d '...'`. Shell eats single quotes faster than you think.
- **Stateful vs stateless matters even in simple tools.** The watchdog stores last-seen message IDs per channel in a state file — without it, every run would re-notify old messages.
