# DATABASE_SCHEMA_V2.md

This schema uses `tinyint` for enum-like fields and `varchar` for flexible string fields, with detailed comments and descriptions for each table and field. Use PHP Enums in your application to map integer values to their meanings.

---

## 👤 User System Tables

### users
| Column | Type | Constraints & Indexes | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID for performance |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| name | VARCHAR(255) | NOT NULL | User name |
| email | VARCHAR(255) | UNIQUE, NOT NULL | User email address |
| email_verified_at | TIMESTAMP | NULLABLE | Email verification timestamp |
| password | VARCHAR(255) | NOT NULL | Hashed password |
| first_name | VARCHAR(100) | NOT NULL | User first name |
| last_name | VARCHAR(100) | NOT NULL | User last name |
| username | VARCHAR(50) | UNIQUE, NULLABLE | Optional username |
| avatar | VARCHAR(255) | NULLABLE | Profile picture path |
| status | TINYINT UNSIGNED | INDEX, DEFAULT 1 | User status (use PHP Enum) |
| level | TINYINT UNSIGNED | INDEX, DEFAULT 1 | User progression level (1-14) |
| points | BIGINT UNSIGNED | INDEX, DEFAULT 0 | Accumulated points |
| preferred_language | VARCHAR(5) | INDEX, DEFAULT 'en' | Language preference |
| text_direction | TINYINT UNSIGNED | DEFAULT 0 | 0=ltr, 1=rtl (use PHP Enum) |
| timezone | VARCHAR(50) | DEFAULT 'UTC' | User timezone |
| referral_code | VARCHAR(20) | UNIQUE, NULLABLE | User's referral code |
| referred_by_user_id | BIGINT UNSIGNED | INDEX, NULLABLE, FK | Referrer user ID |
| last_active_at | TIMESTAMP | INDEX, NULLABLE | Last activity timestamp |
| preferences | JSON | NULLABLE | User preferences object |
| remember_token | VARCHAR(100) | NULLABLE | Remember me token |
| created_at | TIMESTAMP | NOT NULL, INDEX | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### user_profiles
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK, CASCADE DELETE | Associated user |
| bio | TEXT | NULLABLE | User biography |
| website_url | VARCHAR(255) | NULLABLE | Personal website |
| linkedin_url | VARCHAR(255) | NULLABLE | LinkedIn profile |
| twitter_handle | VARCHAR(50) | NULLABLE | Twitter username |
| github_username | VARCHAR(100) | NULLABLE | GitHub username |
| country | VARCHAR(100) | NULLABLE | Country of residence |
| city | VARCHAR(100) | NULLABLE | City of residence |
| birth_date | DATE | NULLABLE | Date of birth |
| gender | TINYINT UNSIGNED | DEFAULT 0 | Gender (use PHP Enum) |
| profession | VARCHAR(100) | NULLABLE | Current profession |
| interests | TEXT | NULLABLE | Areas of interest |
| skills | JSON | NULLABLE | Skills array |
| is_public | BOOLEAN | DEFAULT TRUE | Profile visibility |
| show_email | BOOLEAN | DEFAULT FALSE | Email visibility |
| show_progress | BOOLEAN | DEFAULT TRUE | Progress visibility |
| notification_settings | JSON | NULLABLE | Notification preferences |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### user_levels
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| level | TINYINT UNSIGNED | UNIQUE, NOT NULL | User level number (1-14) |
| name | VARCHAR(50) | NOT NULL | Level name |
| min_points | BIGINT UNSIGNED | NOT NULL | Minimum points required |
| max_points | BIGINT UNSIGNED | NOT NULL | Maximum points for level |
| benefits | JSON | NOT NULL | JSON array of level benefits |
| badge_image | VARCHAR(255) | NULLABLE | Level badge image path |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### user_progress
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK, CASCADE DELETE | User reference |
| episode_id | BIGINT UNSIGNED | FK, CASCADE DELETE | Episode reference |
| completion_percentage | TINYINT UNSIGNED | DEFAULT 0 | Percentage completed (0-100) |
| time_spent_minutes | INTEGER UNSIGNED | DEFAULT 0 | Total time spent |
| last_position | INTEGER UNSIGNED | DEFAULT 0 | Last reading position |
| completed_at | TIMESTAMP | NULLABLE | Completion timestamp |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### user_notes
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK, CASCADE DELETE | User reference |
| episode_id | BIGINT UNSIGNED | FK, CASCADE DELETE | Episode reference |
| content | TEXT | NOT NULL | Note content |
| position | INTEGER UNSIGNED | DEFAULT 0 | Position in episode |
| is_public | BOOLEAN | DEFAULT FALSE | Public visibility |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### user_reactions
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK, CASCADE DELETE | User reference |
| episode_id | BIGINT UNSIGNED | FK, CASCADE DELETE | Episode reference |
| type | TINYINT UNSIGNED | NOT NULL | Reaction type (use PHP Enum) |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

---

## 📚 Course System Tables

### categories
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| name | VARCHAR(100) | NOT NULL | Category name |
| slug | VARCHAR(100) | UNIQUE, NOT NULL | URL-friendly identifier |
| description | TEXT | NULLABLE | Category description |
| parent_id | BIGINT UNSIGNED | FK, NULLABLE | Parent category |
| sort_order | INTEGER UNSIGNED | DEFAULT 0 | Display order |
| is_active | BOOLEAN | DEFAULT TRUE | Active status |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### docu_courses
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| title | VARCHAR(255) | NOT NULL | Course title |
| alias | VARCHAR(255) | UNIQUE, NOT NULL | URL-friendly identifier |
| description | TEXT | NOT NULL | Full course description |
| short_description | TEXT | NULLABLE | Brief course summary |
| cover_image | VARCHAR(255) | NULLABLE | Course cover image path |
| thumbnail | VARCHAR(255) | NULLABLE | Course thumbnail path |
| category_id | BIGINT UNSIGNED | FK, RESTRICT | Course category |
| author_id | BIGINT UNSIGNED | FK, RESTRICT | Course author |
| language_id | BIGINT UNSIGNED | FK, RESTRICT | Primary language |
| status | TINYINT UNSIGNED | DEFAULT 0 | Course status (use PHP Enum) |
| access_level | TINYINT UNSIGNED | DEFAULT 0 | Access requirement (use PHP Enum) |
| required_user_level | TINYINT UNSIGNED | NULLABLE | Minimum user level |
| price | DECIMAL(10,2) | DEFAULT 0.00 | Course price |
| currency_id | BIGINT UNSIGNED | FK, SET NULL | Price currency |
| is_free | BOOLEAN | DEFAULT TRUE | Free access flag |
| estimated_duration_minutes | INTEGER UNSIGNED| NULLABLE | Estimated completion time |
| difficulty_level | TINYINT UNSIGNED | DEFAULT 0 | Course difficulty (use PHP Enum) |
| tags | JSON | NULLABLE | Course tags array |
| seo_meta | JSON | NULLABLE | SEO metadata |
| views_count | INTEGER UNSIGNED | DEFAULT 0 | Total view count |
| enrollments_count | INTEGER UNSIGNED | DEFAULT 0 | Total enrollments |
| average_rating | DECIMAL(3,2) | DEFAULT 0.00 | Average user rating |
| ratings_count | INTEGER UNSIGNED | DEFAULT 0 | Total rating count |
| published_at | TIMESTAMP | NULLABLE | Publication timestamp |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### episodes
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| docu_course_id | BIGINT UNSIGNED | FK, CASCADE DELETE | Course reference |
| title | VARCHAR(255) | NOT NULL | Episode title |
| alias | VARCHAR(255) | NOT NULL | URL-friendly identifier |
| description | TEXT | NULLABLE | Episode description |
| sort_order | INTEGER UNSIGNED | NOT NULL | Display order in course |
| file_path | VARCHAR(255) | NOT NULL | Content file path |
| estimated_reading_time_minutes | INTEGER UNSIGNED | NULLABLE | Estimated reading time |
| access_level | TINYINT UNSIGNED | DEFAULT 0 | Access requirement (use PHP Enum) |
| required_user_level | TINYINT UNSIGNED | NULLABLE | Minimum user level |
| status | TINYINT UNSIGNED | DEFAULT 0 | Publication status (use PHP Enum) |
| published_at | TIMESTAMP | NULLABLE | Publication timestamp |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### episode_parts
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| episode_id | BIGINT UNSIGNED | FK, CASCADE DELETE | Episode reference |
| part_number | INTEGER UNSIGNED | NOT NULL | Part sequence number |
| title | VARCHAR(255) | NULLABLE | Part title |
| file_path | VARCHAR(255) | NOT NULL | Content file path |
| access_level | TINYINT UNSIGNED | DEFAULT 0 | Access requirement (use PHP Enum) |
| required_user_level | TINYINT UNSIGNED | NULLABLE | Minimum user level |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### course_uploads
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK, CASCADE DELETE | Uploader reference |
| docu_course_id | BIGINT UNSIGNED | FK, NULLABLE | Course reference |
| original_filename | VARCHAR(255) | NOT NULL | Original file name |
| storage_path | VARCHAR(255) | NOT NULL | Storage path |
| file_size | BIGINT UNSIGNED | NOT NULL | File size in bytes |
| mime_type | VARCHAR(100) | NOT NULL | File MIME type |
| status | TINYINT UNSIGNED | DEFAULT 0 | Processing status (use PHP Enum) |
| metadata | JSON | NULLABLE | File metadata |
| processed_at | TIMESTAMP | NULLABLE | Processing completion time |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

---

## 💰 Payment System Tables

### currencies
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| code | VARCHAR(3) | UNIQUE, NOT NULL | Currency code (USD, EUR, etc.) |
| name | VARCHAR(100) | NOT NULL | Currency name |
| symbol | VARCHAR(10) | NOT NULL | Currency symbol |
| type | TINYINT UNSIGNED | DEFAULT 0 | Currency type (use PHP Enum) |
| exchange_rate | DECIMAL(12,6) | DEFAULT 1.000000 | Exchange rate to base currency |
| is_active | BOOLEAN | DEFAULT TRUE | Active status |
| decimals | TINYINT UNSIGNED | DEFAULT 2 | Decimal places |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### transactions
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK, CASCADE DELETE | User reference |
| type | TINYINT UNSIGNED | NOT NULL | Transaction type (use PHP Enum) |
| status | TINYINT UNSIGNED | NOT NULL | Transaction status (use PHP Enum) |
| amount | DECIMAL(12,4) | NOT NULL | Transaction amount |
| currency_id | BIGINT UNSIGNED | FK, RESTRICT | Currency reference |
| provider | TINYINT UNSIGNED | NOT NULL | Payment provider (use PHP Enum) |
| provider_transaction_id | VARCHAR(255) | NULLABLE | Provider transaction ID |
| description | TEXT | NULLABLE | Transaction description |
| metadata | JSON | NULLABLE | Additional transaction data |
| completed_at | TIMESTAMP | NULLABLE | Completion timestamp |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### payment_methods
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK, CASCADE DELETE | User reference |
| type | TINYINT UNSIGNED | NOT NULL | Payment method type (use PHP Enum) |
| provider | TINYINT UNSIGNED | NOT NULL | Payment provider (use PHP Enum) |
| is_default | BOOLEAN | DEFAULT FALSE | Default payment method |
| metadata | JSON | NOT NULL | Payment method details |
| is_verified | BOOLEAN | DEFAULT FALSE | Verification status |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### invoices
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK, CASCADE DELETE | User reference |
| transaction_id | BIGINT UNSIGNED | FK, NULLABLE | Transaction reference |
| amount | DECIMAL(12,4) | NOT NULL | Invoice amount |
| currency_id | BIGINT UNSIGNED | FK, RESTRICT | Currency reference |
| status | TINYINT UNSIGNED | NOT NULL | Invoice status (use PHP Enum) |
| due_date | DATE | NULLABLE | Payment due date |
| items | JSON | NOT NULL | Invoice line items |
| tax_amount | DECIMAL(12,4) | DEFAULT 0.0000 | Tax amount |
| total_amount | DECIMAL(12,4) | NOT NULL | Total amount |
| paid_at | TIMESTAMP | NULLABLE | Payment timestamp |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

---

## 🔗 Affiliate System Tables

### affiliates
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK, CASCADE DELETE | User reference |
| status | TINYINT UNSIGNED | DEFAULT 0 | Affiliate status (use PHP Enum) |
| commission_rate | DECIMAL(5,2) | DEFAULT 0.00 | Base commission rate |
| total_commissions | DECIMAL(12,4) | DEFAULT 0.0000 | Total commissions earned |
| paid_commissions | DECIMAL(12,4) | DEFAULT 0.0000 | Commissions paid out |
| balance | DECIMAL(12,4) | DEFAULT 0.0000 | Current balance |
| approved_at | TIMESTAMP | NULLABLE | Approval timestamp |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### referral_codes
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| affiliate_id | BIGINT UNSIGNED | FK, CASCADE DELETE | Affiliate reference |
| code | VARCHAR(20) | UNIQUE, NOT NULL | Referral code |
| type | TINYINT UNSIGNED | NOT NULL | Referral type (use PHP Enum) |
| is_active | BOOLEAN | DEFAULT TRUE | Active status |
| expiration_date | DATE | NULLABLE | Code expiration |
| max_uses | INTEGER UNSIGNED | NULLABLE | Maximum usage limit |
| use_count | INTEGER UNSIGNED | DEFAULT 0 | Current usage count |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### affiliate_clicks
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| affiliate_id | BIGINT UNSIGNED | FK, CASCADE DELETE | Affiliate reference |
| referral_code_id | BIGINT UNSIGNED | FK, CASCADE DELETE | Referral code reference |
| ip_address | VARCHAR(45) | NOT NULL | Visitor IP address |
| user_agent | TEXT | NULLABLE | User agent string |
| referrer | VARCHAR(255) | NULLABLE | HTTP referrer |
| landing_page | VARCHAR(255) | NOT NULL | Initial landing page |
| session_id | VARCHAR(255) | NOT NULL | Visitor session ID |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### conversions
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| affiliate_click_id | BIGINT UNSIGNED | FK, CASCADE DELETE | Click reference |
| type | TINYINT UNSIGNED | NOT NULL | Conversion type (use PHP Enum) |
| user_id | BIGINT UNSIGNED | FK, CASCADE DELETE | Converted user |
| amount | DECIMAL(12,4) | NULLABLE | Conversion amount |
| currency_id | BIGINT UNSIGNED | FK, RESTRICT | Currency reference |
| status | TINYINT UNSIGNED | NOT NULL | Conversion status (use PHP Enum) |
| converted_at | TIMESTAMP | NOT NULL | Conversion timestamp |
| approved_at | TIMESTAMP | NULLABLE | Approval timestamp |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### commissions
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| affiliate_id | BIGINT UNSIGNED | FK, CASCADE DELETE | Affiliate reference |
| conversion_id | BIGINT UNSIGNED | FK, CASCADE DELETE | Conversion reference |
| amount | DECIMAL(12,4) | NOT NULL | Commission amount |
| currency_id | BIGINT UNSIGNED | FK, RESTRICT | Currency reference |
| rate | DECIMAL(5,2) | NOT NULL | Commission rate applied |
| status | TINYINT UNSIGNED | NOT NULL | Commission status (use PHP Enum) |
| payable_at | TIMESTAMP | NULLABLE | When commission becomes payable |
| paid_at | TIMESTAMP | NULLABLE | Payment timestamp |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### social_shares
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK, CASCADE DELETE | User reference |
| platform | TINYINT UNSIGNED | NOT NULL | Sharing platform (use PHP Enum) |
| content_type | TINYINT UNSIGNED | NOT NULL | Shared content type (use PHP Enum) |
| content_id | BIGINT UNSIGNED | NOT NULL | Shared content ID |
| share_url | VARCHAR(255) | NOT NULL | Shared URL |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

---

## 🌐 System Configuration Tables

### languages
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| code | VARCHAR(5) | UNIQUE, NOT NULL | Language code (en, ar, es, etc.) |
| name | VARCHAR(100) | NOT NULL | Language name |
| native_name | VARCHAR(100) | NOT NULL | Native language name |
| direction | TINYINT UNSIGNED | DEFAULT 0 | Text direction (use PHP Enum) |
| locale | VARCHAR(10) | NOT NULL | Locale code |
| is_active | BOOLEAN | DEFAULT TRUE | Active status |
| sort_order | INTEGER UNSIGNED | DEFAULT 0 | Display order |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### configurations
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| key | VARCHAR(100) | UNIQUE, NOT NULL | Configuration key |
| value | TEXT | NOT NULL | Configuration value |
| type | TINYINT UNSIGNED | NOT NULL | Value type (use PHP Enum) |
| group | VARCHAR(50) | NOT NULL | Configuration group |
| is_public | BOOLEAN | DEFAULT FALSE | Publicly accessible |
| description | TEXT | NULLABLE | Configuration description |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### activity_logs
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK, NULLABLE | User reference |
| type | VARCHAR(50) | NOT NULL | Activity type |
| description | TEXT | NOT NULL | Activity description |
| subject_type | VARCHAR(100) | NULLABLE | Subject model type |
| subject_id | BIGINT UNSIGNED | NULLABLE | Subject model ID |
| properties | JSON | NULLABLE | Additional properties |
| ip_address | VARCHAR(45) | NULLABLE | IP address |
| user_agent | TEXT | NULLABLE | User agent string |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### system_metrics
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| metric_type | VARCHAR(50) | NOT NULL | Metric type |
| period | TINYINT UNSIGNED | NOT NULL | Time period (use PHP Enum) |
| value | DECIMAL(15,4) | NOT NULL | Metric value |
| recorded_at | TIMESTAMP | NOT NULL | Recording timestamp |
| metadata | JSON | NULLABLE | Additional metric data |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

---

## 📊 Analytics & Tracking Tables

### user_sessions
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK, CASCADE DELETE | User reference |
| session_id | VARCHAR(255) | NOT NULL | Session identifier |
| ip_address | VARCHAR(45) | NULLABLE | IP address |
| user_agent | TEXT | NULLABLE | User agent string |
| device_type | TINYINT UNSIGNED | DEFAULT 0 | Device type (use PHP Enum) |
| browser | VARCHAR(100) | NULLABLE | Browser name |
| platform | VARCHAR(100) | NULLABLE | Operating system |
| location | VARCHAR(100) | NULLABLE | Geographic location |
| last_activity | TIMESTAMP | NOT NULL | Last activity timestamp |
| expires_at | TIMESTAMP | NULLABLE | Session expiration |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### content_views
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK, NULLABLE | User reference |
| content_type | TINYINT UNSIGNED | NOT NULL | Content type (use PHP Enum) |
| content_id | BIGINT UNSIGNED | NOT NULL | Content ID |
| session_id | VARCHAR(255) | NOT NULL | Session identifier |
| view_duration_seconds | INTEGER UNSIGNED | DEFAULT 0 | View duration in seconds |
| ip_address | VARCHAR(45) | NULLABLE | IP address |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### engagement_metrics
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK, CASCADE DELETE | User reference |
| metric_type | VARCHAR(50) | NOT NULL | Metric type |
| value | DECIMAL(10,4) | NOT NULL | Metric value |
| period | DATE | NOT NULL | Metric period |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### conversion_tracking
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK, CASCADE DELETE | User reference |
| conversion_type | VARCHAR(50) | NOT NULL | Conversion type |
| source | VARCHAR(100) | NULLABLE | Traffic source |
| medium | VARCHAR(100) | NULLABLE | Traffic medium |
| campaign | VARCHAR(100) | NULLABLE | Campaign name |
| content | VARCHAR(100) | NULLABLE | Content identifier |
| term | VARCHAR(100) | NULLABLE | Search term |
| converted_at | TIMESTAMP | NOT NULL | Conversion timestamp |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |
 Package Integration Updates
---

## 🔧 Package Integration Tables

> **Update:** Additional tables from executed migrations including Laravel Breeze, Spatie packages, Sanctum, and package-specific features.

### cache (Laravel)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| key | VARCHAR(255) | PRIMARY KEY | Cache key identifier |
| value | LONGTEXT | NOT NULL | Cached value |
| expiration | INT | NOT NULL | Unix timestamp expiration |

### jobs (Laravel Queue)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Job ID |
| queue | VARCHAR(255) | NOT NULL | Queue name |
| payload | LONGTEXT | NOT NULL | Job data |
| attempts | TINYINT UNSIGNED | NOT NULL | Attempt count |
| reserved_at | INT UNSIGNED | NULLABLE | Reserved timestamp |
| available_at | INT UNSIGNED | NOT NULL | Available timestamp |
| created_at | INT UNSIGNED | NOT NULL | Creation timestamp |

### personal_access_tokens (Laravel Sanctum)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Token ID |
| tokenable_type | VARCHAR(255) | NOT NULL | Model type |
| tokenable_id | BIGINT UNSIGNED | NOT NULL | Model ID |
| name | VARCHAR(255) | NOT NULL | Token name |
| token | VARCHAR(64) | UNIQUE, NOT NULL | Token hash |
| abilities | TEXT | NULLABLE | Token abilities |
| last_used_at | TIMESTAMP | NULLABLE | Last usage timestamp |
| expires_at | TIMESTAMP | NULLABLE | Expiration timestamp |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### permissions (Spatie Permission)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Permission ID |
| name | VARCHAR(255) | NOT NULL | Permission name |
| guard_name | VARCHAR(255) | NOT NULL | Guard name |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### roles (Spatie Permission)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Role ID |
| name | VARCHAR(255) | NOT NULL | Role name |
| guard_name | VARCHAR(255) | NOT NULL | Guard name |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### model_has_permissions (Spatie Permission)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| permission_id | BIGINT UNSIGNED | FK | Permission reference |
| model_type | VARCHAR(255) | NOT NULL | Model type |
| model_id | BIGINT UNSIGNED | NOT NULL | Model ID |

### model_has_roles (Spatie Permission)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| role_id | BIGINT UNSIGNED | FK | Role reference |
| model_type | VARCHAR(255) | NOT NULL | Model type |
| model_id | BIGINT UNSIGNED | NOT NULL | Model ID |

### role_has_permissions (Spatie Permission)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| permission_id | BIGINT UNSIGNED | FK | Permission reference |
| role_id | BIGINT UNSIGNED | FK | Role reference |

### media (Spatie MediaLibrary)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Media ID |
| model_type | VARCHAR(255) | NOT NULL | Model type |
| model_id | BIGINT UNSIGNED | NOT NULL | Model ID |
| uuid | CHAR(36) | UNIQUE | Media UUID |
| collection_name | VARCHAR(255) | NOT NULL | Collection name |
| name | VARCHAR(255) | NOT NULL | File name |
| file_name | VARCHAR(255) | NOT NULL | Original file name |
| mime_type | VARCHAR(255) | NULLABLE | MIME type |
| disk | VARCHAR(255) | NOT NULL | Storage disk |
| conversions_disk | VARCHAR(255) | NULLABLE | Conversions disk |
| size | BIGINT UNSIGNED | NOT NULL | File size |
| manipulations | JSON | NOT NULL | Image manipulations |
| custom_properties | JSON | NOT NULL | Custom properties |
| generated_conversions | JSON | NOT NULL | Generated conversions |
| responsive_images | JSON | NOT NULL | Responsive images |
| order_column | INTEGER UNSIGNED | NULLABLE | Order column |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### telescope_entries (Laravel Telescope)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| sequence | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Sequence ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | Entry UUID |
| batch_id | CHAR(36) | NOT NULL | Batch ID |
| family_hash | VARCHAR(255) | NULLABLE | Family hash |
| should_display_on_index | BOOLEAN | DEFAULT TRUE | Index display flag |
| type | VARCHAR(20) | NOT NULL | Entry type |
| content | LONGTEXT | NOT NULL | Entry content |
| created_at | DATETIME | NULLABLE | Creation timestamp |

### telescope_entries_tags (Laravel Telescope)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| entry_uuid | CHAR(36) | FK | Entry UUID |
| tag | VARCHAR(255) | NOT NULL | Tag value |

### telescope_monitoring (Laravel Telescope)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| tag | VARCHAR(255) | PRIMARY KEY | Monitoring tag |

---

> **Note:** These additional tables support the installed packages and their functionalities. The main schema remains unchanged, with these being supplementary package-specific tables.