# Aura Mart Courier Management System - Implementation Plan

## Project Overview

Build a comprehensive Next-Gen Laravel web application to manage courier slips for "Aura Mart". The system will allow staff to upload courier slip images or PDFs, extract receiver details using OCR, store data in a MySQL database, map tracking IDs to customers by mobile number, display analytics on an admin dashboard, and export data to Excel.

## Phase 1: Project Setup & Infrastructure

### Objectives
- Initialize Laravel 11 project
- Install Laravel Breeze for authentication
- Set up TailwindCSS for UI
- Configure development environment

### Tasks
1. Create Laravel 11 project in `C:\Users\ajmal\.gemini\antigravity\scratch\aura_mart_courier`
2. Install Laravel Breeze (Blade stack)
3. Configure TailwindCSS with glassmorphism theme
4. Set up environment variables (.env)
5. Generate application key

### Success Criteria
- Laravel app runs on `php artisan serve`
- Authentication pages accessible
- TailwindCSS styles applied

**Estimated Time**: 1-2 hours

---

## Phase 2: Dependencies Installation

### Objectives
- Install all required packages
- Configure OCR and PDF processing
- Set up Excel export library

### Dependencies
```bash
composer require thiagoalessio/tesseract_ocr
composer require spatie/pdf-to-image
composer require maatwebsite/excel
npm install chart.js
```

### System Requirements
- **Tesseract OCR**: Download from UB Mannheim's build
- **Ghostscript/ImageMagick**: For PDF to image conversion
- **PHP 8.2+**: For Laravel 11
- **Node.js & npm**: For frontend assets

### Success Criteria
- All packages installed without errors
- Tesseract OCR accessible via command line
- PDF conversion tools available

**Estimated Time**: 1-2 hours

---

## Phase 3: Database Design & Migrations

### Objectives
- Create database schema
- Set up migrations
- Create Courier model

### Database Schema

#### Couriers Table
```sql
CREATE TABLE couriers (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    customer_name VARCHAR(255) NOT NULL,
    address TEXT NOT NULL,
    mobile_number VARCHAR(20) NOT NULL INDEXED,
    tracking_id VARCHAR(100) NOT NULL,
    courier_company ENUM('DTDC', 'Delhivery', 'Blue Dart', 'India Post', 'Others'),
    date_of_dispatch DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

### Tasks
1. Create migration: `php artisan make:migration create_couriers_table`
2. Define schema in migration file
3. Create Courier model: `php artisan make:model Courier`
4. Run migrations: `php artisan migrate`
5. Set up model relationships and fillable attributes

### Success Criteria
- Migration runs without errors
- Couriers table created in database
- Model accessible in application

**Estimated Time**: 1 hour

---

## Phase 4: Backend Development - Controllers & Logic

### Objectives
- Create controllers for main functionality
- Implement OCR text extraction
- Build data validation and storage logic

### Controllers to Create

#### DashboardController
- Fetch statistics (orders per day, per company, total)
- Aggregate analytics data
- Return data for dashboard charts

#### CourierController
- Handle file uploads (image/PDF)
- Run OCR on uploaded files
- Extract details using regex patterns:
  - Customer Name
  - Mobile Number (10 digits)
  - Tracking ID (alphanumeric)
  - Address
  - Courier Company
- Save records to database
- Auto-link by mobile_number

#### ExportController
- Generate Excel exports
- Apply filters (date range, company, mobile)
- Download Excel file

### OCR Service
```php
app/Services/OCRService.php
- extractText($imagePath)
- parseDetails($ocrText)
- validateMobileNumber($number)
- extractTrackingId($text, $company)
```

### Tasks
1. Create controllers: `php artisan make:controller DashboardController`
2. Create OCR service class
3. Implement file upload handling
4. Implement OCR text extraction
5. Implement regex pattern matching
6. Implement data validation
7. Implement Excel export logic

### Success Criteria
- Controllers created and accessible
- OCR extracts text from images
- Data validation works correctly
- Excel export generates valid files

**Estimated Time**: 3-4 hours

---

## Phase 5: Frontend Development - Blade Views

### Objectives
- Create responsive UI with TailwindCSS
- Build forms and data tables
- Implement charts and analytics

### Views to Create

#### layouts/app.blade.php
- Main admin layout
- Navigation sidebar
- Header with user menu
- Footer

#### dashboard.blade.php
- KPI cards (total orders, today's orders, etc.)
- Charts (orders per day, per company)
- Recent uploads table
- Quick stats

#### couriers/create.blade.php
- Drag & drop file upload zone
- File type validation
- Upload progress indicator

#### couriers/edit.blade.php
- Review extracted OCR data
- Edit form for manual corrections
- Save/Cancel buttons

#### couriers/index.blade.php
- Data table with all couriers
- Search filters (mobile, tracking ID, company)
- Pagination
- Export button

#### search.blade.php
- Mobile number lookup form
- Display all orders for customer
- Order details and tracking info

### Tasks
1. Create layout template
2. Create dashboard view with charts
3. Create upload form with drag & drop
4. Create edit form for OCR review
5. Create data table view
6. Create search interface
7. Style all views with TailwindCSS
8. Add glassmorphism effects

### Success Criteria
- All views render correctly
- Forms submit data properly
- Charts display analytics
- Responsive design works on mobile

**Estimated Time**: 3-4 hours

---

## Phase 6: Routing & API Endpoints

### Objectives
- Set up all application routes
- Create API endpoints for AJAX requests

### Routes
```
GET  /                          -> Dashboard
GET  /couriers                  -> List couriers
GET  /couriers/create           -> Upload form
POST /couriers                  -> Store courier
GET  /couriers/{id}/edit        -> Edit form
PUT  /couriers/{id}             -> Update courier
DELETE /couriers/{id}           -> Delete courier
GET  /search                    -> Mobile lookup form
POST /search                    -> Search by mobile
GET  /export                    -> Export to Excel
GET  /api/dashboard-stats       -> Dashboard data (AJAX)
POST /api/upload                -> File upload (AJAX)
```

### Tasks
1. Define all routes in `routes/web.php`
2. Create API routes for AJAX requests
3. Add middleware for authentication
4. Add route model binding

### Success Criteria
- All routes accessible
- Correct HTTP methods used
- Authentication required where needed

**Estimated Time**: 1 hour

---

## Phase 7: Testing

### Objectives
- Create automated tests
- Verify functionality
- Ensure data integrity

### Tests to Create

#### Feature Tests
- User authentication
- Courier creation
- File upload and OCR
- Data validation
- Excel export
- Mobile number search

#### Unit Tests
- OCR text extraction
- Regex pattern matching
- Mobile number validation
- Tracking ID extraction

### Tasks
1. Create test files: `php artisan make:test CourierTest`
2. Write feature tests for main workflows
3. Write unit tests for services
4. Run tests: `php artisan test`
5. Achieve >80% code coverage

### Success Criteria
- All tests pass
- Code coverage >80%
- No critical bugs found

**Estimated Time**: 2-3 hours

---

## Phase 8: Manual Verification

### Objectives
- Test all features manually
- Verify OCR accuracy
- Test data mapping

### Test Cases

1. **Upload Test**
   - Upload JPG/PNG courier slip
   - Verify OCR extracts text correctly
   - Check data accuracy

2. **Data Mapping Test**
   - Upload multiple slips with same mobile number
   - Verify they group under same customer
   - Check tracking ID mapping

3. **Dashboard Test**
   - Verify charts display correct data
   - Check KPI calculations
   - Test date range filters

4. **Search Test**
   - Search by mobile number
   - Verify all related orders display
   - Check order details accuracy

5. **Export Test**
   - Generate Excel export
   - Verify file format
   - Check data completeness
   - Test filters (date, company, mobile)

### Success Criteria
- All manual tests pass
- OCR accuracy >90%
- No data loss or corruption
- Export files valid and complete

**Estimated Time**: 2-3 hours

---

## Phase 9: Deployment Preparation

### Objectives
- Prepare for production deployment
- Set up environment configuration
- Create deployment documentation

### Tasks
1. Create `.env.production` configuration
2. Set up MySQL database for production
3. Configure file storage (local/S3)
4. Set up error logging
5. Create deployment guide
6. Set up backup strategy
7. Configure security headers
8. Set up SSL certificate

### Success Criteria
- Production environment configured
- Security measures in place
- Deployment guide complete

**Estimated Time**: 1-2 hours

---

## Phase 10: Deployment & Launch

### Objectives
- Deploy to production
- Monitor application
- Handle post-launch issues

### Tasks
1. Deploy code to production server
2. Run migrations on production
3. Set up monitoring and logging
4. Configure backups
5. Test all features in production
6. Create user documentation
7. Train staff on usage
8. Monitor for issues

### Success Criteria
- Application live and accessible
- All features working in production
- No critical errors
- Users trained and ready

**Estimated Time**: 2-3 hours

---

## Timeline Summary

| Phase | Duration | Total |
|-------|----------|-------|
| 1. Setup | 1-2 hrs | 1-2 |
| 2. Dependencies | 1-2 hrs | 2-4 |
| 3. Database | 1 hr | 3-5 |
| 4. Backend | 3-4 hrs | 6-9 |
| 5. Frontend | 3-4 hrs | 9-13 |
| 6. Routing | 1 hr | 10-14 |
| 7. Testing | 2-3 hrs | 12-17 |
| 8. Manual Verification | 2-3 hrs | 14-20 |
| 9. Deployment Prep | 1-2 hrs | 15-22 |
| 10. Deployment | 2-3 hrs | 17-25 |

**Total Estimated Time**: 17-25 hours

---

## Risk Mitigation

### Risks & Mitigation Strategies

1. **OCR Accuracy Issues**
   - Mitigation: Manual review and edit functionality
   - Fallback: Allow manual data entry

2. **PDF Processing Complexity**
   - Mitigation: Start with images only, add PDF later
   - Fallback: Restrict to JPG/PNG initially

3. **Database Performance**
   - Mitigation: Add indexes on frequently queried columns
   - Fallback: Implement caching

4. **File Upload Size Limits**
   - Mitigation: Validate file size before upload
   - Fallback: Compress images automatically

5. **Mobile Number Validation**
   - Mitigation: Strict regex validation
   - Fallback: Manual verification option

---

## Success Metrics

- ✅ All features implemented and tested
- ✅ OCR accuracy >90%
- ✅ System handles 1000+ couriers
- ✅ Dashboard loads in <2 seconds
- ✅ Export completes in <5 seconds
- ✅ Zero data loss
- ✅ 99.9% uptime
- ✅ User satisfaction >4.5/5

---

## Next Steps

1. Review and approve this plan
2. Confirm system requirements (Tesseract, MySQL, etc.)
3. Begin Phase 1: Project Setup
4. Follow phases sequentially
5. Document any deviations
6. Conduct final testing before launch

---

**Document Version**: 1.0
**Last Updated**: March 16, 2026
**Status**: Ready for Implementation
