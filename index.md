---
title: Uğur Akçıl — Technical Notes
description: Field notes on infrastructure, software, data, and systems. Minimal theory. Maximum signal.
---


# /notes

This repository is my public engineering notebook.

No storytelling.  
No marketing.  
No fluff.

Just fixes, configs, failures, and working solutions.

If something is here, it means it broke in real life and had to be solved properly.

---

# Who am I

I'm Datasins (@dsins).

- Software developer since early 2000s
- Director of Software Engineering
- Infrastructure, backend, and systems focused
- [DataSins](https://www.datasins.com)

Links:

- GitHub → https://github.com/dsins  
- DataSins → https://www.datasins.com  
- Website → https://www.ugurakcil.com  

---

# What is in this repo

Short, practical, directly applicable technical notes.

Most of them are written right after solving production issues.

Typical format:

Problem  
Cause  
Fix  
Validation  
Rollback (if needed)

No beginner tutorials.

These are operator notes.

---

# Index

## Mail

- [CyberPanel + SnappyMail: spam goes to Junk](./mail/cyberpanel-snappymail-spam-to-junk.md)

## Backup

- [Rclone Backup to Google Drive — encrypted, cron, security](./backup/rclone-gdrive-backup.md)

---

# Philosophy

Most technical content online is optimized for clicks.

This repo is optimized for recovery.

When production breaks, you don't need opinions.

You need exact commands that work.

---

# Expected Content

Examples of upcoming notes:

- Postfix routing behavior deep dive
- Maildir internals
- systemd failure patterns
- Docker production mistakes
- TLS misconfigurations
- Linux debugging workflows
- scaling problems and fixes

---

# Structure

Each note lives in its own directory.

Example:
devops/mail/
devops/backup/
devops/linux/
devops/docker/
security/network
data/scrapping

---

# Warning

These notes assume:

- Linux familiarity
- root access
- production responsibility

They are not beginner guides.

---

# License

MIT

Use at your own risk.

---

# End

If you're here, something probably broke.

Good.

That's how real engineering happens.

