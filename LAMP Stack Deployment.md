## LAMP Stack Deployment – Steps with Commands

### 1. Update Linux Server

```bash
sudo apt update && sudo apt upgrade -y
```

---

### 2. Install Apache Web Server

```bash
sudo apt install apache2 -y
```

Enable and start Apache:

```bash
sudo systemctl enable apache2
sudo systemctl start apache2
```

Check Apache status:

```bash
sudo systemctl status apache2
```

---

### 3. Install MySQL Database Server

```bash
sudo apt install mysql-server -y
```

Secure MySQL installation:

```bash
sudo mysql_secure_installation
```

Login to MySQL:

```bash
sudo mysql
```

Create database and user:

```sql
CREATE DATABASE myapp;
CREATE USER 'appuser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON myapp.* TO 'appuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

### 4. Install PHP and Required Modules

```bash
sudo apt install php libapache2-mod-php php-mysql php-cli php-curl php-xml php-mbstring -y
```

Check PHP version:

```bash
php -v
```

Restart Apache:

```bash
sudo systemctl restart apache2
```

---

### 5. Test PHP Configuration

Create test file:

```bash
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php
```

Open in browser:

```text
http://SERVER-IP/info.php
```

---

### 6. Deploy PHP Application from GitHub

Install Git:

```bash
sudo apt install git -y
```

Clone repository:

```bash
cd /var/www/html
sudo git clone https://github.com/username/project.git
```

Set permissions:

```bash
sudo chown -R www-data:www-data /var/www/html/project
sudo chmod -R 755 /var/www/html/project
```

---

### 7. Configure Apache Virtual Host

Create virtual host file:

```bash
sudo nano /etc/apache2/sites-available/project.conf
```

Add configuration:

```apache
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot /var/www/html/project

    <Directory /var/www/html/project>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Enable site and rewrite module:

```bash
sudo a2ensite project.conf
sudo a2enmod rewrite
sudo systemctl restart apache2
```

---

### 8. Configure SSL Certificate

Install Certbot:

```bash
sudo apt install certbot python3-certbot-apache -y
```

Generate SSL certificate:

```bash
sudo certbot --apache
```

Test SSL renewal:

```bash
sudo certbot renew --dry-run
```

---

### 9. Configure Firewall

Allow HTTP and HTTPS traffic:

```bash
sudo ufw allow 'Apache Full'
```

Enable firewall:

```bash
sudo ufw enable
```

Check firewall status:

```bash
sudo ufw status
```

---

### 10. Verify Deployment

Restart services:

```bash
sudo systemctl restart apache2
sudo systemctl restart mysql
```

Check application:

```text
http://example.com
https://example.com
```

Verified:

* PHP application hosting
* Database connectivity
* HTTPS secure communication
* Production-like deployment environment
