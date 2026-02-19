---
title: Datasins - DevOPS, Security, AI/ML, Data & Software Engineering Management Guides
description: Field guides on infrastructure, software, data engineering, DevOPS, AI/ML, and systems. Minimal theory. Maximum signal.
---


# DevOps & MLOps<br>Cyber Security<br>Data Engineering<br>Machine Learning<br>Artificial Engineering<br>Software Engineering Management

This repository is public engineering notebook.
Just fixes, configs, failures, guides and working solutions.
If something is here, it means it broke in real life and had to be solved properly.

Short, practical, directly applicable technical notes.
Most of them are written right after solving production issues.
No beginner tutorials. These are operator notes.

## Who am I

I'm Datasins

- Software developer since 2005
- Director of Software Engineering
- Infrastructure, backend, and systems focused

[GitHub](https://github.com/dsins) • [Insta](https://instagram.com/datasins) • [X](https://x.com/datasins) • [Website](https://www.ugurakcil.com)

## Index

### Devops

- [CyberPanel + SnappyMail: spam goes to Junk](./devops/mail/cyberpanel-snappymail-spam-to-junk.md)
- [Rclone Backup to Google Drive: encrypted, cron, security](./devops/backup/rclone-gdrive-backup.md)

## Philosophy

Machiavellianism

## Warning

These notes assume:

Linux familiarity • root access • production responsibility

They are not beginner guides.

## License

MIT & Use at your own risk.

## End

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



