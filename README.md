# Aura Mart Courier Management System

## Overview
A comprehensive Next-Gen Laravel web application to manage courier slips for "Aura Mart". The system allows staff to upload courier slip images or PDFs, extract receiver details using OCR, store data in a database, map tracking IDs to customers by mobile number, display analytics on an admin dashboard, and export data to Excel.

## Features
- **OCR Text Extraction**: Automatically extract courier details from slip images/PDFs
- **Smart Data Mapping**: Link tracking IDs to customers by mobile number
- **Admin Dashboard**: View analytics and KPIs with charts
- **Excel Export**: Export courier data with filters
- **Mobile Lookup**: Search orders by customer mobile number
- **Secure Authentication**: Built with Laravel Breeze
- **Modern UI**: TailwindCSS with glassmorphism design

## Tech Stack
- **Backend**: Laravel 11
- **Frontend**: Blade Templates + TailwindCSS
- **Database**: SQLite (development) / MySQL (production)
- **OCR**: Tesseract OCR
- **PDF Processing**: Ghostscript / ImageMagick
- **Excel**: Maatwebsite Excel
- **Charts**: Chart.js

## Installation

### Prerequisites
- Docker (recommended)
- PHP 8.2+
- Composer
- Node.js & npm
- MySQL or SQLite
- Tesseract OCR

### Setup Steps

1. Clone the repository
```bash
git clone https://github.com/ajmal2050/aura-mart-courier.git
cd aura-mart-courier
```

2. Install dependencies
```bash
composer install
npm install
```

3. Configure environment
```bash
cp .env.example .env
php artisan key:generate
```

4. Run migrations
```bash
php artisan migrate
```

5. Build assets
```bash
npm run dev
```

6. Start development server
```bash
php artisan serve
```

## Database Schema

### Couriers Table
- `id` - Primary Key
- `customer_name` - String
- `address` - Text
- `mobile_number` - String (indexed)
- `tracking_id` - String
- `courier_company` - Enum (DTDC, Delhivery, Blue Dart, India Post, Others)
- `date_of_dispatch` - Date
- `created_at`, `updated_at` - Timestamps

## Project Structure

```
aura-mart-courier/
├── app/
│   ├── Http/Controllers/
│   │   ├── DashboardController.php
│   │   ├── CourierController.php
│   │   └── ExportController.php
│   ├── Models/
│   │   └── Courier.php
│   └── Services/
│       └── OCRService.php
├── database/
│   └── migrations/
├── resources/
│   ├── views/
│   │   ├── layouts/
│   │   ├── dashboard.blade.php
│   │   ├── couriers/
│   │   └── search.blade.php
│   └── css/
├── routes/
│   └── web.php
└── tests/
```

## Usage

### Upload Courier Slip
1. Navigate to Couriers > Upload
2. Drag & drop or select courier slip image/PDF
3. System extracts details via OCR
4. Review and edit extracted data
5. Save to database

### View Dashboard
- Analytics with orders per day/company
- Total orders and KPI cards
- Recent uploads

### Search by Mobile
- Use mobile lookup to find all orders for a customer
- View tracking details and dispatch dates

### Export Data
- Filter by date range, company, or mobile number
- Export to Excel format

## Testing

```bash
php artisan test
```

## License

MIT License

**Project Status**: Under Development
**Last Updated**: March 16, 2026
