# Aura Mart Courier Management System - Requirements & Dependencies

## System Requirements

### Development Environment
- **OS**: Windows 11 / macOS / Linux
- **PHP**: 8.2 or higher
- **MySQL**: 8.0+ or SQLite 3.x
- **Node.js**: 18.x or higher
- **npm**: 9.x or higher
- **Composer**: 2.x
- **Git**: 2.x

### Production Environment
- **OS**: Linux (Ubuntu 20.04 LTS or later)
- **PHP**: 8.2 or higher with FPM
- **MySQL**: 8.0 or higher
- **Node.js**: 18.x or higher
- **Web Server**: Nginx or Apache 2.4+
- **SSL**: Valid SSL/TLS certificate
- **RAM**: Minimum 2GB (4GB recommended)
- **CPU**: Minimum 2 cores
- **Disk**: Minimum 10GB

## PHP Extensions Required

```
php8.2-fpm
php8.2-mysql
php8.2-mbstring
php8.2-xml
php8.2-bcmath
php8.2-curl
php8.2-gd
php8.2-zip
php8.2-intl
php8.2-json
php8.2-tokenizer
```

## Laravel Dependencies

### Core Framework
```json
{
  "laravel/framework": "^11.0",
  "laravel/breeze": "^2.4",
  "laravel/tinker": "^2.8"
}
```

### OCR & PDF Processing
```json
{
  "thiagoalessio/tesseract_ocr": "^2.15",
  "spatie/pdf-to-image": "^2.4",
  "spatie/image": "^2.2"
}
```

### Excel Export
```json
{
  "maatwebsite/excel": "^3.1"
}
```

### Database & ORM
```json
{
  "laravel/framework": "includes Eloquent ORM"
}
```

### Frontend
```json
{
  "tailwindcss": "^3.3",
  "@tailwindcss/forms": "^0.5",
  "@tailwindcss/typography": "^0.5",
  "alpinejs": "^3.x",
  "chart.js": "^4.4"
}
```

### Development Dependencies
```json
{
  "laravel/pint": "^1.13",
  "phpunit/phpunit": "^10.5",
  "laravel/sail": "^1.26",
  "vite": "^5.0",
  "@vitejs/plugin-vue": "^5.0"
}
```

## System Dependencies

### Tesseract OCR
**Purpose**: Text extraction from courier slip images

**Installation**:
- **Windows**: Download from [UB Mannheim's Tesseract build](https://github.com/UB-Mannheim/tesseract/wiki)
- **macOS**: `brew install tesseract`
- **Linux**: `sudo apt install tesseract-ocr`

**Verification**:
```bash
tesseract --version
```

### Ghostscript
**Purpose**: PDF to image conversion

**Installation**:
- **Windows**: Download from [ghostscript.com](https://www.ghostscript.com/download/gsdnld.html)
- **macOS**: `brew install ghostscript`
- **Linux**: `sudo apt install ghostscript`

**Verification**:
```bash
gs --version
```

### ImageMagick (Alternative to Ghostscript)
**Purpose**: Image processing and PDF conversion

**Installation**:
- **Windows**: Download from [imagemagick.org](https://imagemagick.org/script/download.php)
- **macOS**: `brew install imagemagick`
- **Linux**: `sudo apt install imagemagick`

**Verification**:
```bash
convert --version
```

## Database Requirements

### MySQL Configuration
```sql
-- Create database
CREATE DATABASE aura_mart_courier CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- Create user
CREATE USER 'aura_mart'@'localhost' IDENTIFIED BY 'strong_password';
GRANT ALL PRIVILEGES ON aura_mart_courier.* TO 'aura_mart'@'localhost';
FLUSH PRIVILEGES;
```

### SQLite Configuration (Development)
```bash
# SQLite database will be created automatically at:
# database/database.sqlite
```

## File Upload Requirements

### Supported Formats
- **Images**: JPG, JPEG, PNG, GIF, BMP
- **Documents**: PDF (optional, requires Ghostscript)

### File Size Limits
- **Maximum file size**: 50MB (configurable)
- **Maximum upload size**: 100MB (PHP configuration)

### Storage Requirements
- **Upload directory**: `storage/app/uploads/`
- **Minimum disk space**: 5GB for uploads
- **Backup storage**: 10GB for database backups

## Browser Compatibility

### Supported Browsers
- Chrome/Chromium 90+
- Firefox 88+
- Safari 14+
- Edge 90+

### Minimum Requirements
- JavaScript enabled
- Cookies enabled
- Local storage support

## Performance Requirements

### Response Times
- Dashboard load: < 2 seconds
- File upload: < 5 seconds
- Excel export: < 10 seconds
- Search query: < 1 second

### Concurrent Users
- Minimum: 10 concurrent users
- Recommended: 50+ concurrent users

### Database Performance
- Query response: < 100ms
- Index on `mobile_number` column
- Index on `tracking_id` column
- Index on `created_at` column

## Security Requirements

### Authentication
- Laravel Breeze authentication
- Password hashing with bcrypt
- Session management
- CSRF protection

### Authorization
- Role-based access control (RBAC)
- Admin dashboard access
- User management

### Data Protection
- SSL/TLS encryption (HTTPS)
- Database encryption at rest
- Secure file storage
- Input validation and sanitization
- SQL injection prevention
- XSS protection

### Compliance
- GDPR compliance for user data
- Data retention policies
- Audit logging
- Backup and recovery procedures

## Development Tools

### Required
- Git version control
- Composer package manager
- npm package manager
- PHP CLI
- MySQL CLI

### Recommended
- VS Code or PhpStorm IDE
- Postman for API testing
- DBeaver for database management
- Docker for containerization

## Installation Checklist

### Before Starting
- [ ] PHP 8.2+ installed and in PATH
- [ ] Composer installed
- [ ] Node.js 18+ installed
- [ ] MySQL 8.0+ running
- [ ] Git installed
- [ ] Tesseract OCR installed
- [ ] Ghostscript installed
- [ ] 10GB+ free disk space
- [ ] 2GB+ RAM available

### Development Setup
- [ ] Clone repository
- [ ] Run `composer install`
- [ ] Run `npm install`
- [ ] Copy `.env.example` to `.env`
- [ ] Generate app key: `php artisan key:generate`
- [ ] Create database
- [ ] Run migrations: `php artisan migrate`
- [ ] Run `npm run dev`
- [ ] Start server: `php artisan serve`
- [ ] Access at `http://localhost:8000`

### Production Setup
- [ ] Server provisioned (Linux)
- [ ] PHP 8.2+ installed with all extensions
- [ ] MySQL 8.0+ installed and configured
- [ ] Node.js 18+ installed
- [ ] Tesseract OCR installed
- [ ] Ghostscript installed
- [ ] SSL certificate obtained
- [ ] Web server (Nginx/Apache) configured
- [ ] Application deployed
- [ ] Database migrations run
- [ ] Backups configured
- [ ] Monitoring set up
- [ ] Security hardened

## Troubleshooting

### PHP Extension Missing
```bash
# Check installed extensions
php -m

# Install missing extension (example: gd)
sudo apt install php8.2-gd
sudo systemctl restart php8.2-fpm
```

### Tesseract Not Found
```bash
# Check installation
which tesseract

# Add to PATH if needed
export PATH=$PATH:/path/to/tesseract
```

### Database Connection Error
```bash
# Test MySQL connection
mysql -u aura_mart -p -h localhost aura_mart_courier

# Check .env file
cat .env | grep DB_
```

### Permission Denied Errors
```bash
# Fix storage permissions
chmod -R 775 storage/
chmod -R 775 bootstrap/cache/

# Fix ownership
chown -R www-data:www-data .
```

## Support & Documentation

- [Laravel Documentation](https://laravel.com/docs)
- [Tesseract OCR Documentation](https://github.com/UB-Mannheim/tesseract/wiki)
- [TailwindCSS Documentation](https://tailwindcss.com/docs)
- [Chart.js Documentation](https://www.chartjs.org/docs/latest/)
- [Maatwebsite Excel Documentation](https://docs.laravel-excel.com/)

---

**Last Updated**: March 16, 2026
**Version**: 1.0
