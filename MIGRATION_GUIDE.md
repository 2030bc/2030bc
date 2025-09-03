# MIGRATION_GUIDE.md - Laravel Docu-Course Platform Migration Guide

## Overview

This guide defines the complete database migration procedures for the Laravel 12.24.0 docu-course platform. All migrations must be executed in the specified order to maintain referential integrity.

## Executed Migrations Update

**The following migrations have been executed and are part of the current system:**

- Laravel Breeze authentication tables (users, password resets)
- Laravel Sanctum personal access tokens
- Spatie Media Library tables
- Spatie Permission tables (roles, permissions, model_has_permissions)
- Spatie Activity Log tables with batch UUID support
- Laravel Telescope debugging tables
- Standard Laravel cache and jobs tables

## Migration Philosophy

- **Sequential Execution** for dependency management
- **Rollback Safety** for all migrations
- **Data Integrity** with foreign key constraints
- **Performance Optimization** through proper indexing
- **Multi-Environment Support** (development, staging, production)

## Executed Migrations Order (Based on Actual System)

**Phase 0: Laravel & Package Foundation (Already Executed)**

```
# Laravel Core Tables (Executed)
0001_01_01_000000_create_users_table.php           # Laravel/Breeze users
0001_01_01_000001_create_cache_table.php           # Laravel cache
0001_01_01_000002_create_jobs_table.php            # Laravel queue jobs

# Package Migrations (Executed)
2025_08_29_211228_create_media_table.php           # Spatie Media Library
2025_08_29_211330_create_permission_tables.php     # Spatie Permission
2025_08_29_211413_create_personal_access_tokens_table.php # Laravel Sanctum
2025_08_29_211552_create_telescope_entries_table.php      # Laravel Telescope
2025_08_29_211842_create_activity_log_table.php           # Spatie Activity Log
2025_08_29_211843_add_event_column_to_activity_log_table.php
2025_08_29_211844_add_batch_uuid_column_to_activity_log_table.php
```

## Phase 1: Core System Tables (Priority: Critical)

```
# 1. Foundation tables (no dependencies)
2024_01_02_000000_create_languages_table.php
2024_01_03_000000_create_currencies_table.php
2024_01_04_000000_create_categories_table.php

# 2. User extension tables
2024_01_08_000000_create_user_profiles_table.php
2024_01_09_000000_create_user_levels_table.php
2024_01_10_000000_create_user_progress_table.php
```

## Phase 2: Content Management (Priority: High)

```
# 3. Course and content tables
2024_01_05_000000_create_docu_courses_table.php
2024_01_06_000000_create_episodes_table.php
2024_01_07_000000_create_episode_parts_table.php
2024_01_22_000000_create_course_uploads_table.php

# 4. User interaction tables
2024_01_20_000000_create_user_notes_table.php
2024_01_21_000000_create_user_reactions_table.php
```

## Phase 3: Affiliate & Payment System (Priority: High)

```
# 5. Affiliate system tables
2024_01_11_000000_create_affiliates_table.php
2024_01_12_000000_create_referral_codes_table.php
2024_01_13_000000_create_affiliate_clicks_table.php
2024_01_14_000000_create_conversions_table.php
2024_01_15_000000_create_commissions_table.php
2024_01_16_000000_create_social_shares_table.php

# 6. Payment system tables (Stripe & Binance integration)
2024_01_17_000000_create_transactions_table.php
2024_01_18_000000_create_payment_methods_table.php
2024_01_19_000000_create_invoices_table.php
```

## Phase 4: System Administration (Priority: Medium)

```
# 7. System management tables
2024_01_23_000000_create_configurations_table.php
2024_01_24_000000_create_system_metrics_table.php
```

## Phase 5: Performance & Integrity (Priority: Medium)

```
# 8. Optimization and constraints
2024_01_25_000000_add_indexes_for_performance.php
2024_01_26_000000_add_foreign_key_constraints.php
```

## Package Integration Commands

**Commands to maintain package migrations:**

```
# Check executed migrations status
php artisan migrate:status

# Publish package migrations (if needed for updates)
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider" --tag="permission-migrations"
php artisan vendor:publish --provider="Spatie\Activitylog\ActivitylogServiceProvider" --tag="activitylog-migrations"
php artisan vendor:publish --provider="Spatie\MediaLibrary\MediaLibraryServiceProvider" --tag="medialibrary-migrations"
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider" --tag="sanctum-migrations"
php artisan vendor:publish --provider="Laravel\Telescope\TelescopeServiceProvider" --tag="telescope-migrations"

# Fresh migration (DEVELOPMENT ONLY - destroys data)
php artisan migrate:fresh --seed

# Production-safe migration
php artisan migrate --force
```

## Migration Execution Procedures

### Development Environment

```
# Fresh migration (destroys existing data)
php artisan migrate:fresh --seed

# Step-by-step migration
php artisan migrate --step

# Rollback last batch
php artisan migrate:rollback

# Rollback specific steps
php artisan migrate:rollback --step=5

# Check migration status
php artisan migrate:status
```

### Staging Environment

```
# Backup database before migration
php artisan backup:database --env=staging

# Run migrations with confirmation
php artisan migrate --force

# Verify data integrity
php artisan db:check-integrity

# Run seeders for staging data
php artisan db:seed --class=StagingSeeder
```

### Production Environment

```
# CRITICAL: Always backup before production migrations
php artisan backup:database --env=production

# Put application in maintenance mode
php artisan down --message="Database Migration in Progress"

# Run migrations
php artisan migrate --force

# Clear all caches
php artisan optimize:clear

# Rebuild caches
php artisan optimize

# Remove maintenance mode
php artisan up

# Verify application health
php artisan health:check
```

## Data Seeding with Package Support

**Updated seeder execution order including package data:**

```
# DatabaseSeeder.php with package support
public function run(): void
{
    // 1. System foundation data
    $this->call([
        LanguageSeeder::class,
        CurrencySeeder::class,
        UserLevelSeeder::class,
        CategorySeeder::class,
    ]);

    // 2. Package data (Spatie Permissions)
    $this->call([
        RoleAndPermissionSeeder::class,
    ]);

    // 3. User data with permissions
    $this->call([
        UserSeeder::class,
        AdminSeeder::class,
    ]);

    // 4. Content data (only in non-production)
    if (!app()->environment('production')) {
        $this->call([
            DocuCourseSeeder::class,
            EpisodeSeeder::class,
            MediaSeeder::class, // Spatie Media Library
            TestDataSeeder::class,
        ]);
    }
}
```

## Package-Specific Migration Health Checks

```
# Check Spatie package tables
php -r "
$tables = ['permissions', 'roles', 'model_has_permissions', 'model_has_roles', 'role_has_permissions', 'media', 'activity_log'];
foreach(\$tables as \$table) {
    if(Schema::hasTable(\$table)) {
        echo \"✅ Table \$table exists\n\";
    } else {
        echo \"❌ Missing table \$table\n\";
    }
}
"

# Check Laravel Sanctum
php artisan tinker --execute="echo Schema::hasTable('personal_access_tokens') ? '✅ Sanctum OK' : '❌ Sanctum Missing';"

# Check Laravel Telescope
php artisan tinker --execute="echo Schema::hasTable('telescope_entries') ? '✅ Telescope OK' : '❌ Telescope Missing';"
```

> **Important:** All package migrations listed above have been executed and are part of the current system. The remaining migrations (Phase 1-5) need to be executed to complete the platform setup.

**This migration guide ensures proper database setup with all required Laravel packages and maintains data integrity throughout the development and deployment process.**
