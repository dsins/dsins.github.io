<style>
  body {
    background-color: #0d1117; /* GitHub Dark Arkaplan */
    color: #c9d1d9;           /* Açık gri yazı */
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif;
    line-height: 1.6;
    max-width: 800px;
    margin: 0 auto;
    padding: 2rem;
  }
  a { color: #58a6ff; }       /* Link rengi */
  code { background: #161b22; padding: 0.2rem; border-radius: 6px; }
  pre { background: #161b22; padding: 1rem; overflow: auto; }
</style>

---
title: DataSins — Technical Notes
description: Field notes on infrastructure, software, data, and systems. Minimal theory. Maximum signal.
---


# /notes

This repository is my public engineering notebook.

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

"Vibes scale confidence. Experience scales consequences."



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



