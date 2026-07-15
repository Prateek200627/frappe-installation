
# Frappe ERPNext Installation Guide (WSL Ubuntu 22.04)

A complete step-by-step guide to install **Frappe Framework v13** and **ERPNext v13** on **Ubuntu 22.04 (WSL2)** with troubleshooting for the most common installation issues.

---

## 📌 Environment

| Component | Version |
|-----------|---------|
| Ubuntu | 22.04 LTS (WSL2) |
| Python | 3.10 |
| MariaDB | 10.6 |
| Node.js | 18.x |
| Bench | 5.x |
| Frappe | 13.58.22 |
| ERPNext | 13.55.2 |

---

# Installation Steps

## 1. Update Ubuntu

```bash
sudo apt update
sudo apt upgrade -y
```

---

## 2. Install Required Packages

```bash
sudo apt install -y \
git \
python3-dev \
python3-pip \
python3-venv \
python3-setuptools \
python3-wheel \
redis-server \
mariadb-server \
mariadb-client \
curl \
wkhtmltopdf \
xvfb \
software-properties-common
```

---

## 3. Install NodeJS

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install nodejs -y
```

Verify:

```bash
node -v
npm -v
```

---

## 4. Install Yarn

```bash
sudo npm install -g yarn
```

Verify:

```bash
yarn -v
```

---

## 5. Install Bench

```bash
sudo pip3 install frappe-bench
```

Verify

```bash
bench --version
```

---

## 6. Configure MariaDB

Run

```bash
sudo mysql_secure_installation
```

Recommended options

```
Remove anonymous users?          Y
Disallow root login remotely?    Y
Remove test database?            Y
Reload privilege tables?         Y
```

---

## 7. Configure Character Set

Open

```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
```

Inside `[mysqld]` add

```ini
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
skip-character-set-client-handshake
```

Restart MariaDB

```bash
sudo systemctl restart mariadb
```

Verify

```sql
SHOW VARIABLES LIKE 'collation_server';
```

Expected output

```
utf8mb4_unicode_ci
```

---

## 8. Initialize Bench

```bash
cd ~

bench init frappe-bench --frappe-branch version-13

cd frappe-bench
```

---

## 9. Create Site

```bash
bench new-site site.local
```

Enter

- MariaDB root password
- Administrator password

---

## 10. Install ERPNext

Download ERPNext

```bash
bench get-app erpnext --branch version-13
```

Install ERPNext

```bash
bench --site site.local install-app erpnext
```

---

## 11. Start Development Server

```bash
bench use site.local
bench start
```

Open

```
http://127.0.0.1:8000
```

---

# Troubleshooting

## MariaDB Collation Error

```
Expected utf8mb4_unicode_ci
Found utf8mb4_general_ci
```

### Solution

Remove duplicate entries from

```
/etc/mysql/mariadb.conf.d/50-server.cnf
```

Ensure only this exists

```ini
collation-server = utf8mb4_unicode_ci
```

Restart MariaDB

```bash
sudo systemctl restart mariadb
```

---

## pkg_resources Error

```
ModuleNotFoundError: No module named 'pkg_resources'
```

### Cause

Newer versions of **setuptools** are incompatible with Frappe v13.

### Fix

```bash
source env/bin/activate

pip uninstall setuptools

pip install setuptools==70.0.0

python -c "import pkg_resources"

deactivate
```

---

## Duplicate Entry Error

```
Duplicate entry 'Accounts'
```

### Cause

Installation stopped midway.

### Solution

Drop the site

```bash
bench drop-site site.local --force
```

Delete

```bash
rm -rf sites/site.local
```

Recreate

```bash
bench new-site site.local
```

---

## Access Denied (MariaDB)

```
Access denied for user 'root'@'localhost'
```

Login

```bash
mysql -u root -p
```

Reset password if required.

---

## Frappe Not Found

```
No module named frappe
```

Create the bench inside

```
~/frappe-bench
```

Do **NOT** create it inside

```
/mnt/c/
```

---

# Useful Commands

```bash
bench version
```

```bash
bench start
```

```bash
bench restart
```

```bash
bench migrate
```

```bash
bench --site site.local list-apps
```

```bash
bench drop-site site.local --force
```

---

# Lessons Learned

- Always create the bench inside the Linux home directory (`~/frappe-bench`).
- Configure MariaDB before creating the site.
- Verify `utf8mb4_unicode_ci` before running `bench new-site`.
- Use a compatible `setuptools` version for Frappe v13.
- If site creation fails midway, recreate the site instead of continuing with the broken one.

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

## Author

**Prateek Basu**

GitHub: https://github.com/Prateek200627
