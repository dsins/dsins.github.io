# Git: Multiple Accounts & SSH Keys via ~/.ssh/config

**Use case:** Multiple GitHub/Bitbucket/GitLab accounts on one machine - personal and work, or accounts across different services - each using a separate SSH key without touching per-project `git config`.

---

## 1. Generate SSH Keys (ED25519)

ED25519 is preferred over RSA - faster, smaller keys, and required by some services going forward.

```bash
ssh-keygen -t ed25519 -C "personal@gmail.com"  -f "~/.ssh/id_ed25519_github_personal"
ssh-keygen -t ed25519 -C "work@company.com"    -f "~/.ssh/id_ed25519_github_work"
ssh-keygen -t ed25519 -C "personal@yandex.com" -f "~/.ssh/id_ed25519_bitbucket_personal"
```

> **macOS tilde bug:** If `~` causes issues, use the full path:
> ```bash
> ssh-keygen -t ed25519 -C "personal@gmail.com" -f "/Users/username/.ssh/id_ed25519_github_personal"
> ```

---

## 2. Add Public Keys to Each Service

Copy each `.pub` file and paste into the service's SSH key settings:

```bash
cat ~/.ssh/id_ed25519_github_personal.pub   # → github.com/settings/keys
cat ~/.ssh/id_ed25519_github_work.pub       # → github.com/settings/keys (other account)
cat ~/.ssh/id_ed25519_bitbucket_personal.pub # → bitbucket.org/account/settings/ssh-keys/
```

GitLab: `gitlab.com/-/profile/keys`

---

## 3. Configure ~/.ssh/config

```bash
nano ~/.ssh/config
```

### Scenario A - One account per service

```
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_github_personal
  IdentitiesOnly yes

Host bitbucket.org
  HostName bitbucket.org
  User git
  IdentityFile ~/.ssh/id_ed25519_bitbucket_personal
  IdentitiesOnly yes
```

Clone and push as normal - no extra steps:
```bash
git clone git@github.com:username/repo.git
```

---

### Scenario B - Multiple accounts on the same service

Use custom `Host` aliases that map to the same `HostName`:

```
Host github-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_github_personal
  IdentitiesOnly yes

Host github-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_github_work
  IdentitiesOnly yes

Host bitbucket
  HostName bitbucket.org
  User git
  IdentityFile ~/.ssh/id_ed25519_bitbucket_personal
  IdentitiesOnly yes
```

Use the alias instead of the real hostname in all git commands:
```bash
git clone git@github-personal:username/repo.git
git clone git@github-work:org-name/repo.git
git clone git@bitbucket:workspace/repo.git
```

For existing repos, update the remote:
```bash
git remote set-url origin git@github-work:org-name/repo.git
```

---

## 4. Verify Connections

```bash
ssh -T git@github.com        # Scenario A
ssh -T git@github-personal   # Scenario B
ssh -T git@github-work       # Scenario B
ssh -T git@bitbucket.org     # Scenario A
ssh -T git@bitbucket         # Scenario B
```

Expected output:
```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## Key Config Options Explained

| Option | What it does |
|---|---|
| `Host` | Alias used in git commands. Can be anything. |
| `HostName` | The actual domain to connect to. |
| `User` | Always `git` for GitHub, GitLab, Bitbucket. |
| `IdentityFile` | Path to the private key to use. |
| `IdentitiesOnly yes` | Prevents SSH from trying other keys. Without this, SSH may fall back to a different key and authenticate as the wrong account. |

---

## Notes

- Per-project `git config user.email` is still needed if commits must show a specific author email - SSH controls authentication, not commit identity.
- To set commit identity per directory (e.g., all repos under `~/work/`), use `.gitconfig` conditional includes:
  ```ini
  # ~/.gitconfig
  [includeIf "gitdir:~/work/"]
    path = ~/.gitconfig-work
  ```
  ```ini
  # ~/.gitconfig-work
  [user]
    name = Work Name
    email = work@company.com
  ```
- Add keys to the SSH agent if prompted for passphrase repeatedly:
  ```bash
  ssh-add ~/.ssh/id_ed25519_github_personal
  ssh-add ~/.ssh/id_ed25519_github_work
  ```
