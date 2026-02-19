# CyberPanel SnappyMail Spam Not Going to Junk Folder

If you're running your own mail server with CyberPanel, you've probably
seen this: SpamAssassin clearly marks emails as spam, but they still
land in Inbox instead of Junk.

You see headers like:

    X-Spam-Flag: YES\
    Subject: \[SPAM\] Something

SpamAssassin is working. But the message still goes to Inbox.

This is not a SpamAssassin problem. It's a delivery routing problem.

CyberPanel's default mail stack marks spam, but it does not move spam.

SpamAssassin only adds headers. It does not decide folders.

Postfix and Dovecot decide delivery.

If Postfix doesn't explicitly route spam to Junk, Dovecot will deliver
it to Inbox.

This is why spam appears in Inbox even though it's marked.

Most tutorials tell you to use Sieve. That works in simple setups, but
in CyberPanel environments it often causes more problems than it solves.
Permission issues, broken delivery, inconsistent behavior across
domains, and hard-to-debug failures.

The clean solution is to let Postfix route spam directly to Junk during
delivery.

## How to Fix It Properly

CyberPanel uses Maildir and the Junk folder is typically:

    Maildir/.Junk/

So the correct target is:

    INBOX.Junk

Follow these steps.

## 1: Create header_checks

Open:

    /etc/postfix/header_checks

Add:

    /\^X-Spam-Flag: YES/ FILTER spamfolder: /\^Subject: \[SPAM\]/ FILTER
    spamfolder:

Save.

## 2: Add spamfolder transport

Open:

    /etc/postfix/master.cf

Add at the bottom:

    spamfolder unix - n n - - pipe\
    flags=DRhu user=vmail:vmail argv=/usr/lib/dovecot/deliver -f \${sender}
    -d \${recipient} -m INBOX.Junk

Save.

## 3: Enable header_checks

Run:

    postconf -e "header_checks = regexp:/etc/postfix/header_checks"

## 4: Reload Postfix

Run:

    postfix reload

Done.

## How It Works Now

Before:

    Internet → Postfix → SpamAssassin → Inbox

After:

    Internet → Postfix → SpamAssassin → Postfix detects spam → Junk

Spam never touches Inbox.

## How to Verify

    Send a GTUBE test email.

Body:

    XJS*C4JDBQADN1.NSBN3*2IDNEN*GTUBE-STANDARD-ANTI-UBE-TEST-EMAIL*C.34X

