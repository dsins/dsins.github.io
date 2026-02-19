---
title: Datasins - Data, DevOPS, Security, AI/ML, Software Engineering Management Guides
description: Field guides on infrastructure, software, data engineering, DevOPS, AI/ML, and systems. Minimal theory. Maximum signal.
---


# Data Engineering, DevOps, Cyber Security, AI/ML, Software Engineering Management

This repository is my public engineering notebook.

Just fixes, configs, failures, and working solutions.

If something is here, it means it broke in real life and had to be solved properly.

---

# Who am I

I'm Datasins

- Software developer since early 2000s
- Director of Software Engineering
- Infrastructure, backend, and systems focused
- [Datasins](https://www.datasins.com)

Links:

- GitHub → https://github.com/dsins  
- Instagram → https://instagram.com/datasins
- DataSins → https://www.datasins.com  
- X → https://x.com/datasins
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

## Devops

- [CyberPanel + SnappyMail: spam goes to Junk](./devops/mail/cyberpanel-snappymail-spam-to-junk.md)
- [Rclone Backup to Google Drive — encrypted, cron, security](./devops/backup/rclone-gdrive-backup.md)

---

# Philosophy

Most technical content online is optimized for clicks.

This repo is optimized for recovery.

When production breaks, you don't need opinions.

You need exact commands that work.

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

```bash
"Vibes scale confidence. Experience scales consequences."
```



⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣴⣾⡇⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⣰⣿⠋⢹⣷⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢠⣿⣦⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⣼⡿⠁⠀⠈⣿⡆⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣠⡿⠁⠙⣿⡄⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⢠⣾⣦⡀⠀⠀⢰⣿⠃⠀⠀⠀⢹⣿⡄⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣴⡟⠁⠀⠀⢹⣿⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⢀⣿⠃⠻⣧⠀⠀⣿⡏⠀⠀⠀⢀⣠⣽⣿⣶⠶⠿⠿⠿⠿⠿⠿⠿⢶⣶⣤⣄⣀⢀⣴⡿⠋⠀⠀⠀⠀⠀⣿⡇⠀⠀⠀⠀⠀⠀⠀⠀⠀
⣼⡏⠀⠀⢹⣧⣸⣿⠁⢀⣤⣾⡿⠟⠋⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠉⠙⠻⢿⣯⣀⠀⠀⠀⠀⠀⠀⣿⣷⠀⠀⠀⠀⠀⠀⣀⣤⣾
⣿⡇⠀⠀⠀⢻⣿⣿⣴⡿⠟⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠻⣷⣄⠀⠀⠀⠀⣿⡟⠀⢀⣠⣴⣾⠿⠛⢹⣿
⣿⡇⠀⠀⠀⠀⣿⣿⠟⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠻⣷⣄⠀⢰⣿⣧⣶⡿⠛⠉⠀⠀⠀⣾⡏
⢹⣇⢰⠀⠀⢠⣿⠏⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⢿⣧⣾⣿⡟⠉⠀⠀⠀⠀⠀⣼⡿⠀
⠘⣿⡈⡆⠀⣾⡟⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⢿⣿⡟⠀⠀⠀⠀⠀⠀⣴⡿⠁⠀
⠀⢹⣧⠸⣤⣿⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⣿⡇⣰⠊⠁⠀⢀⣼⡿⠁⠀⠀
⠀⠀⢿⣇⣹⣿⣷⣶⣤⣀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣀⣠⣤⡄⠀⠀⢹⣿⠃⠀⠀⣠⣿⠟⠀⠀⠀⠀
⠀⠀⠈⣿⣿⠃⢿⡇⠉⠛⠷⣦⣄⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣀⣠⣤⣴⡶⠶⠟⠛⠋⣻⡿⠀⠀⠀⢸⣿⢀⣠⣾⡿⠁⠀⠀⠀⠀⠀
⠀⠀⣴⡿⠁⠀⠘⣿⡄⠀⠀⠀⠉⠛⣷⣼⠀⢰⣤⣴⡶⠾⠟⠛⠋⠉⠁⠀⠀⠀⠀⠀⣰⣿⢃⠀⠀⠀⢸⣿⡿⠟⠁⠀⠀⠀⠀⠀⠀⠀
⠀⢰⣿⠁⠀⠀⠀⠈⢿⣦⣄⣀⣀⣼⠇⠀⠀⠀⠀⠻⣧⣀⠀⠀⠀⠀⠀⠀⠀⢀⣠⣾⣿⠕⠃⠀⠀⠀⣿⡏⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠈⣿⡆⠀⠀⠀⠀⠀⠈⠙⠛⠋⠉⠀⠀⠀⠀⠀⠀⠈⠛⠿⣶⣶⣶⣶⣶⡾⠿⠛⠋⠁⠀⠀⠀⠀⣼⡿⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠘⢿⣦⡀⠀⠀⠀⢀⣤⣦⠀⠀⣀⣀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣼⡿⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠙⢿⣦⣄⠀⠈⠀⢹⣤⠞⠉⠹⣄⣤⠞⠻⣄⣠⠿⡄⢀⡴⠋⠳⣄⠀⠀⠀⠀⢀⣴⡿⠋⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠈⠛⠿⣶⣤⣀⠀⠀⠀⠀⠋⠀⠀⠀⠙⠋⠀⠙⠁⠀⠀⠀⠈⠂⣀⣤⣾⠿⠋⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⣀⠉⠛⠿⣶⣦⣤⣀⣀⠀⠀⠀⠀⢀⣀⣀⣠⣤⣴⣶⡿⠟⠋⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⣠⣴⡿⠛⠷⣶⣤⣄⣉⣽⣿⠛⠛⠿⠿⠟⠛⠛⣻⡿⠋⠉⢿⡇⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⣿⣥⡶⣶⣤⠤⠈⠉⠉⠉⠀⢀⣀⠲⡄⠀⢀⣼⠟⠁⠀⢀⣿⣷⡀⣀⣤⣤⣤⣤⣄⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠘⠟⢠⡿⣡⣦⣤⣤⣤⣤⣼⣟⡛⠻⠿⠿⠟⠋⠀⠀⣠⣾⠋⠹⣿⠟⣉⣡⣄⡉⠻⣿⣆⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠘⣿⡟⠁⠀⠉⠉⠙⡿⢋⡿⠂⣀⣀⣤⣤⣤⣾⠟⠁⠀⢰⣿⠟⠋⠁⠉⢻⡆⢸⣿⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢠⣿⣷⡿⠛⠋⠉⠉⠁⢄⠀⠀⠀⠘⣿⠀⠀⠀⠀⠈⣿⢸⣿⡀⠀⠀⣀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢻⣿⡁⠠⣤⣤⣤⣀⣘⣧⡀⠀⠀⠛⢷⣦⡀⠀⣠⣿⡇⠻⠷⠶⢿⣿⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣴⠿⠷⠀⠀⠈⣿⡏⠉⠉⢻⣆⣀⣤⠄⠻⣷⡀⠻⣿⣄⡀⠀⠀⢸⡇⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣴⣟⣁⠀⠀⠀⣠⡾⠛⠁⠀⠀⠀⠙⣿⡇⠀⠀⠹⣷⡄⠈⠙⠿⣦⣄⣸⡇⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠉⠙⠛⠛⠿⠿⠟⠀⠀⠀⠀⠀⠀⠀⢸⣷⣶⣶⠾⠿⠟⠀⠀⠀⠈⠻⡿⠁⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀



