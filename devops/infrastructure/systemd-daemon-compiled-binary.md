# Hosting a Compiled Binary as a systemd Daemon (Rust, Go, C++)

**Use case:** You built a web service in Rust, Go, C++, or any compiled language and need it running persistently on a Linux server — auto-starting on boot, auto-restarting on crash, with proper logging.

---

## 1. Upload the Binary to the Server

```bash
# From your local machine
scp ./web-server user@your-server:/usr/local/bin/web-server

# Or build directly on the server, then move to a standard location
sudo mv ./web-server /usr/local/bin/web-server
sudo chmod +x /usr/local/bin/web-server
```

> `/usr/local/bin/` is the conventional location for manually installed binaries. Alternatively use `/opt/your-app/` for self-contained apps with config files alongside the binary.

---

## 2. Create a Dedicated System User (Recommended)

Never run a service as root. Create a minimal system user with no login shell and no home directory:

```bash
sudo useradd --system --no-create-home --shell /usr/sbin/nologin webserver
```

Set ownership if the binary or working directory needs it:

```bash
sudo chown webserver:webserver /usr/local/bin/web-server
```

---

## 3. Create the systemd Unit File

```bash
sudo nano /etc/systemd/system/web-server.service
```

```ini
[Unit]
Description=My Web Server
Documentation=https://github.com/you/your-repo
After=network.target
# Use After=network-online.target if the service needs actual network connectivity at start

[Service]
Type=simple
ExecStart=/usr/local/bin/web-server --port 5689
WorkingDirectory=/opt/web-server
User=webserver
Group=webserver
Restart=always
RestartSec=5

# Environment variables
Environment=APP_ENV=production
Environment=LOG_LEVEL=info
# Or load from a file (keep secrets out of the unit file)
# EnvironmentFile=/etc/web-server/env

# Resource limits (optional but recommended)
LimitNOFILE=65536

# Prevent the service from writing outside its working directory (optional hardening)
# PrivateTmp=true
# NoNewPrivileges=true
# ReadOnlyPaths=/

[Install]
WantedBy=multi-user.target
```

### Key directives explained

| Directive | What it does |
|---|---|
| `After=network.target` | Wait for network stack before starting |
| `Type=simple` | Process started by `ExecStart` is the main process. Use `Type=forking` if the binary daemonizes itself |
| `Restart=always` | Restart on any exit — crash, OOM kill, etc. |
| `RestartSec=5` | Wait 5 seconds before restarting (prevents tight crash loops) |
| `EnvironmentFile=` | Load env vars from a file — keep secrets out of the unit file |
| `LimitNOFILE=65536` | Raise open file descriptor limit (important for high-concurrency servers) |

### Restart policies

| Value | Restarts on |
|---|---|
| `no` | Never (default) |
| `always` | Any exit — clean or crash |
| `on-failure` | Non-zero exit code or signal only |
| `on-abnormal` | Signals, watchdog timeout, but not clean exit |

For a web server, `Restart=always` or `Restart=on-failure` are both reasonable choices.

---

## 4. Enable and Start

```bash
# Reload systemd to pick up the new unit file
sudo systemctl daemon-reload

# Enable: auto-start on boot
sudo systemctl enable web-server

# Start now
sudo systemctl start web-server

# Check status
sudo systemctl status web-server
```

Expected output:
```
● web-server.service - My Web Server
     Loaded: loaded (/etc/systemd/system/web-server.service; enabled)
     Active: active (running) since ...
   Main PID: 12345 (web-server)
```

---

## 5. Logs

systemd captures stdout/stderr automatically via journald:

```bash
# Follow live logs
sudo journalctl -u web-server -f

# Last 100 lines
sudo journalctl -u web-server -n 100

# Logs since last boot
sudo journalctl -u web-server -b

# Logs between timestamps
sudo journalctl -u web-server --since "2024-01-01 00:00" --until "2024-01-01 06:00"
```

---

## 6. Managing the Service

```bash
sudo systemctl stop web-server        # stop
sudo systemctl restart web-server     # restart
sudo systemctl reload web-server      # send SIGHUP (if app supports graceful reload)
sudo systemctl disable web-server     # remove from boot (doesn't stop running service)
sudo systemctl is-active web-server   # prints "active" or "inactive"
sudo systemctl is-enabled web-server  # prints "enabled" or "disabled"
```

---

## 7. Updating the Binary

```bash
# Upload new binary
scp ./web-server user@your-server:/usr/local/bin/web-server

# Restart the service to load the new binary
sudo systemctl restart web-server
sudo systemctl status web-server
```

For zero-downtime deploys, consider running two instances behind a reverse proxy and switching traffic, or use a binary that supports graceful shutdown on SIGTERM.

---

## 8. Reverse Proxy (Nginx)

Compiled binaries typically bind to a local port. Use Nginx to expose them on 80/443:

```bash
sudo nano /etc/nginx/sites-available/web-server
```

```nginx
server {
    listen 80;
    server_name yourdomain.com;

    location / {
        proxy_pass http://127.0.0.1:5689;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket support (remove if not needed)
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

```bash
sudo ln -s /etc/nginx/sites-available/web-server /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

Then add SSL with Certbot:

```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d yourdomain.com
```

---

## Troubleshooting

```bash
# Service fails to start — check the full error
sudo journalctl -u web-server -n 50 --no-pager

# Binary not executable
sudo chmod +x /usr/local/bin/web-server

# Wrong architecture (e.g. ARM binary on x86)
file /usr/local/bin/web-server
# Should match: ELF 64-bit LSB executable, x86-64 (or aarch64 for ARM)

# Port already in use
sudo ss -tlnp | grep 5689

# Permission denied on working directory
sudo chown -R webserver:webserver /opt/web-server
```

---

## Notes

- If your binary loads config files or writes logs relative to its location, set `WorkingDirectory=` explicitly in the unit file.
- `EnvironmentFile` should be `chmod 600` and owned by root to protect secrets: `sudo chmod 600 /etc/web-server/env`
- For Rust binaries: build with `cargo build --release` — debug builds are 10–100x slower.
- For Go binaries: `CGO_ENABLED=0 GOOS=linux go build -o web-server .` produces a fully static binary with no libc dependency.
- Use `sudo systemctl daemon-reload` any time you edit the `.service` file — changes are not picked up automatically.
