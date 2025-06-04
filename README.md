# 🖥️ Local WordPress Webserver Setup (VirtualBox + Ubuntu Server)

This guide walks you through setting up a WordPress webserver on a VirtualBox VM using Ubuntu Server.

---

## ✅ Requirements

- Host OS: Linux (e.g., Manjaro)
- VirtualBox installed
- Ubuntu Server ISO (e.g., Ubuntu 22.04 LTS)

---

## 🚀 Step 1: Create Virtual Machine (VM)

**VM Settings:**

- OS: Ubuntu (64-bit)
- RAM: 2 GB+
- Disk: 10 GB+
- Network:
  - Adapter 1: Bridged Adapter
  - Adapter 2: (Optional) Host-only Adapter
- Enable "Cable Connected" and set **Promiscuous Mode** to:
  - Webserver: Deny

---

## 🏗️ Step 2: Install Ubuntu Server

During Ubuntu installation:

- Install OpenSSH Server ✅
- Choose "Minimal installation"
- Set up a user and password

---

## 🔧 Step 3: Install LAMP Stack

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install apache2 mysql-server php libapache2-mod-php php-mysql -y
sudo mysql_secure_installation
sudo systemctl restart apache2
```

---

## 🌐 Step 4: Install WordPress

```bash
cd /tmp
wget https://wordpress.org/latest.tar.gz
tar -xvzf latest.tar.gz
sudo rsync -avP wordpress/ /var/www/html/
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/
sudo rm /var/www/html/index.html  # optional
```

---

## 🛠️ Step 5: Setup MySQL Database

```bash
sudo mysql -u root -p
```

Inside MySQL shell:

```sql
CREATE DATABASE wordpress;
CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## ⚙️ Step 6: Configure WordPress

```bash
cd /var/www/html
sudo cp wp-config-sample.php wp-config.php
sudo nano wp-config.php
```

Update DB settings:

```php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wpuser');
define('DB_PASSWORD', 'secure_password');
```

---

## 🌍 Step 7: Access Webserver from Host

1. Get VM IP:

```bash
ip a
```

2. Visit in browser from host:

```
http://<vm-ip-address>
```

Example: `http://192.168.31.233`

---

## 🌐 Step 8: Optional - Virtual Host Setup

```bash
sudo nano /etc/apache2/sites-available/webserver.pp.conf
```

Paste:

```apache
<VirtualHost *:80>
    ServerAdmin webmaster@webserver.pp
    ServerName webserver.pp
    DocumentRoot /var/www/html
    ErrorLog ${APACHE_LOG_DIR}/webserver.pp_error.log
    CustomLog ${APACHE_LOG_DIR}/webserver.pp_access.log combined
</VirtualHost>
```

Enable and reload:

```bash
sudo a2ensite webserver.pp.conf
sudo systemctl reload apache2
```

Update `/etc/hosts` on host machine:

```text
192.168.31.233    webserver.pp
```

Then open in browser:

```
http://webserver.pp
```

---

## ✅ Done!

You now have a fully functional local WordPress server running in a VirtualBox VM for testing and development.
