---
title: "Linux Setup Generator"
description: "A Linux install script generator — pick your distro, click the apps you want, and get a ready-to-run bash script. 291 programs, 7 distros, zero backend."
date: 2026-03-10
image: "/images/linux-setup-gen-banner.svg"
badges:
  - "Hugo"
  - "Vanilla JS"
  - "GitHub Pages"
  - "Bash"
tags:
  - "dev"
  - "frontend"
  - "linux"
  - "tool"
links:
  - icon: fab fa-github
    url: https://github.com/RajPorus19/linux-setup-generator
  - icon: fas fa-globe
    url: https://linux-setup-generator.online/
showInHome: true
---

## Summary

Installing Linux is easy. Reinstalling all your programs after a wipe is a nightmare. You hunt down package names, copy-paste commands, and always forget half of them. **Linux Setup Generator** replaces all of that with a 3-step wizard:

1. Pick your distribution
2. Click the programs you want
3. Copy or download your install script

Everything runs in the browser. No account, no backend, no data collection.

## Why it's useful

- **291 programs** across 69 categories — from terminals to window managers, IDEs, browsers, and audio tools
- **7 supported distributions**: Alpine, Arch, Debian, Fedora, openSUSE, Ubuntu, Void
- **Distro-aware package names** — each program knows which package name to use for your distro (e.g. `chromium` on Arch → `chromium-browser` on Ubuntu)
- **161 custom install scripts** for programs not available in official repos — npm global, cargo, AppImage, builds from source…
- **Automatic dependency resolution** — pick a program, its dependencies are included without you thinking about it

## Tech stack

- **Hugo** — static site, builds in < 5 seconds
- **Vanilla JS** — 136 lines, zero npm dependencies
- **GitHub Pages** — automatic deployment via GitHub Actions
- **No backend** — the entire script is generated client-side by a `ScriptGenerator` class

## The clever bit

The architecture is purely data-driven. Adding a program is creating a folder with a `program.json` (and optionally an `install.sh`). No database, no API, no centralized config. The `ScriptGenerator` reads the JSON injected into the page by Hugo, resolves dependencies with a DFS topological sort, and generates a bash script tailored to your distro.

---

[Read the detailed architecture article →](/en/blogs/devlog-linux-setup-generator/)
