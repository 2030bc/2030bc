# PACKAGES.md - Laravel Docu-Course Platform

## 📦 Laravel 12.24.0 Installation Process

```bash
# Method 2: Using Composer Create-Project
composer create-project laravel/laravel docu-course-platform "^12.0"
cd docu-course-platform

# Verify Laravel version
php artisan --version

# install Laravel Sanctum via the install:api
php artisan install:api

# Install Breeze if not installed during project creation
composer require laravel/breeze --dev
php artisan breeze:install blade --dark

# Install frontend dependencies
npm install && npm run build

# Generate application key
php artisan key:generate

# Create storage link
php artisan storage:link
```

## 📦 Complete Package Installation Guide

### > Update: Package Alignment with Actual composer.json

The following packages are **EXACTLY** what's installed in your composer.json:

### Core Laravel Framework & Authentication

```bash
# Core Framework (Already Installed)
"laravel/framework": "^12.0"
"php": "^8.2"

# Authentication & Authorization (Already Installed)
"laravel/sanctum": "^4.0"
"laravel/socialite": "^5.23"
"laravel/tinker": "^2.10.1"
"pragmarx/google2fa-laravel": "^2.3"

# Laravel Breeze (Dev Dependency - Already Installed)
"laravel/breeze": "^2.3"
```

### Payment & Currency Systems (Already Installed)

```bash
# Payment Gateways
"binance/binance-connector-php": "^2.0"      # ✅ Binance API Integration
"stripe/stripe-php": "^17.6"                # ✅ Stripe Payment Processing

# Currency & Money Handling
"akaunting/laravel-money": "^6.0"           # ✅ Money formatting & conversion
"florianv/exchanger": "^2.8"               # ✅ Currency exchange rates
"moneyphp/money": "^4.7"                   # ✅ Money value objects

# Exchange Rate Adapters
"php-http/guzzle7-adapter": "^1.1"         # ✅ HTTP adapter for currency APIs
```

### File Handling & Media (Already Installed)

```bash
# Image Processing
"intervention/image": "*"                   # ✅ Image manipulation
"spatie/image-optimizer": "^1.8"           # ✅ Image optimization
"spatie/laravel-image-optimizer": "^1.8"   # ✅ Laravel image optimization
"spatie/laravel-medialibrary": "^11.14"    # ✅ Media management

# File Storage & Cloud
"league/flysystem-aws-s3-v3": "^3.29"      # ✅ AWS S3 integration

# Document Generation
"barryvdh/laravel-dompdf": "^3.1"          # ✅ PDF generation
"mpdf/mpdf": "^8.2"                        # ✅ Advanced PDF generation

# Excel/Spreadsheet
"maatwebsite/excel": "^3.1"                # ✅ Excel import/export

# Markdown Processing
"spatie/laravel-markdown": "^2.7"          # ✅ Markdown to HTML
"league/html-to-markdown": "^5.1"          # ✅ HTML to Markdown
```

### Spatie Package Suite (Already Installed)

```bash
# Content & Data Management
"spatie/laravel-activitylog": "^4.10"      # ✅ Activity logging
"spatie/laravel-backup": "^9.3"            # ✅ Application backups
"spatie/laravel-database-mail-templates": "^3.7"  # ✅ Mail templates
"spatie/laravel-fractal": "^6.3"           # ✅ API transformations
"spatie/laravel-html": "^3.12"             # ✅ HTML helpers
"spatie/laravel-permission": "^6.21"       # ✅ Role-based permissions
"spatie/laravel-query-builder": "^6.3"     # ✅ API query filtering
"spatie/laravel-searchable": "^1.13"       # ✅ Model searching
"spatie/laravel-sitemap": "^7.3"           # ✅ XML sitemap generation
"spatie/laravel-translatable": "^6.11"     # ✅ Multi-language models
"spatie/laravel-validation-rules": "^3.4"  # ✅ Custom validation rules

# Performance & Monitoring
"spatie/laravel-rate-limited-job-middleware": "^2.8"  # ✅ Job rate limiting
"spatie/laravel-schedule-monitor": "^4.0"   # ✅ Schedule monitoring
"spatie/laravel-server-monitor": "^1.10"    # ✅ Server monitoring
```

### Multi-Language & Localization (Already Installed)

```bash
# Localization Support
"mcamara/laravel-localization": "^2.3"     # ✅ URL-based localization
```

### HTTP & API (Already Installed)

```bash
# HTTP Client
"guzzlehttp/guzzle": "^7.10"               # ✅ HTTP client

# Caching & Performance
"predis/predis": "^3.2"                    # ✅ Redis client

# Search
"laravel/scout": "^10.19"                  # ✅ Full-text search

# Unique Identifiers
"ramsey/uuid": "^4.9"                      # ✅ UUID generation
```

### Development & Testing Tools (Already Installed)

```bash
# Code Quality & Analysis
"larastan/larastan": "^3.6"                # ✅ PHPStan for Laravel
"phpstan/phpstan": "^2.1"                  # ✅ Static analysis
"rector/rector": "^2.1"                    # ✅ Code modernization

# Code Formatting
"laravel/pint": "^1.24"                    # ✅ PHP code style fixer

# Development Tools
"barryvdh/laravel-debugbar": "^3.16"       # ✅ Debug toolbar
"laravel/telescope": "^5.11"               # ✅ Application insights
"laravel/sail": "^1.41"                    # ✅ Docker development
"laravel/pail": "^1.2.2"                   # ✅ Log viewing
"spatie/laravel-ray": "^1.40"              # ✅ Debug tool

# Testing
"pestphp/pest": "^3.8"                     # ✅ Testing framework
"pestphp/pest-plugin-laravel": "^3.2"      # ✅ Laravel testing plugin
"phpunit/phpunit": "^11.5"                 # ✅ PHPUnit
"mockery/mockery": "^1.6"                  # ✅ Mocking library
"nunomaduro/collision": "^8.6"             # ✅ Error reporting

# Database Tools
"doctrine/dbal": "^4.3"                    # ✅ Database abstraction

# Fake Data Generation
"fakerphp/faker": "^1.24"                  # ✅ Fake data generation
```

## 🔧 Package Configuration & Usage

### > Update: Laravel Breeze Configuration

```php
// config/auth.php - Breeze Authentication Configuration
return [
    'defaults' => [
        'guard' => 'web',
        'passwords' => 'users',
    ],

    'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'users',
        ],
        'api' => [  // Added by Sanctum
            'driver' => 'sanctum',
            'provider' => 'users',
        ],
    ],

    'providers' => [
        'users' => [
            'driver' => 'eloquent',
            'model' => App\Models\User::class,
        ],
    ],
];
```

### > Update: Sanctum API Authentication Configuration

```php
// config/sanctum.php
return [
    'stateful' => explode(',', env('SANCTUM_STATEFUL_DOMAINS', sprintf(
        '%s%s',
        'localhost,localhost:3000,127.0.0.1,127.0.0.1:8000,::1',
        Sanctum::currentApplicationUrlWithPort()
    ))),

    'guard' => ['web'],

    'expiration' => null,

    'token_prefix' => env('SANCTUM_TOKEN_PREFIX', ''),

    'middleware' => [
        'verify_csrf_token' => App\Http\Middleware\VerifyCsrfToken::class,
        'encrypt_cookies' => App\Http\Middleware\EncryptCookies::class,
    ],
];
```

### > Update: Spatie Permission Configuration

```php
// config/permission.php (Published after installation)
return [
    'models' => [
        'permission' => Spatie\Permission\Models\Permission::class,
        'role' => Spatie\Permission\Models\Role::class,
    ],

    'table_names' => [
        'roles' => 'roles',
        'permissions' => 'permissions',
        'model_has_permissions' => 'model_has_permissions',
        'model_has_roles' => 'model_has_roles',
        'role_has_permissions' => 'role_has_permissions',
    ],

    'cache' => [
        'expiration_time' => \DateInterval::createFromDateString('24 hours'),
        'key' => 'spatie.permission.cache',
        'store' => 'default',
    ],
];
```

### > Update: Payment Configuration

```php
// config/services.php - Payment Gateway Configuration
return [
    // Stripe Configuration
    'stripe' => [
        'model' => App\Models\User::class,
        'key' => env('STRIPE_KEY'),
        'secret' => env('STRIPE_SECRET'),
        'webhook' => [
            'secret' => env('STRIPE_WEBHOOK_SECRET'),
            'tolerance' => env('STRIPE_WEBHOOK_TOLERANCE', 300),
        ],
    ],

    // Binance Configuration
    'binance' => [
        'api_key' => env('BINANCE_API_KEY'),
        'secret_key' => env('BINANCE_SECRET_KEY'),
        'sandbox' => env('BINANCE_SANDBOX', false),
    ],

    // Currency Exchange Configuration
    'exchanger' => [
        'default' => env('EXCHANGER_DEFAULT_SERVICE', 'fixer'),
        'services' => [
            'fixer' => [
                'access_key' => env('FIXER_ACCESS_KEY'),
            ],
        ],
    ],
];
```

## 🗄️ Database Migrations Executed

### > Update: Executed Migration Files

```bash
# Laravel Core Migrations (Executed)
0001_01_01_000000_create_users_table.php           # ✅ Laravel/Breeze
0001_01_01_000001_create_cache_table.php           # ✅ Laravel
0001_01_01_000002_create_jobs_table.php            # ✅ Laravel

# Package Migrations (Executed)
2025_08_29_211228_create_media_table.php           # ✅ Spatie MediaLibrary
2025_08_29_211330_create_permission_tables.php     # ✅ Spatie Permission
2025_08_29_211413_create_personal_access_tokens_table.php  # ✅ Laravel Sanctum
2025_08_29_211552_create_telescope_entries_table.php       # ✅ Laravel Telescope
2025_08_29_211842_create_activity_log_table.php    # ✅ Spatie ActivityLog
2025_08_29_211843_add_event_column_to_activity_log_table.php    # ✅ Spatie ActivityLog
2025_08_29_211844_add_batch_uuid_column_to_activity_log_table.php   # ✅ Spatie ActivityLog
```

## 🚀 Ready-to-Use Features

### > Update: Available Features with Installed Packages

#### Authentication (Laravel Breeze + Sanctum)
```php
// User Registration (Breeze)
Route::post('/register', [RegisteredUserController::class, 'store']);

// API Authentication (Sanctum)
Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
    return $request->user();
});

// Create API Token
$token = $user->createToken('api-token')->plainTextToken;
```

#### Payment Processing (Stripe + Binance)
```php
// Stripe Payment Intent
use Stripe\StripeClient;

$stripe = new StripeClient(config('services.stripe.secret'));
$paymentIntent = $stripe->paymentIntents->create([
    'amount' => 2000,
    'currency' => 'usd',
]);

// Binance API Connection
use Binance\API;

$api = new API(
    config('services.binance.api_key'),
    config('services.binance.secret_key')
);
```

#### File & Media Management (Spatie)
```php
// Media Library Usage
use Spatie\MediaLibrary\InteractsWithMedia;

class Course extends Model implements HasMedia
{
    use InteractsWithMedia;

    public function registerMediaCollections(): void
    {
        $this->addMediaCollection('images')
              ->acceptsMimeTypes(['image/jpeg', 'image/png']);
    }
}

// Add media to model
$course->addMediaFromRequest('image')->toMediaCollection('images');
```

#### Permissions & Roles (Spatie)
```php
// Create Roles and Permissions
use Spatie\Permission\Models\Role;
use Spatie\Permission\Models\Permission;

$role = Role::create(['name' => 'admin']);
$permission = Permission::create(['name' => 'manage courses']);

// Assign to User
$user->assignRole('admin');
$user->givePermissionTo('manage courses');

// Check Permissions
if ($user->can('manage courses')) {
    // User has permission
}
```

#### Activity Logging (Spatie)
```php
// Automatic Activity Logging
use Spatie\Activitylog\Traits\LogsActivity;

class Course extends Model
{
    use LogsActivity;

    protected static $logAttributes = ['title', 'description'];
    protected static $logOnlyDirty = true;
}

// Manual Activity Logging
activity()
    ->performedOn($course)
    ->causedBy($user)
    ->log('Course updated');
```

#### Multi-Language Support
```php
// Localization Middleware (mcamara/laravel-localization)
Route::group([
    'prefix' => LaravelLocalization::setLocale(),
    'middleware' => ['localeSessionRedirect', 'localizationRedirect']
], function() {
    Route::get('/', 'HomeController@index');
    Route::get('/courses', 'CourseController@index');
});

// Translatable Models (spatie/laravel-translatable)
use Spatie\Translatable\HasTranslations;

class Course extends Model
{
    use HasTranslations;

    public $translatable = ['title', 'description'];
}

// Usage
$course->setTranslation('title', 'en', 'Laravel Course');
$course->setTranslation('title', 'ar', 'دورة لارافيل');
echo $course->getTranslation('title', 'ar');
```

## 🔧 Environment Configuration

### > Update: Required .env Variables

```bash
# Application
APP_NAME="Laravel Docu-Course Platform"
APP_ENV=local
APP_KEY=base64:generated_key_here
APP_DEBUG=true
APP_URL=http://localhost

# Database
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=docu_course_platform
DB_USERNAME=root
DB_PASSWORD=

# Cache & Session
CACHE_DRIVER=redis
SESSION_DRIVER=redis
QUEUE_CONNECTION=redis

# Redis
REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

# Payment Gateways
STRIPE_KEY=pk_test_your_stripe_key
STRIPE_SECRET=sk_test_your_stripe_secret
STRIPE_WEBHOOK_SECRET=whsec_your_webhook_secret

BINANCE_API_KEY=your_binance_api_key
BINANCE_SECRET_KEY=your_binance_secret_key
BINANCE_SANDBOX=true

# File Storage
FILESYSTEM_DISK=local
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=

# Mail Configuration
MAIL_MAILER=smtp
MAIL_HOST=mailhog
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null

# Currency Exchange
FIXER_ACCESS_KEY=your_fixer_api_key
EXCHANGER_DEFAULT_SERVICE=fixer

# Search
SCOUT_DRIVER=meilisearch
MEILISEARCH_HOST=http://localhost:7700
```

## ✅ Installation Verification

### > Update: Package Verification Commands

```bash
# Verify Core Installation
php artisan --version
php artisan route:list
php artisan config:cache

# Verify Package Installation
composer show laravel/breeze
composer show laravel/sanctum
composer show spatie/laravel-permission
composer show stripe/stripe-php
composer show binance/binance-connector-php

# Verify Database
php artisan migrate:status
php artisan db:show

# Verify Cache
php artisan cache:clear
php artisan config:clear

# Run Tests
php artisan test

# Check Package Configurations
php artisan vendor:publish --tag=config
ls -la config/
```

## 📝 Development Workflow

### > Update: Development Commands

```bash
# Start Development Environment
php artisan serve
npm run dev

# Queue Workers (for jobs)
php artisan queue:work

# Schedule Runner (for cron jobs)
php artisan schedule:work

# Database Operations
php artisan migrate
php artisan db:seed

# Clear Caches
php artisan optimize:clear

# Code Quality
./vendor/bin/pint
./vendor/bin/phpstan analyse
```

## 🎉 Installation Complete

### Features Ready to Use:
- ✅ **Laravel Breeze Authentication** (Login, Register, Password Reset)
- ✅ **Sanctum API Authentication** (Token-based API access)
- ✅ **Spatie Permission System** (Roles & Permissions)
- ✅ **Stripe Payment Processing** (Credit cards, subscriptions)
- ✅ **Binance Crypto Payments** (Cryptocurrency integration)
- ✅ **Media Library** (File uploads, image processing)
- ✅ **Activity Logging** (User action tracking)
- ✅ **Multi-Language Support** (Localization & translations)
- ✅ **Search Functionality** (Laravel Scout integration)
- ✅ **Backup System** (Application & database backups)
- ✅ **Performance Monitoring** (Laravel Telescope)
- ✅ **Testing Framework** (PHPUnit + Pest)

### Next Steps:
1. Configure payment gateways (Stripe & Binance)
2. Set up roles and permissions
3. Create course models and migrations
4. Implement multi-language content
5. Configure media collections
6. Set up scheduled tasks and queues

> ✨ **All packages are installed and ready for development based on your actual composer.json!**