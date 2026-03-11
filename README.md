# Apache + PHP + MariaDB + SSL Installation Guide (Windows Server)

A complete step-by-step guide to install **Apache, PHP, MariaDB, and SSL** on **Windows Server 2016/2019/2022**.

This setup creates a secure **HTTPS web server environment** suitable for development or internal production systems.

---

# Table of Contents

* Overview
* System Requirements
* Software Downloads
* Installation Steps

  * Install Apache
  * Install PHP
  * Install MariaDB
  * Configure Apache + PHP
  * Enable SSL (HTTPS)
* Verify Installation
* Troubleshooting
* Project Structure
* License

---

# Overview

This guide installs the following stack:

| Component | Description                |
| --------- | -------------------------- |
| Apache    | Web Server                 |
| PHP       | Backend scripting language |
| MariaDB   | Database server            |
| OpenSSL   | SSL certificate generation |
| HeidiSQL  | Database management GUI    |

Final environment:

```
Windows Server
      │
      ├── Apache 2.4
      │       │
      │       └── PHP 8.x
      │
      └── MariaDB
```

HTTPS support will be enabled using **SSL certificates**.

---

# System Requirements

Minimum requirements:

* Windows Server 2016 / 2019 / 2022
* Administrator access
* 4GB RAM (recommended)
* Internet connection

---

# Software Downloads

Download the following software before installation.

### Apache

https://www.apachelounge.com/download/

Example:

```
httpd-2.4.x-win64.zip
```

---

### PHP

https://windows.php.net/download/

Example:

```
php-8.x-Win32-vs16-x64.zip
```

---

### Microsoft Visual C++ Redistributable

Apache requires this runtime.

https://aka.ms/vs/17/release/vc_redist.x64.exe

---

### MariaDB

https://mariadb.org/download/

---

### HeidiSQL (Database GUI)

https://www.heidisql.com/download.php

---

# 1. Install Apache

### Extract Apache

Extract Apache to:

```
C:\Apache24
```

---

### Start Apache (Test Mode)

Open **Command Prompt as Administrator**:

```
cd C:\Apache24\bin
httpd.exe
```

If Apache starts successfully, no errors will appear.

---

### Install Apache as Windows Service

```
httpd.exe -k install
```

---

### Start Apache Service

Open:

```
services.msc
```

Find and start:

```
Apache2.4
```

---

### Test Apache

Open browser:

```
http://localhost
```

You should see the **Apache Test Page**.

---

# 2. Install PHP

### Extract PHP

Extract PHP to:

```
C:\php
```

---

### Configure PHP

Rename:

```
php.ini-production
```

to:

```
php.ini
```

---

### Configure Apache to use PHP

Open:

```
C:\Apache24\conf\httpd.conf
```

Add the following lines:

```
LoadModule php_module "c:/php/php8apache2_4.dll"
AddHandler application/x-httpd-php .php
PHPIniDir "C:/php"
```

---

### Update DirectoryIndex

Find:

```
DirectoryIndex index.html
```

Replace with:

```
DirectoryIndex index.php index.html
```

---

### Enable PHP Extensions

Open:

```
C:\php\php.ini
```

Enable:

```
extension_dir = "ext"
extension=pdo_mysql
extension=mysqli
```

---

# 3. Restart Apache

Restart Apache from:

```
services.msc
```

---

# 4. Test PHP

Create file:

```
C:\Apache24\htdocs\phpinfo.php
```

```
<?php
phpinfo();
?>
```

Open browser:

```
http://localhost/phpinfo.php
```

You should see the **PHP Information Page**.

---

# 5. Install MariaDB

Run the MariaDB installer and follow the setup wizard.

Important settings:

* Set **root password**
* Enable **MariaDB service**
* Default port: **3306**

---

### Verify MariaDB Service

Open:

```
services.msc
```

Confirm:

```
MariaDB
```

is running.

---

# 6. Install SSL Certificate (Self-Signed)

Open Command Prompt:

```
cd C:\Apache24\bin
```

---

### Generate Private Key

```
openssl genrsa -out server.key 2048
```

---

### Generate CSR

```
openssl req -new -key server.key -out server.csr
```

Example information:

```
Country Name: MM
State: Yangon
Organization: Example Company
Common Name: localhost
```

---

### Generate Self-Signed Certificate

```
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
```

---

# 7. Configure Apache SSL

Open:

```
C:\Apache24\conf\httpd.conf
```

Enable SSL modules:

```
LoadModule ssl_module modules/mod_ssl.so
LoadModule socache_shmcb_module modules/mod_socache_shmcb.so
```

---

Enable SSL configuration:

```
Include conf/extra/httpd-ssl.conf
```

---

### Configure Certificate Path

Open:

```
C:\Apache24\conf\extra\httpd-ssl.conf
```

Update:

```
SSLCertificateFile "C:/Apache24/conf/server.crt"
SSLCertificateKeyFile "C:/Apache24/conf/server.key"
```

---

# 8. Restart Apache

Restart Apache service:

```
services.msc
Restart Apache2.4
```

---

# 9. Test HTTPS

Open browser:

```
https://localhost
```

You should see the **secure HTTPS site**.

---

# Verify Installation

Test the following:

| Component | URL                          |
| --------- | ---------------------------- |
| Apache    | http://localhost             |
| PHP       | http://localhost/phpinfo.php |
| HTTPS     | https://localhost            |

---

# Troubleshooting

### Apache not starting

Check error log:

```
C:\Apache24\logs\error.log
```

---

### Port 80 already used

Check using:

```
netstat -ano | findstr :80
```

---

### PHP not working

Verify:

```
php8apache2_4.dll
```

exists in the PHP directory.

---

# Project Structure

Recommended GitHub repository structure:

```
apache-php-ssl-installation
│
├── README.md
├── configs
│   ├── httpd.conf.example
│   └── httpd-ssl.conf.example
│
├── images
│   ├── apache-test.png
│   ├── phpinfo.png
│   └── https-test.png
│
└── scripts
    └── ssl-generation.txt
```

---

# License

This project documentation is provided for educational and internal deployment purposes.

---

# Author

Maintained by:

```
Your Name
Your Organization
```
