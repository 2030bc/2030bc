# PROJECT_PROCESS.md - Full Project Steps for Laravel Docu-Course Platform

This document outlines the complete, best-practice workflow for building the platform, from installation to production. Each step includes a description, the PHP files to generate or modify, the required .md files to consult, and suggested test files. The process is designed to build the application on a solid foundation, implementing security and business logic before the HTTP layer, ensuring a robust and production-ready codebase.

> **Update: Laravel Breeze & Package Integration**
> 
> This process has been updated to reflect the installed Laravel Breeze with Blade templates, comprehensive Spatie package integration, Stripe and Binance payment processing, and the executed migration files. All steps now account for the actual installed package stack.

---

## Phase 1: Foundation & Setup

### 1. Installation & Environment Setup
- **Description:** Install Laravel, required packages, and set up environment variables. Laravel Breeze with Blade templates is installed and configured with Tailwind CSS.
- **PHP Files:** None (initial setup complete)
- **MD Files to Consult:**
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PACKAGES.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PROJECT_GUIDE.md
- **Test Files:**
  - scripts/quality-check.sh (checks installation, PHP version, Composer, NPM)

> **Note: Executed Installation Steps**
> 
> The following have been completed:
> - Laravel 12.x installation
> - Laravel Breeze with Blade templates
> - Laravel Sanctum API authentication
> - All Spatie packages (Permission, Media Library, Activity Log, etc.)
> - Stripe and Binance payment SDKs
> - Testing frameworks (PHPUnit, Pest)
> - Development tools (Telescope, Debugbar)

### 2. Documentation & Planning
- **Description:** Review all architecture, schema, and process documentation to ensure alignment with installed packages and executed migrations.
- **PHP Files:** None (planning phase)
- **MD Files to Consult:**
  - https://raw.githubusercontent.com/2030bc/2030bc/main/ARCHITECTURE_DECISIONS.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PROJECT_GUIDE.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/DATABASE_SCHEMA_V2.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PROJECT_DIRECTORY_STRUCTURE.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/APP_ENUM.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/APP_DTO.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/APP_MODEL.md
- **Test Files:** None

> **Update: Executed Migrations Status**
> 
> The following migrations have been executed and must be considered:
> - Users table (Laravel/Breeze) - 0001_01_01_000000_create_users_table.php
> - Cache table (Laravel) - 0001_01_01_000001_create_cache_table.php
> - Jobs table (Laravel) - 0001_01_01_000002_create_jobs_table.php
> - Media table (Spatie) - 2025_08_29_211228_create_media_table.php
> - Permission tables (Spatie) - 2025_08_29_211330_create_permission_tables.php
> - Personal access tokens (Sanctum) - 2025_08_29_211413_create_personal_access_tokens_table.php
> - Telescope entries - 2025_08_29_211552_create_telescope_entries_table.php
> - Activity log (Spatie) - 2025_08_29_211842_create_activity_log_table.php

---

## Phase 2: Database & Core Structure

### 3. Database Migrations & Seeders
- **Description:** Create migration and seeder files for all application-specific tables, building upon the already executed package migrations. Use DATABASE_SCHEMA_V2.md as the source of truth for custom tables.
- **PHP Files:**
  - database/migrations/* (custom business logic tables only)
  - database/seeders/* (seeders for languages, currencies, user levels, etc.)
- **MD Files to Consult:**
  - https://raw.githubusercontent.com/2030bc/2030bc/main/DATABASE_SCHEMA_V2.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PROJECT_GUIDE.md
- **Test Files:**
  - tests/Feature/Database/MigrationTest.php (tests migration up/down)
  - tests/Feature/Database/SeederTest.php (tests seeder data integrity)

> **Package Integration Notes:**
> 
> - Spatie Media Library migrations are executed - integrate with course content storage
> - Spatie Permission tables are ready - implement role-based access control
> - Sanctum tokens table exists - use for API authentication
> - Activity log table available - implement audit trails

### 4. Generate Enums & DTOs
- **Description:** Create all PHP Enum and DTO files for type safety and data contracts, ensuring compatibility with installed packages.
- **PHP Files:**
  - app/Enums/* (all enums listed in APP_ENUM.md)
  - app/DTOs/* (all DTOs listed in APP_DTO.md)
- **MD Files to Consult:**
  - https://raw.githubusercontent.com/2030bc/2030bc/main/APP_ENUM.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/APP_DTO.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PROJECT_GUIDE.md
- **Test Files:**
  - tests/Unit/Enums/EnumTest.php (tests enum values and mappings)
  - tests/Unit/DTOs/DTOTest.php (tests DTO instantiation and validation)

> **Update: Package-Specific Enums**
> 
> Include enums for:
> - Spatie Permission roles and abilities
> - Payment provider types (Stripe, Binance)
> - Media library collection types
> - Activity log event types

### 5. Eloquent Models
- **Description:** Generate all Eloquent models with relationships, attributes, and business methods. Integrate with Spatie package traits and Laravel Sanctum.
- **PHP Files:**
  - app/Models/* (all models listed in APP_MODEL.md)
- **MD Files to Consult:**
  - https://raw.githubusercontent.com/2030bc/2030bc/main/APP_MODEL.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/APP_ENUM.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/DATABASE_SCHEMA_V2.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PROJECT_GUIDE.md
- **Test Files:**
  - tests/Unit/Models/* (one test per model, tests relationships and methods)

> **Package Integration Requirements:**
> 
> Models must include:
> - HasPermissions trait for role-based access
> - HasMedia trait for file attachments
> - HasActivity trait for audit logging
> - HasApiTokens trait for Sanctum authentication
> - Proper factory and seeder integration

---

## Phase 3: Business Logic & Validation

### 6. Service Layer
- **Description:** Implement service classes for business logic, using DTOs and models. Incorporate payment processing with Stripe and Binance SDKs.
- **PHP Files:**
  - app/Services/* (by domain: User, Course, Payment, Affiliate, etc.)
- **MD Files to Consult:**
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PROJECT_GUIDE.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/APP_DTO.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/APP_MODEL.md
- **Test Files:**
  - tests/Unit/Services/* (tests service logic and edge cases)

> **Payment Integration Services:**
> 
> - StripePaymentService for traditional payments
> - BinancePaymentService for cryptocurrency
> - PaymentProcessorService as abstraction layer
> - CurrencyExchangeService for multi-currency support

### 7. Form Requests & Validation
- **Description:** Create Form Request classes for input validation, leveraging Laravel's validation system and Spatie validation rules.
- **PHP Files:**
  - app/Http/Requests/* (by domain and feature)
- **MD Files to Consult:**
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PROJECT_GUIDE.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/APP_DTO.md
- **Test Files:**
  - tests/Feature/Requests/* (tests validation rules and error messages)

> **Update: Spatie Validation Integration**
> 
> Utilize Spatie Laravel Validation Rules package for:
> - Advanced validation rules
> - Custom validation messages
> - Multi-language validation support

---

## Phase 4: Security & Request Handling Foundation

### 8. Middleware
- **Description:** Implement middleware for authentication, authorization, localization, and other HTTP-level filters. This sets the pipeline that all requests will flow through, integrating with Laravel Breeze and Spatie Permission.
- **PHP Files:**
  - app/Http/Middleware/*
- **MD Files to Consult:**
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PROJECT_GUIDE.md
- **Test Files:**
  - tests/Feature/Middleware/* (tests middleware logic and edge cases)

> **Package Integration Middleware:**
> 
> - Spatie Permission middleware for role checking
> - Laravel Sanctum auth middleware for API routes
> - Custom middleware for activity logging
> - Rate limiting middleware integration

### 9. Policies & Gates
- **Description:** Set up policies and gates for granular access control and business logic authorization. This defines the rules that controllers and services will enforce, working with Spatie Permission package.
- **PHP Files:**
  - app/Policies/*
  - AuthServiceProvider.php
- **MD Files to Consult:**
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PROJECT_GUIDE.md
- **Test Files:**
  - tests/Feature/Policies/* (tests policy logic and permissions)

> **Spatie Permission Integration:**
> 
> - Role-based policies using Spatie Permission
> - Dynamic permission checking
> - Integration with user level system
> - API permission scopes with Sanctum

---

## Phase 5: Application & HTTP Layer

### 10. API Resources
- **Description:** Create API Resource classes for consistent and formatted API responses, compatible with Laravel Sanctum authentication.
- **PHP Files:**
  - app/Http/Resources/* (by domain and feature)
- **MD Files to Consult:**
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PROJECT_GUIDE.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/APP_DTO.md
- **Test Files:**
  - tests/Feature/Resources/* (tests resource output formatting)

> **Sanctum API Resources:**
> 
> - User resources with token abilities
> - Permission-aware resource filtering
> - Media library resource integration
> - Payment resource with sensitive data masking

### 11. Controllers
- **Description:** Build thin controllers that delegate to services and handle HTTP requests. They can now safely use auth() and authorize() as the security foundation is in place. Extend Laravel Breeze controllers where appropriate.
- **PHP Files:**
  - app/Http/Controllers/* (by domain and feature)
- **MD Files to Consult:**
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PROJECT_GUIDE.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/APP_MODEL.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/APP_DTO.md
- **Test Files:**
  - tests/Feature/Controllers/* (tests controller endpoints and responses)

> **Laravel Breeze Integration:**
> 
> - Extend Breeze authentication controllers
> - Integrate with Breeze views and components
> - Maintain Breeze styling consistency
> - Add custom authentication features

### 12. Routes & Configuration
- **Description:** Configure routes and finalize all config files. Apply middleware and bind policies to routes now that they are defined and tested. Configure Sanctum API routes and Spatie package settings.
- **PHP Files:**
  - routes/web.php, api.php, admin.php, etc.
  - config/*
- **MD Files to Consult:**
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PROJECT_GUIDE.md
- **Test Files:**
  - tests/Feature/Routes/RouteTest.php (tests route accessibility, middleware, and binding)

> **Package Configuration:**
> 
> - Sanctum API route configuration
> - Spatie Permission gate definitions
> - Media library disk configurations
> - Payment provider webhook routes
> - Activity log channel configuration

---

## Phase 6: Presentation Layer

### 13. Blade Views & Frontend Assets
- **Description:** Build Blade views and integrate frontend assets (JS, CSS) as needed. Extend Laravel Breeze Blade templates and maintain Tailwind CSS consistency.
- **PHP Files:**
  - resources/views/*
  - resources/js/*
  - resources/css/*
- **MD Files to Consult:**
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PROJECT_GUIDE.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/FRONTEND_GUIDE.md
- **Test Files:**
  - tests/Browser/* (Dusk tests for critical user flows)

> **Laravel Breeze Frontend Integration:**
> 
> - Extend Breeze Blade components
> - Maintain Tailwind CSS consistency
> - Integrate Alpine.js components
> - Custom authentication views
> - Media library file upload interfaces

---

## Phase 7: Production Readiness

### 14. Quality Assurance & Production Readiness
- **Description:** Run all quality gates, static analysis, and tests. Prepare for deployment with all installed packages properly configured.
- **PHP Files:**
  - scripts/quality-check.sh
  - scripts/deploy.sh
- **MD Files to Consult:**
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PROJECT_GUIDE.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/TESTING_GUIDE.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/PERFORMANCE_GUIDE.md
  - https://raw.githubusercontent.com/2030bc/2030bc/main/SECURITY_GUIDE.md
- **Test Files:**
  - All tests above, plus:
  - tests/Benchmark/PerformanceTest.php (performance)
  - tests/Integration/PaymentGatewayTest.php (integration)
  - tests/Integration/SpatiePackageTest.php (package integration)

> **Package-Specific Quality Checks:**
> 
> - Spatie package configuration validation
> - Payment provider connection testing
> - Media library storage permissions
> - Sanctum token generation testing
> - Activity log functionality verification
> - Laravel Breeze authentication flow testing

---

> **Key Principle:** For every step, always consult the listed `.md` files via their URLs for standards, structure, and requirements.
> Generate and test all files as described before moving to the next step. This ensures a robust, maintainable, and production-ready codebase.
> 
> **Security First:** Notice that the Security Foundation (Middleware & Policies) is built *before* the Controllers and Routes. This prevents writing insecure code and having to refactor it later.
> 

> **Package Integration:** All steps now account for the installed package stack including Laravel Breeze, Spatie packages, payment processors, and testing frameworks. Ensure proper integration and configuration at each phase.

