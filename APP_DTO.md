# APP_DTO.md - Data Transfer Objects for Laravel Docu-Course Platform

This document lists all DTO (Data Transfer Object) files required for the platform, grouped by domain, with descriptions for each. Use this as a reference for AI agents to generate and maintain DTOs consistently.

---

## 📁 User DTOs

- **app/DTOs/User/ProfileData.php**
  - Encapsulates user profile information (bio, website, social links, visibility settings).

- **app/DTOs/User/ProgressData.php**
  - Represents user progress in courses and episodes (completion %, time spent, last position).

- **app/DTOs/User/NoteData.php**
  - Transfers user note content, position, and visibility for episodes.

- **app/DTOs/User/ReactionData.php**
  - Contains data for user reactions (type, episode, timestamp).

- **app/DTOs/UserRegistrationData.php**
  - Used for user registration flows (email, password, referral, preferences).

## 📁 Course DTOs

- **app/DTOs/CourseData.php**
  - Main course data (title, alias, description, category, language, status, pricing).

- **app/DTOs/Course/EpisodeData.php**
  - Episode metadata (title, alias, description, file path, access level, status).

- **app/DTOs/Course/EpisodePartData.php**
  - Data for episode fragments (part number, title, file path, access level).

- **app/DTOs/Course/CategoryData.php**
  - Category details (name, slug, description, parent, sort order).

## 📁 Payment DTOs

- **app/DTOs/PaymentData.php**
  - General payment data (amount, currency, provider, status).

- **app/DTOs/Payment/TransactionData.php**
  - Transaction details (type, status, amount, provider, metadata).

- **app/DTOs/Payment/InvoiceData.php**
  - Invoice information (user, transaction, amount, status, items).

- **app/DTOs/Payment/PaymentMethodData.php**
  - Payment method details (type, provider, metadata, verification).

## 📁 Affiliate DTOs

- **app/DTOs/Affiliate/AffiliateData.php**
  - Affiliate profile (user, status, commission rate, earnings).

- **app/DTOs/Affiliate/ReferralCodeData.php**
  - Referral code info (affiliate, code, type, usage, expiration).

- **app/DTOs/Affiliate/CommissionData.php**
  - Commission record (affiliate, conversion, amount, rate, status).

- **app/DTOs/Affiliate/ConversionData.php**
  - Conversion event (affiliate click, type, user, amount, status).

- **app/DTOs/Affiliate/SocialShareData.php**
  - Social share details (user, platform, content type, share URL).

## 📁 System DTOs

- **app/DTOs/System/LanguageData.php**
  - Language configuration (code, name, direction, locale, status).

- **app/DTOs/System/ConfigurationData.php**
  - System configuration (key, value, type, group, description).

- **app/DTOs/System/ActivityLogData.php**
  - Activity log entry (user, type, description, subject, properties).

- **app/DTOs/System/SystemMetricData.php**
  - System metric (type, period, value, recorded_at, metadata).

## 📁 General DTOs

- **app/DTOs/ApiResponse.php**
  - Standard API response structure (data, status, message, errors).

---

> Each DTO should be a readonly PHP class with strict typing, used for transferring validated data between controllers, services, and other layers. Follow the structure and naming conventions as described in the project documentation.

### Package Integration Updates

Based on the installed packages from composer.json, the following DTOs need to be added to support Laravel Breeze, Spatie packages, Stripe, Binance, and other integrations:

## 📁 Authentication DTOs (Laravel Breeze + Sanctum)

- **app/DTOs/Auth/LoginData.php** - Login credentials and remember me token
- **app/DTOs/Auth/RegisterData.php** - Registration form data with email verification
- **app/DTOs/Auth/PasswordResetData.php** - Password reset token and new password
- **app/DTOs/Auth/TwoFactorData.php** - 2FA setup and verification codes
- **app/DTOs/Auth/SanctumTokenData.php** - API token creation and management
- **app/DTOs/Auth/SocialiteData.php** - Social login provider data

## 📁 Payment Integration DTOs

- **app/DTOs/Payment/StripeData.php** - Stripe payment intent and webhook data
- **app/DTOs/Payment/BinanceData.php** - Binance API request and response data
- **app/DTOs/Payment/CurrencyExchangeData.php** - Exchange rate and conversion data (florianv/exchanger)
- **app/DTOs/Payment/MoneyData.php** - Money object with currency (moneyphp/money)
- **app/DTOs/Payment/WebhookData.php** - Payment webhook validation and processing

## 📁 Media & File DTOs (Spatie MediaLibrary)

- **app/DTOs/Media/MediaData.php** - Media file metadata and conversions
- **app/DTOs/Media/ImageOptimizationData.php** - Image processing and optimization settings
- **app/DTOs/Media/FileUploadData.php** - File upload validation and storage data

## 📁 Permission & Activity DTOs (Spatie Packages)

- **app/DTOs/Permission/RoleData.php** - Role creation and permission assignment
- **app/DTOs/Permission/PermissionData.php** - Permission creation and management
- **app/DTOs/Activity/ActivityLogData.php** - Enhanced activity logging with properties
- **app/DTOs/Activity/AuditData.php** - Audit trail data (owen-it/laravel-auditing)

## 📁 Search & Query DTOs

- **app/DTOs/Search/SearchData.php** - Search query and filter parameters
- **app/DTOs/Search/QueryBuilderData.php** - Spatie Query Builder filters and sorts
- **app/DTOs/Search/ScoutData.php** - Laravel Scout search configuration

## 📁 Export & Import DTOs

- **app/DTOs/Export/ExcelData.php** - Excel export configuration (maatwebsite/excel)
- **app/DTOs/Export/PdfData.php** - PDF generation settings (dompdf/mpdf)
- **app/DTOs/Export/BackupData.php** - Backup configuration (spatie/laravel-backup)

## 📁 Localization DTOs

- **app/DTOs/Localization/LocaleData.php** - Locale configuration and switching
- **app/DTOs/Localization/TranslationData.php** - Translation management and fallbacks

