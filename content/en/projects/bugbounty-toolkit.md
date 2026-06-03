---
title: "Bug Bounty — Recon Toolkit"
description: "Tools and scripts for bug bounty reconnaissance"
date: 2026-04-10
image: "/images/project-placeholder.svg"
badges:
  - "Python"
  - "Bash"
  - "Security"
tags:
  - "security"
  - "bugbounty"
  - "dev"
links:
  - icon: fab fa-github
    url: https://github.com/RajPorus19
showInHome: true
---

Collection of tools and scripts for **bug bounty reconnaissance** — subdomain discovery, port scanning, hidden endpoint detection.

### Tools included
- **Subdomain scraper** — multi-source collection (crt.sh, Certificate Transparency, etc.)
- **Endpoint extractor** — parse JS to find hidden endpoints
- **Lightweight port scanner** — quick scan of common ports
- **Diff checker** — compare HTTP responses to detect changes

### Stack
- Python + asyncio for parallelized scans
- Bash for glue scripting
- curl/jq for API parsing

### Results
- Automated reconnaissance phase
- More vulnerabilities found in less time
- Reproducible, documented workflow
