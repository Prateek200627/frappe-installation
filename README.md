# 🚀 Frappe Framework & ERPNext v13 Installation Guide (Ubuntu 22.04 WSL)

A complete step-by-step guide to install **Frappe Framework v13** and **ERPNext v13** on **Ubuntu 22.04 (WSL2)** from scratch.

This guide also includes solutions to common installation errors encountered during setup.

---

# 📋 System Requirements

| Software | Version |
|----------|----------|
| Windows | Windows 10/11 |
| WSL | Ubuntu 22.04 LTS |
| Python | 3.10 |
| NodeJS | 18.x |
| MariaDB | 10.6 |
| Redis | Latest |
| Yarn | Latest |
| Bench | 5.x |
| Frappe | 13.58.22 |
| ERPNext | 13.55.2 |

---

# Step 1 : Install WSL

Open **Command Prompt (Administrator)**

```cmd
wsl --install -d Ubuntu-22.04
```

Restart the system.

Open Ubuntu.

Create

```
Username:
Password:
```

---

# Step 2 : Update Ubuntu

```bash
sudo apt update
sudo apt upgrade -y
```

---

# Step 3 : Install Required Dependencies

```bash
sudo apt install git python3-dev python3-pip python3-setuptools python3-wheel python3-venv redis-server mariadb-server mariadb-client curl xvfb wkhtmltopdf software-properties-common -y
```

---

# Step 4 : Install NodeJS

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```

```bash
sudo apt install nodejs -y
```

Verify

```bash
node -v
npm -v
```

---

# Step 5 : Install Yarn

```bash
sudo npm install -g yarn
```

Verify

```bash
yarn -v
```

---

# Step 6 : Install Bench

```bash
sudo pip3 install frappe-bench
```

Verify

```bash
bench --version
```

---

# Step 7 : Secure MariaDB

Run

```bash
sudo mysql_secure_installation
```

Select

```
Remove anonymous users? Y

Disallow root login remotely? Y

Remove test database? Y

Reload privilege tables? Y
```

---

# Step 8 : Configure MariaDB

Open

```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
```

Locate

```ini
[mysqld]
```

Add

```ini
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
skip-character-set-client-handshake
```

> **Important:** Remove any duplicate entries such as:

```ini
collation-server = utf8mb4_general_ci
```

Save

```
CTRL + O
ENTER
CTRL + X
```

Restart MariaDB

```bash
sudo systemctl restart mariadb
```

Verify

```bash
mysql -u root -p
```

Run

```sql
SHOW VARIABLES LIKE 'collation_server';
```

Expected Output

```
utf8mb4_unicode_ci
```

Exit

```sql
EXIT;
```

---

# Step 9 : Create Bench

Move to Home Directory

```bash
cd ~
```

Create Bench

```bash
bench init frappe-bench --frappe-branch version-13
```

Go inside

```bash
cd frappe-bench
```

---

# Step 10 : Create Site

```bash
bench new-site site.local
```

Provide

```
MySQL Root Password

Administrator Password
```

---

# Step 11 : Download ERPNext

```bash
bench get-app erpnext --branch version-13
```

Skip this command if ERPNext already exists.

---

# Step 12 : Install ERPNext

```bash
bench --site site.local install-app erpnext
```

---

# Step 13 : Set Default Site

```bash
bench use site.local
```

---

# Step 14 : Start Bench

```bash
bench start
```

Open Browser

```
http://127.0.0.1:8000
```

---

# Useful Commands

Check Bench Version

```bash
bench version
```

List Installed Apps

```bash
bench --site site.local list-apps
```

Run Migration

```bash
bench --site site.local migrate
```

Restart Bench

```bash
bench restart
```

Stop Bench

```
CTRL + C
```

Drop Site

```bash
bench drop-site site.local --force
```

---

# Common Errors & Solutions

---

## 1. MariaDB Collation Error

### Error

```
Expected utf8mb4_unicode_ci
Found utf8mb4_general_ci
```

### Cause

Duplicate entries inside

```
50-server.cnf
```

### Solution

Keep only

```ini
collation-server = utf8mb4_unicode_ci
```

Restart MariaDB

```bash
sudo systemctl restart mariadb
```

---

## 2. pkg_resources Error

### Error

```
ModuleNotFoundError:
pkg_resources
```

### Cause

Latest setuptools version is incompatible with Frappe v13.

### Solution

Activate Virtual Environment

```bash
source env/bin/activate
```

Remove setuptools

```bash
pip uninstall setuptools
```

Install Compatible Version

```bash
pip install setuptools==70.0.0
```

Verify

```bash
python -c "import pkg_resources"
```

Deactivate

```bash
deactivate
```

---

## 3. Access Denied

```
Access denied for user 'root'@'localhost'
```

Login

```bash
mysql -u root -p
```

Reset password if required.

---

## 4. Duplicate Entry

```
Duplicate entry 'Accounts'
```

### Cause

Installation stopped midway.

### Solution

```bash
bench drop-site site.local --force

rm -rf sites/site.local

bench new-site site.local
```

---

## 5. No Module Named frappe

### Cause

Bench created incorrectly.

### Solution

Create bench inside

```
~/frappe-bench
```

NOT

```
/mnt/c/Users/...
```

---

## 6. Site Not Found

```
127.0.0.1 does not exist
```

### Solution

```bash
bench use site.local

bench start
```

---

# Verify Installation

Run

```bash
bench version
```

Expected

```
erpnext 13.55.2
frappe 13.58.22
```

List Apps

```bash
bench --site site.local list-apps
```

Expected

```
frappe
erpnext
```

---

# Project Structure

```
frappe-bench
│
├── apps
│   ├── frappe
│   └── erpnext
│
├── config
│
├── env
│
├── logs
│
├── sites
│   ├── common_site_config.json
│   └── site.local
│
└── Procfile
```

---

# Best Practices

- Always install Bench inside the Linux home directory (`~/frappe-bench`).
- Verify MariaDB collation before creating the site.
- Use Ubuntu 22.04 with Python 3.10 for Frappe v13.
- Do **not** manually install `frappe` after `bench new-site`; install **ERPNext** instead.
- If installation fails midway, drop the site and recreate it.

---

# Tech Stack

- Frappe Framework
- ERPNext
- Python
- MariaDB
- Redis
- NodeJS
- Yarn
- Ubuntu 22.04
- WSL2

---

# Author

**Prateek Basu**

GitHub: https://github.com/Prateek200627

If this guide helped you, consider giving the repository a ⭐.
