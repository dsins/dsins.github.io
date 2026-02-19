# Rclone Backup to Google Drive - Encrypted, Unencrypted, Cron, and Security Best Practices

This guide explains how to correctly back up your server using rclone,
including encrypted and unencrypted methods, cron automation, scripts,
and security precautions.

## Important: root_folder_id Behavior

If your rclone remote is configured like:

    remote: gdrive
    root_folder_id: 1AbCdEfGh...

Then this:

    rclone copy /backup gdrive

Adding it again creates:

    mail-backups/mail-backups/

or causes errors.

## 1: Unencrypted Backup

    rclone copy /backup gdrive -P

Pros:

-   Fast
-   Simple

Cons:

-   Anyone with Drive access can read backup

## 2: Encrypted Backup (Recommended)

Create crypt remote:

    rclone config

New remote:

    name: gdrive-crypt
    type: crypt
    remote: gdrive:
    filename encryption: standard
    password: choose strong password

Backup:

    rclone copy /backup gdrive-crypt -P

Now files are unreadable in Drive.

## 3: Scripted Backup

    nano /usr/local/bin/backup.sh

Script;

    #!/bin/bash
    
    DATE=$(date +%Y-%m-%d_%H-%M-%S)
    
    DEST=/backup/mailbackup/mail-$DATE
    
    mkdir -p $DEST
    
    echo "Backing up mail data..."
    
    cp -a /home/vmail $DEST/
    
    echo "Backing up postfix..."
    
    cp -a /etc/postfix $DEST/
    
    echo "Backing up dovecot..."
    
    cp -a /etc/dovecot $DEST/
    
    echo "Backing up spamassassin..."
    
    cp -a /etc/spamassassin $DEST/ 2>/dev/null
    cp -a /etc/mail/spamassassin $DEST/ 2>/dev/null
    
    echo "Backing up opendkim..."
    
    cp -a /etc/opendkim $DEST/ 2>/dev/null
    cp -a /etc/opendkim.conf $DEST/ 2>/dev/null
    
    echo "Backing up cyberpanel..."
    
    cp -a /etc/cyberpanel $DEST/ 2>/dev/null
    cp -a /usr/local/CyberCP $DEST/ 2>/dev/null
    
    echo "Dumping MySQL..."
    
    mysqldump --all-databases > $DEST/mysql-all.sql
    
    echo "Saving installed packages..."
    
    dpkg --get-selections > $DEST/packages.txt
    
    echo "Compressing..."
    
    tar -czf $DEST.tar.gz $DEST
    
    echo "Backup complete: $DEST.tar.gz"


Make executable:

    chmod +x /usr/local/bin/backup.sh

Run:

    ./backup.sh

## 4: Cron Automation

Edit cron:

    crontab -e

Example: Daily backup at 03:00:

    0 3 * * * /usr/local/bin/backup.sh

## 5: Versioned Backup

Safer approach.

Script example:

    #!/bin/bash

    DATE=$(date +%Y-%m-%d)

    rclone copy /backup gdrive-crypt:$DATE -P

This creates:

    Drive

    2026-02-19
    2026-02-20

## Security Best Practices

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

## Recommended Production Setup

Use:

Encrypted remote

Script

Cron

Versioned backups

Restricted root_folder_id

# Example Final Command

    rclone sync /backup gdrive-crypt

