# Aura Mart Courier Management System - Deployment Guide

## Production Deployment Instructions

This guide covers deploying the Aura Mart Courier Management System to a production environment.

## Prerequisites

### Server Requirements
- **OS**: Linux (Ubuntu 20.04 LTS or later recommended)
- **PHP**: 8.2 or higher
- **MySQL**: 8.0 or higher
- **Node.js**: 18.x or higher
- **Nginx** or **Apache** web server
- **SSL Certificate**: Valid SSL/TLS certificate
- **Disk Space**: Minimum 10GB for application and uploads
- **RAM**: Minimum 2GB (4GB recommended)
- **CPU**: Minimum 2 cores

### Software Dependencies
- Composer
- npm or yarn
- Git
- Tesseract OCR
- Ghostscript or ImageMagick

## Step 1: Server Setup

### 1.1 Update System
```bash
sudo apt update
sudo apt upgrade -y
```

### 1.2 Install PHP and Extensions
```bash
sudo apt install -y php8.2 php8.2-fpm php8.2-mysql php8.2-mbstring \
  php8.2-xml php8.2-bcmath php8.2-curl php8.2-gd php8.2-zip
```

### 1.3 Install MySQL
```bash
sudo apt install -y mysql-server
sudo mysql_secure_installation
```

### 1.4 Install Node.js
```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
```

### 1.5 Install Composer
```bash
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
```

### 1.6 Install Tesseract OCR
```bash
sudo apt install -y tesseract-ocr
```

### 1.7 Install Ghostscript
```bash
sudo apt install -y ghostscript
```

## Step 2: Database Setup

### 2.1 Create Database and User
```bash
sudo mysql -u root -p
```

```sql
CREATE DATABASE aura_mart_courier;
CREATE USER 'aura_mart'@'localhost' IDENTIFIED BY 'strong_password_here';
GRANT ALL PRIVILEGES ON aura_mart_courier.* TO 'aura_mart'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### 2.2 Backup Strategy
```bash
# Create backup directory
sudo mkdir -p /backups/aura_mart
sudo chown www-data:www-data /backups/aura_mart

# Create backup script
sudo nano /usr/local/bin/backup-aura-mart.sh
```

Add the following content:
```bash
#!/bin/bash
BACKUP_DIR="/backups/aura_mart"
DB_NAME="aura_mart_courier"
DB_USER="aura_mart"
DB_PASS="strong_password_here"
DATE=$(date +%Y%m%d_%H%M%S)

mysqldump -u $DB_USER -p$DB_PASS $DB_NAME > $BACKUP_DIR/db_$DATE.sql
tar -czf $BACKUP_DIR/app_$DATE.tar.gz /var/www/aura-mart-courier

# Keep only last 7 days of backups
find $BACKUP_DIR -type f -mtime +7 -delete
```

Make it executable:
```bash
sudo chmod +x /usr/local/bin/backup-aura-mart.sh
```

Add to crontab for daily backups:
```bash
sudo crontab -e
# Add: 0 2 * * * /usr/local/bin/backup-aura-mart.sh
```

## Step 3: Application Deployment

### 3.1 Clone Repository
```bash
cd /var/www
sudo git clone https://github.com/ajmal2050/aura-mart-courier.git
cd aura-mart-courier
sudo chown -R www-data:www-data .
```

### 3.2 Install Dependencies
```bash
sudo -u www-data composer install --no-dev --optimize-autoloader
sudo -u www-data npm install
sudo -u www-data npm run build
```

### 3.3 Configure Environment
```bash
sudo cp .env.example .env
sudo nano .env
```

Update the following variables:
```env
APP_ENV=production
APP_DEBUG=false
APP_URL=https://yourdomain.com

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=aura_mart_courier
DB_USERNAME=aura_mart
DB_PASSWORD=strong_password_here

MAIL_MAILER=smtp
MAIL_HOST=smtp.mailtrap.io
MAIL_PORT=465
MAIL_USERNAME=your_email
MAIL_PASSWORD=your_password
MAIL_FROM_ADDRESS=noreply@auramart.com

TESSERACT_OCR_PATH=/usr/bin/tesseract
```

### 3.4 Generate Application Key
```bash
sudo -u www-data php artisan key:generate
```

### 3.5 Run Migrations
```bash
sudo -u www-data php artisan migrate --force
```

### 3.6 Create Storage Links
```bash
sudo -u www-data php artisan storage:link
```

### 3.7 Set Permissions
```bash
sudo chown -R www-data:www-data /var/www/aura-mart-courier
sudo chmod -R 755 /var/www/aura-mart-courier
sudo chmod -R 775 /var/www/aura-mart-courier/storage
sudo chmod -R 775 /var/www/aura-mart-courier/bootstrap/cache
```

## Step 4: Web Server Configuration

### 4.1 Nginx Configuration
```bash
sudo nano /etc/nginx/sites-available/aura-mart-courier
```

Add the following configuration:
```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name yourdomain.com www.yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;

    root /var/www/aura-mart-courier/public;
    index index.php index.html index.htm;

    client_max_body_size 100M;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;
}
```

Enable the site:
```bash
sudo ln -s /etc/nginx/sites-available/aura-mart-courier /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

### 4.2 SSL Certificate (Let's Encrypt)
```bash
sudo apt install -y certbot python3-certbot-nginx
sudo certbot certonly --nginx -d yourdomain.com -d www.yourdomain.com
```

## Step 5: PHP-FPM Configuration

```bash
sudo nano /etc/php/8.2/fpm/pool.d/www.conf
```

Optimize settings:
```ini
pm = dynamic
pm.max_children = 20
pm.start_servers = 5
pm.min_spare_servers = 5
pm.max_spare_servers = 10
pm.max_requests = 500
```

Restart PHP-FPM:
```bash
sudo systemctl restart php8.2-fpm
```

## Step 6: Monitoring & Logging

### 6.1 Configure Logging
```bash
sudo nano /var/www/aura-mart-courier/.env
```

Set:
```env
LOG_CHANNEL=stack
LOG_LEVEL=warning
```

### 6.2 Monitor Logs
```bash
# Application logs
tail -f /var/www/aura-mart-courier/storage/logs/laravel.log

# Nginx logs
tail -f /var/log/nginx/error.log

# PHP-FPM logs
tail -f /var/log/php8.2-fpm.log
```

### 6.3 Set Up Monitoring
```bash
# Install Supervisor for queue workers
sudo apt install -y supervisor

# Create supervisor config
sudo nano /etc/supervisor/conf.d/aura-mart-courier.conf
```

Add:
```ini
[program:aura-mart-courier-queue]
process_name=%(program_name)s_%(process_num)02d
command=php /var/www/aura-mart-courier/artisan queue:work --sleep=3 --tries=3
autostart=true
autorestart=true
numprocs=4
user=www-data
stdout_logfile=/var/www/aura-mart-courier/storage/logs/queue.log
stderr_logfile=/var/www/aura-mart-courier/storage/logs/queue-error.log
```

Start supervisor:
```bash
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl start aura-mart-courier-queue:*
```

## Step 7: Performance Optimization

### 7.1 Cache Configuration
```bash
sudo -u www-data php artisan config:cache
sudo -u www-data php artisan route:cache
sudo -u www-data php artisan view:cache
```

### 7.2 Database Optimization
```bash
# Add indexes
sudo -u www-data php artisan tinker
# Run: DB::statement('ALTER TABLE couriers ADD INDEX idx_mobile (mobile_number)');
```

### 7.3 Enable Gzip Compression
Add to Nginx config:
```nginx
gzip on;
gzip_vary on;
gzip_min_length 1000;
gzip_types text/plain text/css text/xml text/javascript application/x-javascript application/xml+rss;
```

## Step 8: Security Hardening

### 8.1 Firewall Configuration
```bash
sudo ufw enable
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

### 8.2 Fail2Ban Installation
```bash
sudo apt install -y fail2ban
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

### 8.3 SSH Hardening
```bash
sudo nano /etc/ssh/sshd_config
```

Update:
```
Port 2222
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
```

Restart SSH:
```bash
sudo systemctl restart ssh
```

## Step 9: Verification

### 9.1 Test Application
```bash
# Check if application is running
curl -I https://yourdomain.com

# Check database connection
sudo -u www-data php artisan tinker
# Run: DB::connection()->getPdo();

# Check OCR
tesseract --version
```

### 9.2 Run Tests
```bash
sudo -u www-data php artisan test
```

## Step 10: Post-Deployment

### 10.1 Create Admin User
```bash
sudo -u www-data php artisan tinker
# Run: User::create(['name' => 'Admin', 'email' => 'admin@auramart.com', 'password' => Hash::make('password')]);
```

### 10.2 Set Up Cron Jobs
```bash
sudo crontab -e
```

Add:
```
* * * * * cd /var/www/aura-mart-courier && php artisan schedule:run >> /dev/null 2>&1
```

### 10.3 Monitor Application
- Set up uptime monitoring (UptimeRobot, Pingdom)
- Configure error tracking (Sentry)
- Set up performance monitoring (New Relic, DataDog)

## Troubleshooting

### Issue: 500 Internal Server Error
```bash
# Check logs
tail -f /var/www/aura-mart-courier/storage/logs/laravel.log

# Check permissions
sudo chown -R www-data:www-data /var/www/aura-mart-courier
```

### Issue: Database Connection Error
```bash
# Test MySQL connection
mysql -u aura_mart -p -h 127.0.0.1 aura_mart_courier

# Check .env file
sudo cat /var/www/aura-mart-courier/.env | grep DB_
```

### Issue: OCR Not Working
```bash
# Check Tesseract installation
which tesseract
tesseract --version

# Check permissions
sudo -u www-data tesseract /path/to/image.jpg stdout
```

## Rollback Procedure

If deployment fails:
```bash
# Restore from backup
cd /var/www
sudo rm -rf aura-mart-courier
sudo tar -xzf /backups/aura-mart/app_YYYYMMDD_HHMMSS.tar.gz

# Restore database
mysql -u aura_mart -p aura_mart_courier < /backups/aura-mart/db_YYYYMMDD_HHMMSS.sql

# Restart services
sudo systemctl restart nginx php8.2-fpm
```

## Support & Maintenance

- Regular backups: Daily at 2 AM
- Security updates: Apply monthly
- Log rotation: Configure logrotate
- Monitoring: 24/7 uptime monitoring
- Support: Contact admin@auramart.com

---

**Last Updated**: March 16, 2026
**Version**: 1.0
