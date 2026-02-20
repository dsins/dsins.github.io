# LAMP Stack on Ubuntu: Apache, MySQL, PHP Multi-Version, VirtualHost, phpMyAdmin

**Target:** Local development environment with multiple PHP versions, clean VirtualHost per project, `.htaccess` support (Laravel/Symfony/CodeIgniter compatible).

> **Ubuntu 24.04 critical differences** vs 22.04 are marked with `⚠️ 24.04` throughout this document.

---

## 1. Apache

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install apache2 -y
```

### Firewall

```bash
sudo ufw allow in "Apache Full"   # opens port 80 (HTTP) and 443 (HTTPS)
sudo ufw status
```

### Verify

```bash
sudo systemctl status apache2
# Visit http://127.0.0.1 - should show Apache default page
```

### Essential Apache commands

```bash
sudo systemctl start apache2
sudo systemctl stop apache2
sudo systemctl restart apache2    # full restart - reloads config + workers
sudo systemctl reload apache2     # graceful reload - no downtime, config only
sudo apache2ctl configtest        # always run this before restarting
```

---

## 2. MySQL

```bash
sudo apt install mysql-server -y
sudo systemctl enable --now mysql
```

### ⚠️ Authentication plugin changes - critical for 24.04

| Ubuntu | MySQL version | Default auth plugin | mysql_native_password |
|---|---|---|---|
| 22.04 | MySQL 8.0 | `caching_sha2_password` | Available but deprecated |
| 24.04 | MySQL 8.0 (repo default) | `caching_sha2_password` | Available but deprecated |
| 24.04 + MySQL 8.4 | MySQL 8.4 | `caching_sha2_password` | **Disabled by default** |

`mysql_native_password` is deprecated in MySQL 8.0 and **not loaded at all** in MySQL 8.4. PHP 7.4+ supports `caching_sha2_password` natively - use it for all new users.

### Set root password

```bash
sudo mysql
```

```sql
-- Check current auth plugin
SELECT user, host, plugin FROM mysql.user WHERE user = 'root';

-- Set root password with caching_sha2_password (correct for MySQL 8.0 and 8.4)
ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'your_password';
FLUSH PRIVILEGES;
EXIT;
```

### ⚠️ If you need mysql_native_password (legacy apps, PHP < 7.4 only)

Only do this if you have a specific reason. Not recommended for new setups.

```bash
sudo nano /etc/mysql/conf.d/enable-native-password.cnf
```

```ini
[mysqld]
mysql_native_password=ON
```

```bash
sudo systemctl restart mysql
```

> This workaround will stop working entirely on MySQL 9.0, which removes `mysql_native_password` completely.

### Create a project database and dedicated user

```sql
CREATE DATABASE myproject CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'myuser'@'localhost' IDENTIFIED BY 'strongpassword';
GRANT ALL PRIVILEGES ON myproject.* TO 'myuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## 3. PHP - Multi-Version Setup

### ⚠️ Default PHP version by Ubuntu release

| Ubuntu | Default PHP (apt) |
|---|---|
| 22.04 | PHP 8.1 |
| 24.04 | PHP 8.3 |

### Add ondrej/php PPA (required for multiple versions and latest releases)

```bash
sudo apt install software-properties-common -y
sudo add-apt-repository ppa:ondrej/php -y
sudo apt update
```

### Install PHP versions

```bash
# Current stable
sudo apt install php8.3 -y

# Older versions for legacy projects
sudo apt install php8.2 php8.1 php7.4 -y
```

> **Note:** PHP 5.6 and earlier are not available on Ubuntu 22.04/24.04 even via the ondrej PPA. Minimum supported is PHP 7.4.

### Install Apache module + MySQL extension per version

```bash
sudo apt install libapache2-mod-php8.3 php8.3-mysql -y
sudo apt install libapache2-mod-php8.2 php8.2-mysql -y
sudo apt install libapache2-mod-php7.4 php7.4-mysql -y
```

### ⚠️ Explicitly enable the PHP module after install (24.04 does not always auto-enable)

```bash
sudo a2enmod php8.3
sudo systemctl restart apache2

# Verify
apache2ctl -M | grep php
```

### Common extensions (replace 8.3 with your target version)

```bash
sudo apt install \
  php8.3-cli php8.3-common php8.3-fpm \
  php8.3-curl php8.3-gd php8.3-imagick \
  php8.3-intl php8.3-mbstring php8.3-xml \
  php8.3-zip php8.3-bcmath php8.3-bz2 \
  php8.3-soap php8.3-redis php8.3-memcached \
  php8.3-mysql php8.3-sqlite3 \
  -y
```

To install the same set for another version: `Ctrl+H` → find `8.3` → replace with target version.

Explore available packages:
```bash
apt search php8.3- | less
```

---

## 4. Apache Configuration

### Set index.php priority

```bash
sudo nano /etc/apache2/mods-enabled/dir.conf
```

Change the `DirectoryIndex` line:
```apache
DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
```

### Enable mod_rewrite and mbstring (required for Laravel, Symfony, WordPress)

```bash
sudo a2enmod rewrite
sudo phpenmod mbstring
```

### Enable .htaccess overrides globally

```bash
sudo nano /etc/apache2/apache2.conf
```

Find the `/var/www/` block and change `AllowOverride None` to `All`:

```apache
<Directory /var/www/>
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>
```

### Suppress FQDN warning

```bash
echo "ServerName 127.0.0.1" | sudo tee -a /etc/apache2/apache2.conf
```

### Apply all changes

```bash
sudo apache2ctl configtest   # must say "Syntax OK"
sudo systemctl restart apache2
```

---

## 5. VirtualHost - One Per Project

### Create project directory

```bash
sudo mkdir -p /var/www/myproject
sudo chown -R $USER:$USER /var/www/myproject
sudo chmod -R 755 /var/www/myproject
```

### Create VirtualHost config

```bash
sudo nano /etc/apache2/sites-available/myproject.local.conf
```

```apache
<VirtualHost *:80>
    ServerName myproject.local
    ServerAlias www.myproject.local
    DocumentRoot /var/www/myproject/public

    <Directory /var/www/myproject/public>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/myproject-error.log
    CustomLog ${APACHE_LOG_DIR}/myproject-access.log combined
</VirtualHost>
```

> **Laravel/Symfony:** `DocumentRoot` must point to the `public/` subdirectory, not the project root.

### Enable the site

```bash
sudo a2ensite myproject.local.conf
sudo apache2ctl configtest
sudo systemctl reload apache2
```

### Add to /etc/hosts

```bash
echo "127.0.0.1 myproject.local" | sudo tee -a /etc/hosts
```

### Disable a site

```bash
sudo a2dissite myproject.local.conf
sudo systemctl reload apache2
```

---

## 6. Switch PHP Version (CLI + Apache)

```bash
# Switch CLI
sudo update-alternatives --set php /usr/bin/php8.2

# Switch Apache module
sudo a2dismod php8.3
sudo a2enmod php8.2
sudo systemctl restart apache2

# Verify
php -v
apache2ctl -M | grep php
```

### Per-VirtualHost PHP version via PHP-FPM (recommended over global switch)

Run different PHP versions per site without touching the global Apache module:

```bash
sudo apt install php8.3-fpm php8.2-fpm -y
sudo a2enmod proxy_fcgi setenvif
sudo a2enconf php8.3-fpm
sudo systemctl restart apache2
```

In your VirtualHost config:
```apache
<VirtualHost *:80>
    ServerName myproject.local
    DocumentRoot /var/www/myproject/public

    <FilesMatch \.php$>
        SetHandler "proxy:unix:/run/php/php8.2-fpm.sock|fcgi://localhost"
    </FilesMatch>

    <Directory /var/www/myproject/public>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

Change the socket path to target any installed PHP-FPM version.

---

## 7. phpMyAdmin

### ⚠️ Ubuntu 24.04: apt package is outdated and has known dependency issues

The `phpmyadmin` package in Ubuntu 24.04's universe repo ships version 5.2.1 and frequently fails with unmet dependencies or throws errors about deprecated PHP constructs. The manual install method is more reliable on 24.04.

### Method A - apt (works reliably on 22.04)

```bash
sudo apt install phpmyadmin php-mbstring gettext -y
```

During installation:
- Select `apache2` (Space to select, Tab → OK)
- Configure with dbconfig-common: **Yes**
- Enter your MySQL root password

```bash
sudo ln -s /etc/phpmyadmin/apache.conf /etc/apache2/conf-available/phpmyadmin.conf
sudo a2enconf phpmyadmin
sudo systemctl restart apache2
```

Access at: `http://127.0.0.1/phpmyadmin`

### Method B - Manual install (recommended for 24.04)

```bash
# Download latest stable release
cd /tmp
wget https://www.phpmyadmin.net/downloads/phpMyAdmin-latest-all-languages.tar.gz
tar xzf phpMyAdmin-latest-all-languages.tar.gz
sudo mv phpMyAdmin-*-all-languages /usr/share/phpmyadmin

# Set permissions
sudo mkdir /usr/share/phpmyadmin/tmp
sudo chown -R www-data:www-data /usr/share/phpmyadmin/tmp
sudo chmod 770 /usr/share/phpmyadmin/tmp

# Configure
sudo cp /usr/share/phpmyadmin/config.sample.inc.php /usr/share/phpmyadmin/config.inc.php
sudo nano /usr/share/phpmyadmin/config.inc.php
```

Set a random 32-character blowfish secret:
```php
$cfg['blowfish_secret'] = 'your_32_character_random_string__';
```

Create Apache config:
```bash
sudo nano /etc/apache2/conf-available/phpmyadmin.conf
```

```apache
Alias /phpmyadmin /usr/share/phpmyadmin

<Directory /usr/share/phpmyadmin>
    Options SymLinksIfOwnerMatch
    DirectoryIndex index.php
    AllowOverride All
    Require all granted
</Directory>

<Directory /usr/share/phpmyadmin/libraries>
    Require all denied
</Directory>

<Directory /usr/share/phpmyadmin/setup>
    Require all denied
</Directory>
```

```bash
sudo a2enconf phpmyadmin
sudo systemctl restart apache2
```

### ⚠️ Do not log into phpMyAdmin as root - create a dedicated user

```sql
CREATE USER 'pmauser'@'localhost' IDENTIFIED BY 'strongpassword';
GRANT ALL PRIVILEGES ON *.* TO 'pmauser'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

Log in with `pmauser`. Root login via phpMyAdmin often fails due to `auth_socket` / `caching_sha2_password` requiring a secure channel.

---

## 8. Useful Apache Management

```bash
# List enabled sites
ls /etc/apache2/sites-enabled/

# List enabled modules
apache2ctl -M

# Check which process is using port 80
sudo ss -tlnp | grep :80

# Tail logs
sudo tail -f /var/log/apache2/error.log
sudo tail -f /var/log/apache2/myproject-error.log
sudo tail -f /var/log/apache2/access.log
```

---

## Troubleshooting

### 403 Forbidden
- `DocumentRoot` path doesn't exist or is wrong
- `AllowOverride All` not set in the Directory block
- Permissions: `sudo chmod -R 755 /var/www/myproject`

### 500 Internal Server Error
- `.htaccess` syntax error: `sudo tail -f /var/log/apache2/error.log`
- PHP extension missing - check log for `Call to undefined function`
- Wrong PHP version active: `php -v` and `apache2ctl -M | grep php`

### Site not resolving locally
- `/etc/hosts` missing: `grep myproject.local /etc/hosts`
- VirtualHost not enabled: `ls /etc/apache2/sites-enabled/`
- Apache not reloaded: `sudo systemctl reload apache2`

### ⚠️ PHP module not loading after install on 24.04

```bash
apache2ctl -M | grep php   # check if loaded

sudo a2enmod php8.3
sudo systemctl restart apache2
```

### ⚠️ "Plugin 'mysql_native_password' is not loaded" (MySQL 8.4)

```bash
# Recommended: migrate user to caching_sha2_password
sudo mysql
ALTER USER 'youruser'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'password';
FLUSH PRIVILEGES;
EXIT;

# Temporary workaround: re-enable native password plugin
echo -e "[mysqld]\nmysql_native_password=ON" | sudo tee /etc/mysql/conf.d/enable-native-password.cnf
sudo systemctl restart mysql
```

### ⚠️ phpMyAdmin 404 after install on 24.04

```bash
ls /etc/apache2/conf-enabled/ | grep phpmyadmin   # confirm enabled

sudo a2enconf phpmyadmin
sudo systemctl restart apache2
```

### MySQL access denied after reboot

```bash
sudo mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'your_password';
FLUSH PRIVILEGES;
EXIT;
```

---

## Notes

- `a2enmod`, `a2dismod`, `a2ensite`, `a2dissite`, `a2enconf` all require `reload` or `restart` to take effect.
- For production servers, use PHP-FPM over `mod_php` - better isolation, per-site PHP version, pool-level resource limits.
- `mysql_native_password` will be completely removed in MySQL 9.0. Migrate all users to `caching_sha2_password` now.
- On 24.04, after switching PHP versions always run `apache2ctl configtest` before restarting - module conflicts can cause silent failures.
- Apache per-VirtualHost log files (as shown in the config) make debugging much easier than relying on the global log when running multiple projects.
