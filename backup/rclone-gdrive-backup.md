# Rclone Backup to Google Drive - Encrypted, Unencrypted, Cron, and Security Best Practices

This guide explains how to correctly back up your server using rclone,
including encrypted and unencrypted methods, cron automation, scripts,
and security precautions.

------------------------------------------------------------------------

# Important: root_folder_id Behavior

If your rclone remote is configured like:

    remote: gdrive
    root_folder_id: 1AbCdEfGh...

Then this:

    rclone copy /backup gdrive:

is correct.

This is WRONG:

    rclone copy /backup gdrive:mail-backups/

Because you are already inside that folder.

Adding it again creates:

    mail-backups/mail-backups/

or causes errors.

------------------------------------------------------------------------

# Method 1 --- Unencrypted Backup

Simplest method.

Command:

    rclone copy /backup gdrive: -P

Pros:

-   Fast
-   Simple

Cons:

-   Anyone with Drive access can read backup

------------------------------------------------------------------------

# Method 2 --- Encrypted Backup (Recommended)

Create crypt remote:

    rclone config

New remote:

    name: gdrive-crypt
    type: crypt
    remote: gdrive:
    filename encryption: standard
    password: choose strong password

Backup:

    rclone copy /backup gdrive-crypt: -P

Now files are unreadable in Drive.

------------------------------------------------------------------------

# Method 3 --- Scripted Backup

Create script:

    nano /usr/local/bin/backup.sh

Example:

    #!/bin/bash

    DATE=$(date +%Y-%m-%d_%H-%M)

    SOURCE="/backup"

    DEST="gdrive-crypt:"

    LOG="/var/log/rclone-backup.log"

    rclone sync $SOURCE $DEST --log-file=$LOG --log-level=INFO --transfers=4 --checkers=8 --delete-excluded

Make executable:

    chmod +x /usr/local/bin/backup.sh

Run:

    /usr/local/bin/backup.sh

------------------------------------------------------------------------

# Method 4 --- Cron Automation

Edit cron:

    crontab -e

Example:

Daily backup at 03:00:

    0 3 * * * /usr/local/bin/backup.sh

------------------------------------------------------------------------

# Method 5 --- Versioned Backup

Safer approach.

Script example:

    #!/bin/bash

    DATE=$(date +%Y-%m-%d)

    rclone copy /backup gdrive-crypt:$DATE -P

This creates:

    Drive

    2026-02-19
    2026-02-20

------------------------------------------------------------------------

# Security Best Practices

Always use:

Encrypted remote

Never store backup unencrypted in cloud.

Use strong password.

Example:

16+ characters

Store password securely.

------------------------------------------------------------------------

Restrict remote using root_folder_id

Limits access scope.

------------------------------------------------------------------------

Protect config file

Location:

    /root/.config/rclone/rclone.conf

Permissions:

    chmod 600

------------------------------------------------------------------------

Use separate Google account for backup

Never use personal Drive.

------------------------------------------------------------------------

Monitor logs

Example:

    tail -f /var/log/rclone-backup.log

------------------------------------------------------------------------

# Recommended Production Setup

Use:

Encrypted remote

Script

Cron

Versioned backups

Restricted root_folder_id

------------------------------------------------------------------------

# Example Final Command

    rclone sync /backup gdrive-crypt:

------------------------------------------------------------------------
