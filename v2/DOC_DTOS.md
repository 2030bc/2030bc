# DOC_DTOS.md

## Component 6: DTOs - Immutable Data Transfer Objects

### Overview
Data Transfer Objects provide immutable, validated data structures for transferring information between layers. They ensure type safety, validation, and consistency across the application.

### Architecture Principles
- **Immutability**: All DTOs are readonly for data integrity
- **Validation**: Built-in validation using Laravel validation rules
- **Type Safety**: Strict typing with PHP 8.2+ features
- **Serialization**: JSON serialization support for APIs
- **Laravel Integration**: Seamless integration with requests, resources, and services

---

## User DTOs

### app/DTOs/User/ProfileData.php

**Purpose**: Handles user profile information transfer between controllers, services, and repositories with validation and type safety.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\Gender;
use App\Enums\TextDirection;
use Illuminate\Validation\Rules\Enum;
```

**Properties**:
- readonly string $firstName
- readonly string $lastName  
- readonly ?string $bio
- readonly ?string $websiteUrl
- readonly ?string $linkedinUrl
- readonly ?string $twitterHandle
- readonly ?string $githubUsername
- readonly ?string $country
- readonly ?string $city
- readonly ?Carbon $birthDate
- readonly ?Gender $gender
- readonly ?string $profession
- readonly array $interests
- readonly array $skills
- readonly bool $isPublic
- readonly bool $showEmail
- readonly bool $showProgress
- readonly array $notificationSettings

**Do**:
✅ Validate all input data before DTO creation
✅ Use proper enum types for constrained values
✅ Implement nullable fields appropriately
✅ Sanitize URLs and external links

**Don't**:
❌ Don't expose sensitive information in public profiles
❌ Don't allow invalid URLs or malicious content
❌ Don't skip validation for profile updates
❌ Don't store large binary data in profile DTOs

---

### app/DTOs/User/ProgressData.php

**Purpose**: Manages user learning progress data including completion percentages, time tracking, and achievement data.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use Carbon\Carbon;
```

**Properties**:
- readonly int $userId
- readonly int $episodeId
- readonly float $completionPercentage
- readonly int $timeSpentMinutes
- readonly ?string $lastPosition
- readonly ?Carbon $completedAt
- readonly int $pointsEarned
- readonly array $achievements

**Do**:
✅ Track accurate time spent metrics
✅ Validate completion percentage bounds (0-100)
✅ Handle resume position accurately
✅ Award points based on actual progress

**Don't**:
❌ Don't allow progress manipulation by users
❌ Don't lose progress data due to validation errors
❌ Don't ignore performance impact of frequent updates
❌ Don't award duplicate points for same progress

---

### app/DTOs/User/NoteData.php

**Purpose**: Handles user note creation and management with position tracking and privacy settings.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use Illuminate\Validation\Rule;
```

**Properties**:
- readonly int $userId
- readonly int $episodeId
- readonly string $content
- readonly ?string $position
- readonly bool $isPublic
- readonly array $tags

**Do**:
✅ Sanitize note content to prevent XSS
✅ Implement proper privacy controls
✅ Support rich text formatting safely
✅ Enable collaborative note features

**Don't**:
❌ Don't store unlimited note content without limits
❌ Don't expose private notes publicly
❌ Don't allow malicious content in notes
❌ Don't ignore note versioning for important content

---

### app/DTOs/User/ReactionData.php

**Purpose**: Manages user reactions and feedback on episodes with engagement tracking.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\ContentType;
```

**Properties**:
- readonly int $userId
- readonly int $episodeId
- readonly string $type
- readonly ?string $comment
- readonly int $rating
- readonly Carbon $createdAt

**Do**:
✅ Validate reaction types against allowed values
✅ Implement rate limiting for reactions
✅ Support anonymous feedback where appropriate
✅ Track engagement metrics for analytics

**Don't**:
❌ Don't allow duplicate reactions without business logic
❌ Don't store inappropriate or spam reactions
❌ Don't ignore reaction data for content improvement
❌ Don't expose user identity for anonymous reactions

---

## Course DTOs

### app/DTOs/Course/CourseData.php

**Purpose**: Comprehensive course information transfer including metadata, pricing, access levels, and multi-language support.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\CourseStatus;
use App\Enums\ContentAccessLevel;
use App\Enums\DifficultyLevel;
use Money\Money;
```

**Properties**:
- readonly string $title
- readonly string $alias
- readonly string $description
- readonly ?string $shortDescription
- readonly ?string $coverImage
- readonly ?string $thumbnail
- readonly int $categoryId
- readonly int $authorId
- readonly int $languageId
- readonly CourseStatus $status
- readonly ContentAccessLevel $accessLevel
- readonly int $requiredUserLevel
- readonly ?Money $price
- readonly ?int $currencyId
- readonly bool $isFree
- readonly int $estimatedDurationMinutes
- readonly DifficultyLevel $difficultyLevel
- readonly array $tags
- readonly array $seoMeta

**Do**:
✅ Validate course pricing and currency consistency
✅ Handle multi-language course variants properly
✅ Implement proper SEO metadata structure
✅ Validate access level and user level requirements

**Don't**:
❌ Don't allow inconsistent pricing and free status
❌ Don't skip content validation for published courses
❌ Don't ignore SEO requirements for course discovery
❌ Don't allow invalid category or author assignments

---

### app/DTOs/Course/EpisodeData.php

**Purpose**: Individual episode data with content management, ordering, and access control.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\CourseStatus;
use App\Enums\ContentAccessLevel;
```

**Properties**:
- readonly int $docuCourseId
- readonly string $title
- readonly string $alias
- readonly string $description
- readonly int $sortOrder
- readonly string $filePath
- readonly int $estimatedReadingTimeMinutes
- readonly ContentAccessLevel $accessLevel
- readonly int $requiredUserLevel
- readonly CourseStatus $status

**Do**:
✅ Maintain proper episode ordering within courses
✅ Validate file paths and content accessibility
✅ Implement progressive access based on completion
✅ Calculate accurate reading time estimates

**Don't**:
❌ Don't allow duplicate sort orders within courses
❌ Don't expose unrestricted access to premium content
❌ Don't ignore file validation and security checks
❌ Don't skip reading time calculation updates

---

### app/DTOs/Course/EpisodePartData.php

**Purpose**: Handles episode sub-sections for large content with granular access control and progress tracking.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\ContentAccessLevel;
```

**Properties**:
- readonly int $episodeId
- readonly int $partNumber
- readonly string $title
- readonly string $filePath
- readonly ContentAccessLevel $accessLevel
- readonly int $requiredUserLevel

**Do**:
✅ Implement granular progress tracking per part
✅ Validate part numbering sequence
✅ Handle content chunking for large episodes
✅ Support independent part access control

**Don't**:
❌ Don't create unnecessary content fragmentation
❌ Don't ignore part-level access validation
❌ Don't allow gaps in part numbering
❌ Don't duplicate content across parts unnecessarily

---

### app/DTOs/Course/CategoryData.php

**Purpose**: Course categorization with hierarchical structure support and SEO optimization.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use Illuminate\Validation\Rule;
```

**Properties**:
- readonly string $name
- readonly string $slug
- readonly string $description
- readonly ?int $parentId
- readonly int $sortOrder
- readonly bool $isActive
- readonly array $seoMeta

**Do**:
✅ Implement proper hierarchical category structure
✅ Generate SEO-friendly category slugs
✅ Validate parent-child category relationships
✅ Support category-based content filtering

**Don't**:
❌ Don't create circular category relationships
❌ Don't allow duplicate category slugs
❌ Don't ignore category hierarchy validation
❌ Don't skip category SEO optimization

---

## Payment DTOs

### app/DTOs/Payment/PaymentData.php

**Purpose**: Comprehensive payment processing data with multi-provider support and currency handling.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\PaymentProvider;
use App\Enums\PaymentStatus;
use Money\Money;
```

**Properties**:
- readonly int $userId
- readonly Money $amount
- readonly int $currencyId
- readonly PaymentProvider $provider
- readonly string $description
- readonly ?string $providerTransactionId
- readonly PaymentStatus $status
- readonly array $metadata
- readonly ?Carbon $completedAt

**Do**:
✅ Use proper Money objects for all currency calculations
✅ Validate payment amounts against business rules
✅ Implement idempotency for payment processing
✅ Store comprehensive audit trail for payments

**Don't**:
❌ Don't process payments without proper validation
❌ Don't store sensitive payment credentials
❌ Don't ignore currency conversion requirements
❌ Don't skip webhook signature verification

---

### app/DTOs/Payment/TransactionData.php

**Purpose**: Individual transaction records with detailed metadata and status tracking.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\TransactionType;
use App\Enums\PaymentStatus;
use Money\Money;
```

**Properties**:
- readonly int $userId
- readonly TransactionType $type
- readonly PaymentStatus $status
- readonly Money $amount
- readonly int $currencyId
- readonly PaymentProvider $provider
- readonly ?string $providerTransactionId
- readonly string $description
- readonly array $metadata

**Do**:
✅ Maintain accurate transaction history
✅ Implement proper reconciliation with providers
✅ Handle transaction status updates correctly
✅ Support transaction reporting and analytics

**Don't**:
❌ Don't modify transaction records after creation
❌ Don't ignore provider webhook notifications
❌ Don't store incomplete transaction data
❌ Don't skip transaction verification steps

---

### app/DTOs/Payment/InvoiceData.php

**Purpose**: Invoice generation and management with line items, tax calculations, and payment tracking.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\PaymentStatus;
use Money\Money;
```

**Properties**:
- readonly int $userId
- readonly int $transactionId
- readonly Money $amount
- readonly int $currencyId
- readonly PaymentStatus $status
- readonly Carbon $dueDate
- readonly array $items
- readonly Money $taxAmount
- readonly Money $totalAmount
- readonly ?Carbon $paidAt

**Do**:
✅ Calculate tax amounts accurately
✅ Generate detailed line item breakdowns
✅ Implement proper invoice numbering
✅ Support multiple invoice formats (PDF, email)

**Don't**:
❌ Don't generate invoices without proper line items
❌ Don't ignore tax calculation requirements
❌ Don't allow invoice manipulation after generation
❌ Don't skip invoice delivery confirmation

---

### app/DTOs/Payment/PaymentMethodData.php

**Purpose**: Stored payment method information with security and validation.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\PaymentProvider;
```

**Properties**:
- readonly int $userId
- readonly PaymentProvider $type
- readonly PaymentProvider $provider
- readonly bool $isDefault
- readonly array $metadata
- readonly bool $isVerified

**Do**:
✅ Store only tokenized payment method data
✅ Implement payment method verification
✅ Support multiple payment methods per user
✅ Handle payment method expiration gracefully

**Don't**:
❌ Don't store raw credit card information
❌ Don't allow unverified payment methods for payments
❌ Don't ignore PCI compliance requirements
❌ Don't skip payment method validation

---

## Affiliate DTOs

### app/DTOs/Affiliate/AffiliateData.php

**Purpose**: Affiliate program enrollment and management with commission tracking and payout handling.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\AffiliateStatus;
use Money\Money;
```

**Properties**:
- readonly int $userId
- readonly AffiliateStatus $status
- readonly float $commissionRate
- readonly Money $totalCommissions
- readonly Money $paidCommissions
- readonly Money $balance
- readonly ?Carbon $approvedAt

**Do**:
✅ Validate commission rate boundaries
✅ Track accurate commission calculations
✅ Implement proper payout scheduling
✅ Support tiered commission structures

**Don't**:
❌ Don't allow negative commission balances
❌ Don't skip affiliate verification processes
❌ Don't ignore commission rate validation
❌ Don't process payouts without proper approval

---

### app/DTOs/Affiliate/ReferralCodeData.php

**Purpose**: Referral code creation and management with usage tracking and expiration handling.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\ReferralType;
```

**Properties**:
- readonly int $affiliateId
- readonly string $code
- readonly ReferralType $type
- readonly bool $isActive
- readonly ?Carbon $expirationDate
- readonly ?int $maxUses
- readonly int $useCount

**Do**:
✅ Generate unique, non-guessable referral codes
✅ Implement proper expiration date handling
✅ Track referral code usage accurately
✅ Support different referral code types

**Don't**:
❌ Don't allow duplicate referral codes
❌ Don't ignore expiration date validation
❌ Don't exceed maximum usage limits
❌ Don't allow inactive codes for new referrals

---

### app/DTOs/Affiliate/CommissionData.php

**Purpose**: Commission calculation and payout tracking with detailed attribution and status management.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\CommissionStatus;
use Money\Money;
```

**Properties**:
- readonly int $affiliateId
- readonly int $conversionId
- readonly Money $amount
- readonly int $currencyId
- readonly float $rate
- readonly CommissionStatus $status
- readonly ?Carbon $payableAt
- readonly ?Carbon $paidAt

**Do**:
✅ Calculate commissions accurately based on rates
✅ Implement proper commission approval workflow
✅ Track commission payment status precisely
✅ Support commission reversals for refunds

**Don't**:
❌ Don't calculate commissions on refunded transactions
❌ Don't pay commissions without proper approval
❌ Don't ignore commission calculation errors
❌ Don't skip commission audit trail

---

### app/DTOs/Affiliate/ConversionData.php

**Purpose**: Conversion tracking with attribution and analytics for affiliate performance measurement.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\ConversionType;
use App\Enums\PaymentStatus;
use Money\Money;
```

**Properties**:
- readonly int $affiliateClickId
- readonly ConversionType $type
- readonly int $userId
- readonly Money $amount
- readonly int $currencyId
- readonly PaymentStatus $status
- readonly Carbon $convertedAt
- readonly ?Carbon $approvedAt

**Do**:
✅ Track accurate conversion attribution
✅ Implement proper conversion validation
✅ Support different conversion types (signup, purchase)
✅ Handle conversion approval workflow

**Don't**:
❌ Don't double-count conversions
❌ Don't approve fraudulent conversions
❌ Don't ignore conversion quality validation
❌ Don't skip conversion verification steps

---

### app/DTOs/Affiliate/SocialShareData.php

**Purpose**: Social media sharing tracking for viral marketing and affiliate promotion analytics.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\SocialPlatform;
use App\Enums\ContentType;
```

**Properties**:
- readonly int $userId
- readonly SocialPlatform $platform
- readonly ContentType $contentType
- readonly int $contentId
- readonly string $shareUrl

**Do**:
✅ Track social sharing for viral analytics
✅ Generate trackable social share URLs
✅ Support multiple social media platforms
✅ Implement social sharing incentives

**Don't**:
❌ Don't track social sharing without user consent
❌ Don't ignore social sharing privacy settings
❌ Don't create broken or invalid share URLs
❌ Don't skip social sharing performance analysis

---

## System DTOs

### app/DTOs/System/LanguageData.php

**Purpose**: Multi-language system configuration with RTL support and locale management.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\LanguageDirection;
```

**Properties**:
- readonly string $code
- readonly string $name
- readonly string $nativeName
- readonly LanguageDirection $direction
- readonly string $locale
- readonly bool $isActive
- readonly int $sortOrder

**Do**:
✅ Support both LTR and RTL languages
✅ Validate ISO language codes
✅ Implement proper locale fallback
✅ Handle language-specific formatting

**Don't**:
❌ Don't assume left-to-right text direction
❌ Don't ignore language-specific date/number formats
❌ Don't allow invalid language codes
❌ Don't skip language availability validation

---

### app/DTOs/System/ConfigurationData.php

**Purpose**: System configuration management with type safety and environment-specific overrides.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\ConfigType;
```

**Properties**:
- readonly string $key
- readonly mixed $value
- readonly ConfigType $type
- readonly string $group
- readonly bool $isPublic
- readonly string $description

**Do**:
✅ Validate configuration values by type
✅ Implement proper configuration caching
✅ Support environment-specific overrides
✅ Document configuration options thoroughly

**Don't**:
❌ Don't expose sensitive configuration publicly
❌ Don't allow invalid configuration values
❌ Don't ignore configuration validation
❌ Don't skip configuration backup and restore

---

### app/DTOs/System/ActivityLogData.php

**Purpose**: System activity logging with user attribution and detailed event information.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\ActivityType;
```

**Properties**:
- readonly int $userId
- readonly ActivityType $type
- readonly string $description
- readonly string $subjectType
- readonly int $subjectId
- readonly array $properties
- readonly string $ipAddress
- readonly string $userAgent

**Do**:
✅ Log significant user and system activities
✅ Include relevant context and metadata
✅ Implement proper data retention policies
✅ Support activity log analytics and reporting

**Don't**:
❌ Don't log sensitive user data
❌ Don't create excessive log entries
❌ Don't ignore log data privacy requirements
❌ Don't skip log data validation

---

### app/DTOs/System/SystemMetricData.php

**Purpose**: System performance and business metrics collection with time-series data support.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\MetricPeriod;
```

**Properties**:
- readonly string $metricType
- readonly MetricPeriod $period
- readonly float $value
- readonly Carbon $recordedAt
- readonly array $metadata

**Do**:
✅ Collect relevant business and technical metrics
✅ Implement proper metric aggregation
✅ Support time-series metric analysis
✅ Create meaningful metric dashboards

**Don't**:
❌ Don't collect metrics without purpose
❌ Don't ignore metric data quality
❌ Don't skip metric data retention policies
❌ Don't allow metric data manipulation

---

## Auth DTOs

### app/DTOs/Auth/LoginData.php

**Purpose**: User authentication data with security validation and rate limiting support.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\AuthProvider;
```

**Properties**:
- readonly string $email
- readonly string $password
- readonly bool $remember
- readonly AuthProvider $provider
- readonly ?string $providerToken
- readonly string $ipAddress
- readonly string $userAgent

**Do**:
✅ Validate email format and password strength
✅ Implement proper rate limiting
✅ Support multiple authentication providers
✅ Log authentication attempts for security

**Don't**:
❌ Don't store plain text passwords
❌ Don't ignore brute force protection
❌ Don't allow weak password policies
❌ Don't skip authentication logging

---

### app/DTOs/Auth/RegisterData.php

**Purpose**: User registration with comprehensive validation and welcome workflow integration.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\AuthProvider;
use App\Rules\StrongPassword;
```

**Properties**:
- readonly string $name
- readonly string $email
- readonly string $password
- readonly string $passwordConfirmation
- readonly ?string $referralCode
- readonly AuthProvider $provider
- readonly bool $agreedToTerms
- readonly bool $subscribedToNewsletter

**Do**:
✅ Enforce strong password requirements
✅ Validate email uniqueness and format
✅ Handle referral code processing
✅ Implement proper terms agreement validation

**Don't**:
❌ Don't allow registration without terms agreement
❌ Don't skip email verification requirements
❌ Don't ignore password confirmation validation
❌ Don't allow duplicate email addresses

---

### app/DTOs/Auth/PasswordResetData.php

**Purpose**: Password reset workflow with token validation and security measures.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Rules\StrongPassword;
```

**Properties**:
- readonly string $email
- readonly string $token
- readonly string $password
- readonly string $passwordConfirmation

**Do**:
✅ Validate password reset tokens properly
✅ Enforce password strength requirements
✅ Implement token expiration handling
✅ Log password reset activities

**Don't**:
❌ Don't allow weak passwords during reset
❌ Don't reuse expired or invalid tokens
❌ Don't skip password confirmation validation
❌ Don't ignore password reset rate limiting

---

### app/DTOs/Auth/TwoFactorData.php

**Purpose**: Two-factor authentication setup and verification with backup codes support.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\TwoFactorMethod;
```

**Properties**:
- readonly int $userId
- readonly TwoFactorMethod $method
- readonly string $code
- readonly ?string $secret
- readonly ?array $backupCodes

**Do**:
✅ Support multiple 2FA methods (TOTP, SMS, email)
✅ Generate secure backup codes
✅ Validate 2FA codes with time tolerance
✅ Implement proper 2FA recovery options

**Don't**:
❌ Don't store 2FA secrets in plain text
❌ Don't allow 2FA bypass without proper validation
❌ Don't ignore 2FA code replay attacks
❌ Don't skip 2FA setup verification

---

### app/DTOs/Auth/SanctumTokenData.php

**Purpose**: Laravel Sanctum API token management with scopes and expiration handling.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\TokenType;
use Laravel\Sanctum\PersonalAccessToken;
```

**Properties**:
- readonly int $userId
- readonly string $name
- readonly TokenType $type
- readonly array $abilities
- readonly ?Carbon $expiresAt

**Do**:
✅ Implement proper token scopes and abilities
✅ Set appropriate token expiration times
✅ Support token revocation and rotation
✅ Log token usage for security monitoring

**Don't**:
❌ Don't create tokens with excessive permissions
❌ Don't allow indefinite token lifetimes
❌ Don't ignore token usage monitoring
❌ Don't skip token validation in API requests

---

## Media DTOs

### app/DTOs/Media/MediaData.php

**Purpose**: Media file metadata management with Spatie Media Library integration and optimization settings.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\MediaCollection;
use App\Enums\FileType;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
```

**Properties**:
- readonly string $name
- readonly string $fileName
- readonly MediaCollection $collection
- readonly FileType $type
- readonly int $size
- readonly string $mimeType
- readonly array $customProperties
- readonly array $conversions

**Do**:
✅ Validate file types and sizes per collection
✅ Generate appropriate image conversions
✅ Store file metadata for search and organization
✅ Implement virus scanning for uploaded files

**Don't**:
❌ Don't allow unlimited file uploads
❌ Don't skip file type validation
❌ Don't store files without proper organization
❌ Don't ignore file security scanning

---

### app/DTOs/Media/ImageOptimizationData.php

**Purpose**: Image processing and optimization configuration with format conversion and quality settings.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\ImageConversion;
use Spatie\Image\Enums\Fit;
```

**Properties**:
- readonly ImageConversion $conversion
- readonly int $width
- readonly int $height
- readonly int $quality
- readonly Fit $fit
- readonly bool $optimize
- readonly string $format

**Do**:
✅ Optimize images for web performance
✅ Generate responsive image sizes
✅ Convert images to modern formats (WebP)
✅ Maintain image quality while reducing size

**Don't**:
❌ Don't generate unnecessary image conversions
❌ Don't ignore image optimization opportunities
❌ Don't create oversized images for small displays
❌ Don't skip image format validation

---

### app/DTOs/Media/FileUploadData.php

**Purpose**: File upload handling with progress tracking, chunked upload support, and validation.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use Illuminate\Http\UploadedFile;
use App\Enums\FileType;
```

**Properties**:
- readonly UploadedFile $file
- readonly FileType $type
- readonly MediaCollection $collection
- readonly array $metadata
- readonly bool $isChunked
- readonly ?string $chunkId

**Do**:
✅ Support large file uploads with chunking
✅ Validate file integrity and security
✅ Provide upload progress feedback
✅ Handle upload interruptions gracefully

**Don't**:
❌ Don't allow uploads without proper validation
❌ Don't ignore file size and type restrictions
❌ Don't skip virus scanning for uploads
❌ Don't leave incomplete uploads in storage

---

## Utility DTOs

### app/DTOs/ApiResponse.php

**Purpose**: Standardized API response format with consistent error handling and pagination support.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use Illuminate\Http\JsonResponse;
```

**Properties**:
- readonly bool $success
- readonly mixed $data
- readonly ?string $message
- readonly ?array $errors
- readonly ?array $meta
- readonly int $statusCode

**Do**:
✅ Maintain consistent API response format
✅ Include proper HTTP status codes
✅ Provide helpful error messages
✅ Support pagination metadata

**Don't**:
❌ Don't expose internal error details to clients
❌ Don't return inconsistent response formats
❌ Don't ignore proper HTTP status code usage
❌ Don't skip API response validation

---

### app/DTOs/WebhookData.php

**Purpose**: Webhook payload processing with signature verification and event type handling.

**Dependencies**:
```php
use App\Foundation\Core\BaseDto;
use App\Enums\WebhookEvent;
use App\Enums\PaymentProvider;
```

**Properties**:
- readonly WebhookEvent $event
- readonly PaymentProvider $provider
- readonly array $payload
- readonly string $signature
- readonly Carbon $timestamp

**Do**:
✅ Verify webhook signatures properly
✅ Handle webhook idempotency
✅ Process webhooks asynchronously
✅ Log webhook processing for debugging

**Don't**:
❌ Don't process webhooks without signature verification
❌ Don't ignore webhook processing failures
❌ Don't handle webhooks synchronously for slow operations
❌ Don't skip webhook retry logic

---

## DTO Integration Best Practices

### Validation Rules
- Implement comprehensive validation in DTO constructors
- Use Laravel validation rules with custom rule objects
- Support nested validation for complex DTOs
- Provide clear validation error messages

### Immutability Patterns
- Use readonly properties for all DTO fields
- Implement proper constructor validation
- Support DTO transformation methods
- Handle nullable fields appropriately

### Serialization Support
- Implement JsonSerializable interface
- Support API resource transformation
- Handle nested DTO serialization
- Provide proper date formatting

### Performance Considerations
- Use DTOs for data validation boundaries
- Implement DTO caching for expensive validations
- Support batch DTO processing
- Optimize DTO memory usage

### Testing Requirements
- Unit test DTO validation rules
- Test DTO serialization and deserialization
- Verify DTO immutability guarantees
- Test DTO integration with Laravel features