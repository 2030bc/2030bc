# Laravel Docu-Course Platform - Project Guide

## 🎯 Project Overview
**Project Name:** Laravel Docu-Course Platform with Modules Marketplace
**Laravel Version:** 12.24.0 (EXACT VERSION REQUIRED)
**PHP Version:** 8.2+ (minimum 8.2.0)
**Database:** MySQL 8.0+ or PostgreSQL 13+
**Frontend:** Laravel Breeze with Blade templates
**Authentication:** Laravel Breeze + Sanctum for API

### Update: Package Integration Notes
The current installation includes all packages from composer.json:
- **Laravel Breeze (Blade):** Installed for authentication scaffolding
- **Spatie Packages:** Full suite including permissions, media library, activity log, backup, etc.
- **Payment Integration:** Stripe and Binance connectors ready
- **Database Migrations:** Laravel/Breeze tables, Spatie package tables, Sanctum, Telescope

## 🏗️ Architecture Principles
- **Module-Based Design:** Clear separation of concerns with dedicated modules
- **Repository Pattern:** Data access abstraction for testability
- **Service Layer:** Business logic centralization
- **Event-Driven Architecture:** Loose coupling via events and listeners
- **SOLID Principles:** Maintainable and extensible code structure
- **Contract-First Development:** Interfaces before implementations

## 📋 Coding Standards

### Code Quality Requirements
- **PSR-12 Coding Standard:** Enforced by Laravel Pint
- **PHPStan Level 8+:** Static analysis for type safety
- **Test Coverage:** Minimum 90% code coverage
- **Documentation:** PHPDoc blocks for all public methods
- **Type Hints:** Strict typing with return type declarations

### Update: Enhanced Quality Tools
Additional tools now available from composer.json:
- **Laravel Pint:** Code style fixer (PSR-12)
- **Larastan:** PHPStan for Laravel (Level 8+)
- **Rector:** Automated code upgrades
- **Pest:** Modern testing framework alongside PHPUnit
- **Laravel Telescope:** Debug assistant for development

### File Structure Standards
```
app/
├── Console/Commands/     # Artisan commands
├── DTOs/                # Data Transfer Objects
├── Enums/               # PHP 8.2 Enums for constants
├── Events/              # Domain events
├── Exceptions/          # Custom exceptions
├── Http/
│   ├── Controllers/     # Thin controllers
│   ├── Middleware/      # Request filtering
│   ├── Requests/        # Form request validation
│   └── Resources/       # API resource transformations
├── Jobs/                # Queued jobs
├── Listeners/           # Event listeners
├── Models/              # Eloquent models (by domain)
├── Notifications/       # Email/SMS notifications
├── Policies/            # Authorization policies
├── Providers/           # Service providers
├── Rules/               # Custom validation rules
├── Services/            # Business logic services
└── Traits/              # Reusable traits
```

### Update: Laravel Breeze Integration
Laravel Breeze adds the following structure:
```
app/Http/Controllers/Auth/
├── AuthenticatedSessionController.php  # Login/logout
├── ConfirmablePasswordController.php   # Password confirmation
├── EmailVerificationNotificationController.php
├── EmailVerificationPromptController.php
├── NewPasswordController.php          # Password reset
├── PasswordController.php             # Password update
├── PasswordResetLinkController.php    # Reset link
├── RegisteredUserController.php       # Registration
└── VerifyEmailController.php          # Email verification

resources/views/auth/                   # Blade auth templates
resources/views/layouts/                # App layout templates
```

## 🗄️ Database Schema Standards

### Table Design Rules
- **Dual ID Strategy:** `id` (auto-increment) + `uuid` (string)
- **Required Columns:** id, uuid, created_at, updated_at
- **Foreign Keys:** Follow pattern {table_singular}_id
- **Indexes:** uuid, created_at, frequently queried columns
- **Soft Deletes:** For important business entities

### Update: Executed Migration Tables
The following tables are now created from executed migrations:
- **Laravel/Breeze:** users, password_reset_tokens, sessions
- **Laravel Core:** cache, jobs, failed_jobs
- **Spatie Media Library:** media table
- **Spatie Permissions:** permissions, roles, model_has_permissions, etc.
- **Laravel Sanctum:** personal_access_tokens
- **Laravel Telescope:** telescope_entries, telescope_entries_tags, etc.
- **Spatie Activity Log:** activity_log table

### Migration Standards
```
// Standard migration template  
Schema::create('table_name', function (Blueprint $table) {
    $table->id(); // Auto-increment primary key
    $table->uuid('uuid')->unique()->index(); // UUID for external references
    
    // Business columns here
    $table->string('name');
    $table->text('description')->nullable();
    
    // Standard timestamps
    $table->timestamps();
    
    // Indexes for performance
    $table->index(['status', 'created_at']);
    $table->index(['uuid']); // UUID lookup optimization
});
```

## 🌐 Multi-Language Support

### Supported Languages
| Language | Code | Direction | Locale | Status |
|----------|------|-----------|--------|--------|
| English | en | LTR | en_US | Default |
| العربية (Arabic) | ar | RTL | ar_SA | Active |
| Español | es | LTR | es_ES | Active |
| Français | fr | LTR | fr_FR | Active |
| Deutsch | de | LTR | de_DE | Active |

### Update: Localization Package Integration
Multi-language support enhanced with installed packages:
- **mcamara/laravel-localization:** URL-based language switching
- **spatie/laravel-translatable:** Model attribute translations
- **spatie/laravel-markdown:** Multi-language markdown content

### RTL Implementation
- **CSS Classes:** Automatic RTL/LTR class application
- **Text Direction:** Dynamic dir attribute management
- **Layout Mirroring:** Flexbox and grid adjustments
- **Icon Rotation:** Directional icon transformations

## 👤 User System Architecture

### User Level Progression (1-14)
| Level | Points Required | Benefits | Access Level |
|-------|----------------|----------|-------------|
| 1-3 | 0-500 | Basic access, public courses | Beginner |
| 4-6 | 501-2000 | Premium content, affiliate program | Intermediate |
| 7-10 | 2001-10000 | Advanced courses, higher commissions | Advanced |
| 11-14 | 10001+ | VIP access, module marketplace | Expert |

### Update: Enhanced User Management
User system enhanced with Spatie packages:
- **spatie/laravel-permission:** Role and permission management
- **Laravel Sanctum:** API token authentication
- **spatie/laravel-activitylog:** User activity tracking
- **Laravel Breeze:** Complete authentication scaffolding

### Points Calculation System
```
Points Sources:
├── Time Spent Reading: 1 point/minute
├── Episode Completion: 10-50 points (based on difficulty)
├── Course Completion: 100-500 points (based on course level)
├── User Reactions: 2 points per reaction given
├── Notes Created: 5 points per note
├── Quality Testimonials: 50-200 points
├── Successful Referrals: 25-100 points
├── Module Contributions: 500-2000 points
└── Community Engagement: 1-10 points per activity
```

## 💰 Payment & Currency System

### Supported Currency Types
- **Fiat Currencies:** USD, EUR, GBP, JPY, etc.
- **Cryptocurrencies:** BTC, ETH, USDT, BNB (via Binance API)
- **Local Currencies:** Custom currencies managed by super-admin
- **Platform Tokens:** Internal reward/credit system

### Update: Payment Integration Ready
Payment processing enhanced with installed packages:
- **stripe/stripe-php:** Credit card and traditional payments
- **binance/binance-connector-php:** Cryptocurrency payments
- **akaunting/laravel-money:** Multi-currency support
- **florianv/exchanger:** Real-time exchange rates
- **moneyphp/money:** Precise monetary calculations

### Payment Integration Priority
1. **Binance API:** Primary crypto payment processor
2. **Stripe:** Traditional payment methods
3. **PayPal:** Global payment alternative
4. **Local Gateways:** Region-specific providers

## 📚 Course System Architecture

### Content Storage Structure
```
storage/docu-courses/
├── {course-alias}/
│   ├── {language-code}/
│   │   ├── metadata.json # Course configuration
│   │   ├── episodes/
│   │   │   ├── {episode-alias}/
│   │   │   │   ├── content.html # Full episode content
│   │   │   │   ├── parts/
│   │   │   │   │   ├── part-001.html
│   │   │   │   │   ├── part-002.html
│   │   │   │   │   └── ...
│   │   │   │   └── metadata.json
│   │   │   └── ...
│   │   └── assets/     # Course assets
│   │       ├── images/
│   │       ├── videos/
│   │       └── documents/
│   └── ...
└── uploads/            # Temporary uploads
    ├── processing/
    └── archive/
```

### Update: Enhanced File Management
File handling enhanced with Spatie packages:
- **spatie/laravel-medialibrary:** Advanced file management
- **spatie/image-optimizer:** Automatic image optimization
- **intervention/image:** Image manipulation
- **league/flysystem-aws-s3-v3:** Cloud storage support

### Access Control Matrix
| Access Level | Authentication | User Level | Payment | Description |
|-------------|---------------|------------|---------|------------|
| Public | No | Any | No | Open access content |
| Authenticated | Yes | 1+ | No | Requires user account |
| Level Restricted | Yes | Specified | No | Requires specific user level |
| Premium | Yes | Any | Yes | Paid content access |
| VIP | Yes | 11+ | Yes | Exclusive high-level content |

## 🔗 Affiliate System

### Commission Structure
| User Level | Base Commission | Bonus Multiplier | Monthly Cap |
|------------|----------------|------------------|------------|
| Level 1-3 | 5% | 1x | $100 |
| Level 4-6 | 10% | 1.2x | $500 |
| Level 7-10 | 15% | 1.5x | $2,000 |
| Level 11-14 | 25% | 2x | Unlimited |

### Referral Tracking
- **Unique Codes:** Auto-generated per user
- **Cookie Duration:** 30 days tracking window
- **Attribution Model:** First-click attribution
- **Conversion Events:** Registration, purchase, subscription

## 🛡️ Security Standards

### Authentication & Authorization
- **Multi-Factor Authentication:** Optional 2FA via Google Authenticator
- **Session Management:** Secure session handling with Redis
- **Password Policy:** Minimum 8 characters, mixed case, numbers, symbols
- **Rate Limiting:** API and login attempt protection
- **CSRF Protection:** All forms protected by default

### Update: Enhanced Security Features
Security enhanced with installed packages:
- **pragmarx/google2fa-laravel:** Two-factor authentication
- **Laravel Sanctum:** Secure API token management
- **spatie/laravel-rate-limited-job-middleware:** Job rate limiting
- **Laravel Breeze:** Secure authentication scaffolding

### Data Protection
- **UUID Obfuscation:** All public IDs use UUID
- **Input Sanitization:** HTML purification for user content
- **SQL Injection Prevention:** Eloquent ORM usage mandatory
- **XSS Protection:** Blade template escaping by default
- **File Upload Security:** Type validation and virus scanning

## ⚡ Performance Standards

### Response Time Requirements
| Page Type | Target Time | Maximum Time | Optimization Strategy |
|-----------|-------------|--------------|----------------------|
| Static Pages | < 100ms | < 200ms | Full page caching |
| Dynamic Content | < 300ms | < 500ms | Query optimization, Redis caching |
| API Endpoints | < 150ms | < 300ms | Database indexing, eager loading |
| File Uploads | N/A | < 30s | Chunked uploads, background processing |

### Update: Performance Monitoring Tools
Performance monitoring enhanced with:
- **spatie/laravel-backup:** Automated backup systems
- **spatie/laravel-schedule-monitor:** Cron job monitoring
- **spatie/laravel-server-monitor:** Server health monitoring
- **Laravel Telescope:** Application performance insights
- **predis/predis:** Redis caching optimization

### Caching Strategy
- **Redis:** Session data, user preferences, temporary data
- **Database Query Cache:** Frequently accessed data
- **File Cache:** Rendered episode content
- **CDN Integration:** Static assets and media files
- **OPcache:** PHP bytecode caching

## 🧪 Testing Requirements

### Test Coverage Standards
- **Overall Coverage:** Minimum 90%
- **Critical Path Coverage:** 100% (payment, authentication, user progression)
- **Service Layer:** 100% unit test coverage
- **Controller Layer:** 90% feature test coverage
- **Model Relationships:** 100% coverage

### Update: Enhanced Testing Stack
Testing capabilities enhanced with:
- **pestphp/pest:** Modern testing framework
- **pestphp/pest-plugin-laravel:** Laravel-specific test helpers
- **nunomaduro/collision:** Beautiful error reporting
- **Laravel Sail:** Docker development environment
- **Laravel Dusk:** Browser testing (available for installation)

### Testing Pyramid
```
Testing Structure:
├── Unit Tests (70%)
│   ├── Model tests
│   ├── Service tests  
│   ├── Helper tests
│   └── Trait tests
├── Feature Tests (25%)
│   ├── HTTP tests
│   ├── Authentication tests
│   ├── API tests
│   └── Integration tests
└── Browser Tests (5%)
    ├── Critical user flows
    ├── Payment processes
    └── Course interactions
```

## 🚀 Deployment Standards

### Environment Configuration
```
# Required Environment Variables
APP_NAME="Laravel Docu-Course Platform"
APP_ENV=production
APP_KEY=base64:generated_key_here
APP_DEBUG=false
APP_URL=https://2030b.com

# Database Configuration
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=docu_course_platform
DB_USERNAME=db_user
DB_PASSWORD=secure_password

# Cache Configuration
CACHE_DRIVER=redis
SESSION_DRIVER=redis
QUEUE_CONNECTION=redis

# Payment Integration
BINANCE_API_KEY=your_binance_key
BINANCE_SECRET_KEY=your_binance_secret
STRIPE_KEY=pk_live_your_stripe_key
STRIPE_SECRET=sk_live_your_stripe_secret

# File Storage
FILESYSTEM_DISK=s3
AWS_ACCESS_KEY_ID=your_access_key
AWS_SECRET_ACCESS_KEY=your_secret_key
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=your-bucket-name

# Mail Configuration
MAIL_MAILER=ses
MAIL_HOST=email-smtp.us-east-1.amazonaws.com
MAIL_PORT=587
MAIL_USERNAME=your_ses_username
MAIL_PASSWORD=your_ses_password
```

### Update: Additional Environment Variables
Additional configuration for installed packages:
```
# Two-Factor Authentication
GOOGLE_2FA_ENABLED=true

# Backup Configuration  
BACKUP_ARCHIVE_PASSWORD=secure_backup_password

# Activity Logging
ACTIVITY_LOGGER_ENABLED=true

# Media Library
MEDIA_DISK=public

# Queue Configuration
QUEUE_CONNECTION=redis
REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379
```

## 📝 AI Agent Instructions

> ⚠️ **Critical Requirements for AI Agents**
> **All AI agents MUST follow this guide exactly. Deviations will cause integration failures.**

### Code Generation Standards
1. **Always implement interfaces before concrete classes**
2. **Use readonly DTOs for data transfer between layers**
3. **Include comprehensive validation and error handling**
4. **Generate complete test coverage for all methods**
5. **Follow UUID + ID hybrid pattern for all models**
6. **Implement proper caching strategies**
7. **Add audit logging for sensitive operations**
8. **Support multi-language and RTL requirements**

### Update: Package-Specific Code Generation
AI agents must integrate these installed packages:
- **Use Spatie Permission:** for role-based access control
- **Use Spatie Media Library:** for file uploads and management
- **Use Laravel Sanctum:** for API authentication
- **Use Laravel Breeze views:** as base for authentication UI
- **Use Spatie Activity Log:** for user action tracking
- **Use Laravel Money:** for currency calculations

### Quality Gates
All generated code must pass:
```
# Quality Validation Commands
./vendor/bin/pint --test         # Code style validation
./vendor/bin/phpstan analyse --level=8  # Static analysis  
php artisan test --coverage --min=90    # Test coverage
php artisan route:list           # Route validation
php artisan config:cache         # Configuration validation
```

## 🔄 Version Control

### Git Workflow
- **Main Branch:** Production-ready code only
- **Develop Branch:** Integration branch for features
- **Feature Branches:** Individual feature development
- **Hotfix Branches:** Critical production fixes

### Commit Message Format
```
Type(scope): Description

Types:
- feat: New feature
- fix: Bug fix  
- docs: Documentation
- style: Code style changes
- refactor: Code refactoring
- test: Test additions
- chore: Maintenance tasks

Example:
feat(auth): add two-factor authentication support
fix(payment): resolve Binance API timeout issue
docs(api): update endpoint documentation
```

> ✅ **Project Guide Compliance**
> This PROJECT_GUIDE.md serves as the single source of truth for all development decisions. All AI agents and team members must reference this document for consistent implementation.
