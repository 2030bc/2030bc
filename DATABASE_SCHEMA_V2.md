# DATABASE_SCHEMA_V2.md

This schema uses `tinyint` for enum-like fields and `varchar` for flexible string fields, with detailed comments and descriptions for each table and field. Use PHP Enums in your application to map integer values to their meanings.

## Package Integration Status

**Executed Package Migrations:**
- ✅ Laravel Breeze users table (0001_01_01_000000_create_users_table.php)
- ✅ Laravel cache table (0001_01_01_000001_create_cache_table.php)
- ✅ Laravel jobs table (0001_01_01_000002_create_jobs_table.php)
- ✅ Spatie Media Library table (2025_08_29_211228_create_media_table.php)
- ✅ Spatie Permission tables (2025_08_29_211330_create_permission_tables.php)
- ✅ Laravel Sanctum personal access tokens (2025_08_29_211413_create_personal_access_tokens_table.php)
- ✅ Laravel Telescope tables (2025_08_29_211552_create_telescope_entries_table.php)
- ✅ Spatie Activity Log table (2025_08_29_211842_create_activity_log_table.php)

**Custom Tables to Create:** All tables below (except package tables) need to be created as migrations.

---

## 👤 User System Tables

### users (✅ INSTALLED - Laravel Breeze)
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
| referred_by_user_id | BIGINT UNSIGNED | INDEX, NULLABLE, FK → users(id) | Referrer user ID |
| last_active_at | TIMESTAMP | INDEX, NULLABLE | Last activity timestamp |
| preferences | JSON | NULLABLE | User preferences object |
| remember_token | VARCHAR(100) | NULLABLE | Remember me token |
| created_at | TIMESTAMP | NOT NULL, INDEX | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

**Package Integration:** This table extends the Laravel Breeze users table with additional fields. The base structure (id, name, email, password, remember_token, timestamps) is already installed.

### user_profiles (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK → users(id), CASCADE DELETE | Associated user |
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

### user_levels (➕ TO CREATE)
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

### user_progress (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK → users(id), CASCADE DELETE | User reference |
| episode_id | BIGINT UNSIGNED | FK → episodes(id), CASCADE DELETE | Episode reference |
| completion_percentage | TINYINT UNSIGNED | DEFAULT 0 | Percentage completed (0-100) |
| time_spent_minutes | INTEGER UNSIGNED | DEFAULT 0 | Total time spent |
| last_position | INTEGER UNSIGNED | DEFAULT 0 | Last reading position |
| completed_at | TIMESTAMP | NULLABLE | Completion timestamp |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### user_notes (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK → users(id), CASCADE DELETE | User reference |
| episode_id | BIGINT UNSIGNED | FK → episodes(id), CASCADE DELETE | Episode reference |
| content | TEXT | NOT NULL | Note content |
| position | INTEGER UNSIGNED | DEFAULT 0 | Position in episode |
| is_public | BOOLEAN | DEFAULT FALSE | Public visibility |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### user_reactions (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK → users(id), CASCADE DELETE | User reference |
| episode_id | BIGINT UNSIGNED | FK → episodes(id), CASCADE DELETE | Episode reference |
| type | TINYINT UNSIGNED | NOT NULL | Reaction type (use PHP Enum) |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

---

## 📚 Course System Tables

### categories (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| name | VARCHAR(100) | NOT NULL | Category name |
| slug | VARCHAR(100) | UNIQUE, NOT NULL | URL-friendly identifier |
| description | TEXT | NULLABLE | Category description |
| parent_id | BIGINT UNSIGNED | FK → categories(id), NULLABLE | Parent category |
| sort_order | INTEGER UNSIGNED | DEFAULT 0 | Display order |
| is_active | BOOLEAN | DEFAULT TRUE | Active status |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### docu_courses (➕ TO CREATE)
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
| category_id | BIGINT UNSIGNED | FK → categories(id), RESTRICT | Course category |
| author_id | BIGINT UNSIGNED | FK → users(id), RESTRICT | Course author |
| language_id | BIGINT UNSIGNED | FK → languages(id), RESTRICT | Primary language |
| status | TINYINT UNSIGNED | DEFAULT 0 | Course status (use PHP Enum) |
| access_level | TINYINT UNSIGNED | DEFAULT 0 | Access requirement (use PHP Enum) |
| required_user_level | TINYINT UNSIGNED | NULLABLE | Minimum user level |
| price | DECIMAL(10,2) | DEFAULT 0.00 | Course price |
| currency_id | BIGINT UNSIGNED | FK → currencies(id), SET NULL | Price currency |
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

### episodes (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| docu_course_id | BIGINT UNSIGNED | FK → docu_courses(id), CASCADE DELETE | Course reference |
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

### episode_parts (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| episode_id | BIGINT UNSIGNED | FK → episodes(id), CASCADE DELETE | Episode reference |
| part_number | INTEGER UNSIGNED | NOT NULL | Part sequence number |
| title | VARCHAR(255) | NULLABLE | Part title |
| file_path | VARCHAR(255) | NOT NULL | Content file path |
| access_level | TINYINT UNSIGNED | DEFAULT 0 | Access requirement (use PHP Enum) |
| required_user_level | TINYINT UNSIGNED | NULLABLE | Minimum user level |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### course_uploads (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK → users(id), CASCADE DELETE | Uploader reference |
| docu_course_id | BIGINT UNSIGNED | FK → docu_courses(id), NULLABLE | Course reference |
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

### currencies (➕ TO CREATE)
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

### transactions (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK → users(id), CASCADE DELETE | User reference |
| type | TINYINT UNSIGNED | NOT NULL | Transaction type (use PHP Enum) |
| status | TINYINT UNSIGNED | NOT NULL | Transaction status (use PHP Enum) |
| amount | DECIMAL(12,4) | NOT NULL | Transaction amount |
| currency_id | BIGINT UNSIGNED | FK → currencies(id), RESTRICT | Currency reference |
| provider | TINYINT UNSIGNED | NOT NULL | Payment provider (use PHP Enum) |
| provider_transaction_id | VARCHAR(255) | NULLABLE | Provider transaction ID |
| description | TEXT | NULLABLE | Transaction description |
| metadata | JSON | NULLABLE | Additional transaction data |
| completed_at | TIMESTAMP | NULLABLE | Completion timestamp |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### payment_methods (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK → users(id), CASCADE DELETE | User reference |
| type | TINYINT UNSIGNED | NOT NULL | Payment method type (use PHP Enum) |
| provider | TINYINT UNSIGNED | NOT NULL | Payment provider (use PHP Enum) |
| is_default | BOOLEAN | DEFAULT FALSE | Default payment method |
| metadata | JSON | NOT NULL | Payment method details |
| is_verified | BOOLEAN | DEFAULT FALSE | Verification status |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### invoices (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK → users(id), CASCADE DELETE | User reference |
| transaction_id | BIGINT UNSIGNED | FK → transactions(id), NULLABLE | Transaction reference |
| amount | DECIMAL(12,4) | NOT NULL | Invoice amount |
| currency_id | BIGINT UNSIGNED | FK → currencies(id), RESTRICT | Currency reference |
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

### affiliates (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK → users(id), CASCADE DELETE | User reference |
| status | TINYINT UNSIGNED | DEFAULT 0 | Affiliate status (use PHP Enum) |
| commission_rate | DECIMAL(5,2) | DEFAULT 0.00 | Base commission rate |
| total_commissions | DECIMAL(12,4) | DEFAULT 0.0000 | Total commissions earned |
| paid_commissions | DECIMAL(12,4) | DEFAULT 0.0000 | Commissions paid out |
| balance | DECIMAL(12,4) | DEFAULT 0.0000 | Current balance |
| approved_at | TIMESTAMP | NULLABLE | Approval timestamp |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### referral_codes (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| affiliate_id | BIGINT UNSIGNED | FK → affiliates(id), CASCADE DELETE | Affiliate reference |
| code | VARCHAR(20) | UNIQUE, NOT NULL | Referral code |
| type | TINYINT UNSIGNED | NOT NULL | Referral type (use PHP Enum) |
| is_active | BOOLEAN | DEFAULT TRUE | Active status |
| expiration_date | DATE | NULLABLE | Code expiration |
| max_uses | INTEGER UNSIGNED | NULLABLE | Maximum usage limit |
| use_count | INTEGER UNSIGNED | DEFAULT 0 | Current usage count |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### affiliate_clicks (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| affiliate_id | BIGINT UNSIGNED | FK → affiliates(id), CASCADE DELETE | Affiliate reference |
| referral_code_id | BIGINT UNSIGNED | FK → referral_codes(id), CASCADE DELETE | Referral code reference |
| ip_address | VARCHAR(45) | NOT NULL | Visitor IP address |
| user_agent | TEXT | NULLABLE | User agent string |
| referrer | VARCHAR(255) | NULLABLE | HTTP referrer |
| landing_page | VARCHAR(255) | NOT NULL | Initial landing page |
| session_id | VARCHAR(255) | NOT NULL | Visitor session ID |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### conversions (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| affiliate_click_id | BIGINT UNSIGNED | FK → affiliate_clicks(id), CASCADE DELETE | Click reference |
| type | TINYINT UNSIGNED | NOT NULL | Conversion type (use PHP Enum) |
| user_id | BIGINT UNSIGNED | FK → users(id), CASCADE DELETE | Converted user |
| amount | DECIMAL(12,4) | NULLABLE | Conversion amount |
| currency_id | BIGINT UNSIGNED | FK → currencies(id), RESTRICT | Currency reference |
| status | TINYINT UNSIGNED | NOT NULL | Conversion status (use PHP Enum) |
| converted_at | TIMESTAMP | NOT NULL | Conversion timestamp |
| approved_at | TIMESTAMP | NULLABLE | Approval timestamp |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### commissions (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| affiliate_id | BIGINT UNSIGNED | FK → affiliates(id), CASCADE DELETE | Affiliate reference |
| conversion_id | BIGINT UNSIGNED | FK → conversions(id), CASCADE DELETE | Conversion reference |
| amount | DECIMAL(12,4) | NOT NULL | Commission amount |
| currency_id | BIGINT UNSIGNED | FK → currencies(id), RESTRICT | Currency reference |
| rate | DECIMAL(5,2) | NOT NULL | Commission rate applied |
| status | TINYINT UNSIGNED | NOT NULL | Commission status (use PHP Enum) |
| payable_at | TIMESTAMP | NULLABLE | When commission becomes payable |
| paid_at | TIMESTAMP | NULLABLE | Payment timestamp |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### social_shares (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK → users(id), CASCADE DELETE | User reference |
| platform | TINYINT UNSIGNED | NOT NULL | Sharing platform (use PHP Enum) |
| content_type | TINYINT UNSIGNED | NOT NULL | Shared content type (use PHP Enum) |
| content_id | BIGINT UNSIGNED | NOT NULL | Shared content ID |
| share_url | VARCHAR(255) | NOT NULL | Shared URL |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

---

## 🌐 System Configuration Tables

### languages (➕ TO CREATE)
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

### configurations (➕ TO CREATE)
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

### activity_logs (✅ INSTALLED - Spatie Activity Log)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK → users(id), NULLABLE | User reference |
| type | VARCHAR(50) | NOT NULL | Activity type |
| description | TEXT | NOT NULL | Activity description |
| subject_type | VARCHAR(100) | NULLABLE | Subject model type |
| subject_id | BIGINT UNSIGNED | NULLABLE | Subject model ID |
| properties | JSON | NULLABLE | Additional properties |
| ip_address | VARCHAR(45) | NULLABLE | IP address |
| user_agent | TEXT | NULLABLE | User agent string |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

**Package Integration:** This table is installed via Spatie Activity Log package migration (2025_08_29_211842_create_activity_log_table.php).

### system_metrics (➕ TO CREATE)
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

### user_sessions (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK → users(id), CASCADE DELETE | User reference |
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

### content_views (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK → users(id), NULLABLE | User reference |
| content_type | TINYINT UNSIGNED | NOT NULL | Content type (use PHP Enum) |
| content_id | BIGINT UNSIGNED | NOT NULL | Content ID |
| session_id | VARCHAR(255) | NOT NULL | Session identifier |
| view_duration_seconds | INTEGER UNSIGNED | DEFAULT 0 | View duration in seconds |
| ip_address | VARCHAR(45) | NULLABLE | IP address |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### engagement_metrics (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK → users(id), CASCADE DELETE | User reference |
| metric_type | VARCHAR(50) | NOT NULL | Metric type |
| value | DECIMAL(10,4) | NOT NULL | Metric value |
| period | DATE | NOT NULL | Metric period |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

### conversion_tracking (➕ TO CREATE)
| Column | Type | Constraints | Description |
|-----------------------|---------------------|-------------------------------|----------------------------------------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Internal ID |
| uuid | CHAR(36) | UNIQUE, NOT NULL | External reference UUID |
| user_id | BIGINT UNSIGNED | FK → users(id), CASCADE DELETE | User reference |
| conversion_type | VARCHAR(50) | NOT NULL | Conversion type |
| source | VARCHAR(100) | NULLABLE | Traffic source |
| medium | VARCHAR(100) | NULLABLE | Traffic medium |
| campaign | VARCHAR(100) | NULLABLE | Campaign name |
| content | VARCHAR(100) | NULLABLE | Content identifier |
| term | VARCHAR(100) | NULLABLE | Search term |
| converted_at | TIMESTAMP | NOT NULL | Conversion timestamp |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

---

## 🔧 Package Integration Tables

### cache (✅ INSTALLED - Laravel)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| key | VARCHAR(255) | PRIMARY KEY | Cache key identifier |
| value | LONGTEXT | NOT NULL | Cached value |
| expiration | INT | NOT NULL | Unix timestamp expiration |

**Package Integration:** This table is installed via Laravel migration (0001_01_01_000001_create_cache_table.php).

### jobs (✅ INSTALLED - Laravel Queue)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Job ID |
| queue | VARCHAR(255) | NOT NULL | Queue name |
| payload | LONGTEXT | NOT NULL | Job data |
| attempts | TINYINT UNSIGNED | NOT NULL | Attempt count |
| reserved_at | INT UNSIGNED | NULLABLE | Reserved timestamp |
| available_at | INT UNSIGNED | NOT NULL | Available timestamp |
| created_at | INT UNSIGNED | NOT NULL | Creation timestamp |

**Package Integration:** This table is installed via Laravel migration (0001_01_01_000002_create_jobs_table.php).

### personal_access_tokens (✅ INSTALLED - Laravel Sanctum)
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

**Package Integration:** This table is installed via Laravel Sanctum migration (2025_08_29_211413_create_personal_access_tokens_table.php).

### permissions (✅ INSTALLED - Spatie Permission)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Permission ID |
| name | VARCHAR(255) | NOT NULL | Permission name |
| guard_name | VARCHAR(255) | NOT NULL | Guard name |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

**Package Integration:** This table is installed via Spatie Permission migration (2025_08_29_211330_create_permission_tables.php).

### roles (✅ INSTALLED - Spatie Permission)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| id | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Role ID |
| name | VARCHAR(255) | NOT NULL | Role name |
| guard_name | VARCHAR(255) | NOT NULL | Guard name |
| created_at | TIMESTAMP | NOT NULL | Creation timestamp |
| updated_at | TIMESTAMP | NOT NULL | Last update timestamp |

**Package Integration:** This table is installed via Spatie Permission migration (2025_08_29_211330_create_permission_tables.php).

### model_has_permissions (✅ INSTALLED - Spatie Permission)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| permission_id | BIGINT UNSIGNED | FK → permissions(id) | Permission reference |
| model_type | VARCHAR(255) | NOT NULL | Model type |
| model_id | BIGINT UNSIGNED | NOT NULL | Model ID |

**Package Integration:** This table is installed via Spatie Permission migration (2025_08_29_211330_create_permission_tables.php).

### model_has_roles (✅ INSTALLED - Spatie Permission)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| role_id | BIGINT UNSIGNED | FK → roles(id) | Role reference |
| model_type | VARCHAR(255) | NOT NULL | Model type |
| model_id | BIGINT UNSIGNED | NOT NULL | Model ID |

**Package Integration:** This table is installed via Spatie Permission migration (2025_08_29_211330_create_permission_tables.php).

### role_has_permissions (✅ INSTALLED - Spatie Permission)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| permission_id | BIGINT UNSIGNED | FK → permissions(id) | Permission reference |
| role_id | BIGINT UNSIGNED | FK → roles(id) | Role reference |

**Package Integration:** This table is installed via Spatie Permission migration (2025_08_29_211330_create_permission_tables.php).

### media (✅ INSTALLED - Spatie MediaLibrary)
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

**Package Integration:** This table is installed via Spatie Media Library migration (2025_08_29_211228_create_media_table.php).

### telescope_entries (✅ INSTALLED - Laravel Telescope)
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

**Package Integration:** This table is installed via Laravel Telescope migration (2025_08_29_211552_create_telescope_entries_table.php).

### telescope_entries_tags (✅ INSTALLED - Laravel Telescope)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| entry_uuid | CHAR(36) | FK → telescope_entries(uuid) | Entry UUID |
| tag | VARCHAR(255) | NOT NULL | Tag value |

**Package Integration:** This table is installed via Laravel Telescope migration (2025_08_29_211552_create_telescope_entries_table.php).

### telescope_monitoring (✅ INSTALLED - Laravel Telescope)
| Column | Type | Constraints | Description |
|---------|------|-------------|-------------|
| tag | VARCHAR(255) | PRIMARY KEY | Monitoring tag |

**Package Integration:** This table is installed via Laravel Telescope migration (2025_08_29_211552_create_telescope_entries_table.php).

---

## Migration Execution Order

1. ✅ **Package Migrations (Already Executed)**
   - 0001_01_01_000000_create_users_table.php (Laravel Breeze)
   - 0001_01_01_000001_create_cache_table.php (Laravel)
   - 0001_01_01_000002_create_jobs_table.php (Laravel Queue)
   - 2025_08_29_211228_create_media_table.php (Spatie Media Library)
   - 2025_08_29_211330_create_permission_tables.php (Spatie Permission)
   - 2025_08_29_211413_create_personal_access_tokens_table.php (Laravel Sanctum)
   - 2025_08_29_211552_create_telescope_entries_table.php (Laravel Telescope)
   - 2025_08_29_211842_create_activity_log_table.php (Spatie Activity Log)

2. ➕ **Custom Business Tables (To Be Created)**
   - 2024_01_02_000000_create_languages_table.php
   - 2024_01_03_000000_create_currencies_table.php
   - 2024_01_04_000000_create_categories_table.php
   - 2024_01_05_000000_create_docu_courses_table.php
   - 2024_01_06_000000_create_episodes_table.php
   - 2024_01_07_000000_create_episode_parts_table.php
   - 2024_01_08_000000_create_user_profiles_table.php
   - 2024_01_09_000000_create_user_levels_table.php
   - 2024_01_10_000000_create_user_progress_table.php
   - 2024_01_11_000000_create_affiliates_table.php
   - 2024_01_12_000000_create_referral_codes_table.php
   - 2024_01_13_000000_create_affiliate_clicks_table.php
   - 2024_01_14_000000_create_conversions_table.php
   - 2024_01_15_000000_create_commissions_table.php
   - 2024_01_16_000000_create_social_shares_table.php
   - 2024_01_17_000000_create_transactions_table.php
   - 2024_01_18_000000_create_payment_methods_table.php
   - 2024_01_19_000000_create_invoices_table.php
   - 2024_01_20_000000_create_user_notes_table.php
   - 2024_01_21_000000_create_user_reactions_table.php
   - 2024_01_22_000000_create_course_uploads_table.php
   - 2024_01_23_000000_create_configurations_table.php
   - 2024_01_24_000000_create_system_metrics_table.php
   - 2024_01_25_000000_add_indexes_for_performance.php
   - 2024_01_26_000000_add_foreign_key_constraints.php

> **Note:** All custom tables should be created with proper foreign key relationships to both existing package tables and other custom tables. The execution order should follow the dependency chain (e.g., create users table before user_profiles, create currencies before transactions, etc.).
