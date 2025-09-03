# APP_ENUM.md - Enums for Laravel Docu-Course Platform

This document lists all Enum files required for the platform, grouped by domain, with descriptions and possible values for each. Use this as a reference for AI agents to generate and maintain Enums consistently.

---

## 📁 User Enums

- **app/Enums/UserStatus.php**
  - Represents user account status. Values: active, inactive, banned, pending.

- **app/Enums/UserLevel.php**
  - User progression levels (1-14), used for access and benefits.

- **app/Enums/TextDirection.php**
  - Text direction for user interface. Values: ltr, rtl.

- **app/Enums/Gender.php**
  - User gender identity. Values: male, female, other, prefer_not_to_say.

## 📁 Course Enums

- **app/Enums/CourseStatus.php**
  - Course publication status. Values: draft, published, archived, private.

- **app/Enums/ContentAccessLevel.php**
  - Access requirements for content. Values: public, authenticated, level_restricted, premium, vip.

- **app/Enums/DifficultyLevel.php**
  - Course difficulty. Values: beginner, intermediate, advanced, expert.

## 📁 Payment Enums

- **app/Enums/PaymentStatus.php**
  - Payment and transaction status. Values: pending, completed, failed, refunded.

- **app/Enums/TransactionType.php**
  - Transaction types. Values: purchase, refund, withdrawal, commission, bonus.

- **app/Enums/PaymentProvider.php**
  - Payment providers. Values: binance, stripe, paypal, local, internal.

- **app/Enums/CurrencyType.php**
  - Currency types. Values: fiat, crypto, local, platform.

## 📁 Affiliate Enums

- **app/Enums/AffiliateStatus.php**
  - Affiliate account status. Values: pending, active, suspended, terminated.

- **app/Enums/ReferralType.php**
  - Referral code type. Values: signup, purchase, both.

- **app/Enums/ConversionType.php**
  - Conversion event type. Values: signup, purchase, subscription.

- **app/Enums/CommissionStatus.php**
  - Commission payout status. Values: pending, payable, paid, cancelled.

- **app/Enums/SocialPlatform.php**
  - Social sharing platforms. Values: facebook, twitter, linkedin, whatsapp, telegram, email.

## 📁 System Enums

- **app/Enums/LanguageDirection.php**
  - Language text direction. Values: ltr, rtl.

- **app/Enums/MetricPeriod.php**
  - System metric period. Values: minute, hourly, daily, weekly, monthly.

- **app/Enums/ConfigType.php**
  - Configuration value type. Values: string, integer, boolean, array, object.

## 📁 General Enums

- **app/Enums/DeviceType.php**
  - Device type for analytics. Values: desktop, mobile, tablet, unknown.

- **app/Enums/ContentType.php**
  - Content type for analytics and sharing. Values: course, episode, profile.

---

> Each Enum should be a PHP 8.2+ native enum, used for type safety and clarity throughout the application. Follow the structure and naming conventions as described in the project documentation.

### Package Integration Updates

Based on the installed packages and executed migrations, the following Enums need to be added:

## 📁 Authentication Enums (Laravel Breeze + Sanctum)

- **app/Enums/AuthProvider.php** - Authentication providers: local, google, facebook, github, twitter
- **app/Enums/TokenType.php** - Sanctum token types: api, mobile, web, admin
- **app/Enums/TwoFactorMethod.php** - 2FA methods: app, sms, email, backup_codes
- **app/Enums/SessionStatus.php** - Session states: active, expired, revoked, invalid

## 📁 Enhanced Payment Enums

- **app/Enums/StripeStatus.php** - Stripe-specific statuses: requires_payment_method, requires_confirmation, requires_action, processing, requires_capture, canceled, succeeded
- **app/Enums/BinanceOrderType.php** - Binance order types: market, limit, stop_loss, take_profit
- **app/Enums/WebhookEvent.php** - Webhook event types: payment_succeeded, payment_failed, subscription_created, invoice_paid
- **app/Enums/RefundReason.php** - Refund reasons: duplicate, fraudulent, requested_by_customer, subscription_canceled

## 📁 Permission & Role Enums (Spatie Permissions)

- **app/Enums/RoleType.php** - System roles: super_admin, admin, instructor, student, affiliate, guest
- **app/Enums/PermissionType.php** - Permission categories: course, user, payment, system, affiliate
- **app/Enums/GuardName.php** - Guard types: web, api, admin

## 📁 Media & File Enums (Spatie MediaLibrary)

- **app/Enums/MediaCollection.php** - Media collections: avatars, course_images, episode_files, documents
- **app/Enums/ImageConversion.php** - Image conversions: thumbnail, medium, large, webp
- **app/Enums/FileType.php** - Supported file types: image, video, audio, document, archive
- **app/Enums/StorageDisk.php** - Storage disks: local, public, s3, gcs

## 📁 Activity & Audit Enums

- **app/Enums/ActivityType.php** - Activity types: created, updated, deleted, viewed, downloaded
- **app/Enums/AuditEvent.php** - Audit events: login, logout, payment, course_access, profile_update
- **app/Enums/LogLevel.php** - Log severity: emergency, alert, critical, error, warning, notice, info, debug

## 📁 Search & Query Enums

- **app/Enums/SearchEngine.php** - Search engines: database, algolia, meilisearch, elasticsearch
- **app/Enums/SortDirection.php** - Sort directions: asc, desc
- **app/Enums/FilterOperator.php** - Filter operators: equals, contains, starts_with, ends_with, greater_than, less_than

## 📁 Queue & Job Enums

- **app/Enums/QueuePriority.php** - Job priorities: low, normal, high, critical
- **app/Enums/JobStatus.php** - Job statuses: pending, processing, completed, failed, retry
- **app/Enums/QueueConnection.php** - Queue connections: sync, database, redis, sqs

## 📁 Backup & Monitoring Enums

- **app/Enums/BackupType.php** - Backup types: database, files, full, incremental
- **app/Enums/BackupStatus.php** - Backup statuses: running, completed, failed, partial
- **app/Enums/MonitorStatus.php** - Server monitoring: online, offline, warning, critical

## 📁 Localization Enums

- **app/Enums/LocaleStatus.php** - Locale availability: active, inactive, maintenance
- **app/Enums/TranslationStatus.php** - Translation completeness: complete, partial, missing, review_needed