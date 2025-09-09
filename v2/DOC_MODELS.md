# DOC_MODELS.md

## Overview
Eloquent models implementing the repository pattern with UUID+ID dual strategy, soft deletes, Spatie package integration, and comprehensive relationships for the Laravel docu-course platform.

## Architecture Principles
- **Dual ID Strategy**: Auto-increment `id` for performance + `uuid` for external references
- **Soft Deletes**: Implemented on critical business entities
- **Spatie Integration**: HasRoles, HasPermissions, HasMedia, LogsActivity traits
- **Type Safety**: Enum casting for status fields stored as TINYINT
- **Relationships**: Comprehensive foreign key relationships with cascade rules

---

## User Models

### app/Models/User.php
**Purpose**: Core user entity extending Laravel Breeze with additional fields for levels, points, preferences, and referral system.

**Dependencies**:
```php
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\SoftDeletes;
use Illuminate\Notifications\Notifiable;
use Laravel\Sanctum\HasApiTokens;
use Spatie\Permission\Traits\HasRoles;
use Spatie\Activitylog\Traits\LogsActivity;
use Spatie\MediaLibrary\HasMedia;
use Spatie\MediaLibrary\InteractsWithMedia;
use App\Traits\HasUuid;
use App\Enums\UserStatus;
use App\Enums\TextDirection;
use App\Enums\Gender;
```

**Methods**:
- profile(): BelongsTo (UserProfile relationship)
- level(): BelongsTo (UserLevel relationship)  
- progress(): HasMany (UserProgress relationship)
- notes(): HasMany (UserNote relationship)
- reactions(): HasMany (UserReaction relationship)
- referrals(): HasMany (self-referential)
- referrer(): BelongsTo (self-referential)
- transactions(): HasMany (Transaction relationship)
- affiliate(): HasOne (Affiliate relationship)
- courses(): HasMany (DocuCourse relationship - authored)
- enrollments(): BelongsToMany (DocuCourse relationship - enrolled)
- getStatusAttribute(): UserStatus
- getTextDirectionAttribute(): TextDirection
- scopeActive(): Builder
- scopeByLevel(): Builder
- calculatePoints(): int
- canAccessCourse(): bool
- hasCompletedCourse(): bool

**Do**:
- Always use UUID for external references and API responses
- Implement soft deletes for user data retention
- Use enum casting for status fields
- Log user activities with Spatie ActivityLog
- Validate user level requirements before granting access
- Use HasRoles trait for permission management

**Don't**:
- Expose auto-increment ID in API responses
- Store sensitive data in JSON preferences field
- Allow direct status changes without validation
- Skip activity logging for sensitive operations
- Use plain text for storing preferences

---

### app/Models/User/UserProfile.php
**Purpose**: Extended user profile information with privacy settings and social media links.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\Gender;
```

**Methods**:
- user(): BelongsTo (User relationship)
- getFullBioAttribute(): string
- getSocialLinksAttribute(): array
- getPrivacySettingsAttribute(): array
- scopePublic(): Builder
- scopeWithSocialLinks(): Builder

**Do**:
- Respect privacy settings when displaying profile data
- Validate social media URLs before saving
- Use mutators for data sanitization
- Implement proper JSON casting for arrays

**Don't**:
- Display private information without permission checks
- Store unvalidated external URLs
- Allow XSS through bio content

---

### app/Models/User/UserLevel.php
**Purpose**: User progression levels (1-14) with point requirements and benefits configuration.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
```

**Methods**:
- users(): HasMany (User relationship)
- getNextLevel(): ?UserLevel
- getPreviousLevel(): ?UserLevel  
- getBenefitsListAttribute(): array
- scopeOrderByLevel(): Builder
- isAccessibleBy(): bool
- getPointsRequiredAttribute(): int

**Do**:
- Use algorithmic point calculations for level requirements
- Store benefits as JSON for flexibility
- Implement level validation logic
- Cache level data for performance

**Don't**:
- Hardcode point requirements in application logic
- Allow manual level adjustments without proper validation
- Expose internal level calculations in API

---

### app/Models/User/UserProgress.php
**Purpose**: Track user progress through episodes with completion percentages and time tracking.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
```

**Methods**:
- user(): BelongsTo (User relationship)
- episode(): BelongsTo (Episode relationship)
- isCompleted(): bool
- calculatePoints(): int
- updateProgress(): void
- getTimeSpentHoursAttribute(): float
- scopeCompleted(): Builder
- scopeInProgress(): Builder

**Do**:
- Update progress atomically to prevent race conditions
- Calculate points based on completion percentage and time
- Track actual reading position for resume functionality
- Implement progress validation rules

**Don't**:
- Allow progress values above 100%
- Skip validation for completion timestamps
- Update progress without user authentication

---

### app/Models/User/UserNote.php
**Purpose**: User-generated notes for episodes with position tracking and privacy settings.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\SoftDeletes;
use App\Traits\HasUuid;
```

**Methods**:
- user(): BelongsTo (User relationship)
- episode(): BelongsTo (Episode relationship)
- scopePublic(): Builder
- scopeAtPosition(): Builder
- getFormattedContentAttribute(): string
- isOwnedBy(): bool

**Do**:
- Sanitize note content to prevent XSS
- Implement soft deletes for note recovery
- Validate position values against episode content
- Respect privacy settings for note sharing

**Don't**:
- Allow HTML content without proper sanitization
- Store notes without user ownership validation
- Display private notes to unauthorized users

---

### app/Models/User/UserReaction.php
**Purpose**: User reactions (like, dislike, etc.) for episodes with type enum validation.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\ReactionType;
```

**Methods**:
- user(): BelongsTo (User relationship)
- episode(): BelongsTo (Episode relationship)
- getTypeAttribute(): ReactionType
- scopeByType(): Builder
- isPositive(): bool
- toggleReaction(): void

**Do**:
- Enforce unique constraint on user-episode-type combination
- Use enum validation for reaction types
- Implement reaction analytics and aggregation
- Prevent duplicate reactions

**Don't**:
- Allow multiple reactions of same type per user per episode
- Skip validation for reaction type values
- Allow reactions on inaccessible content

---

## Course Models

### app/Models/Course/DocuCourse.php
**Purpose**: Main course entity with metadata, pricing, access levels, and content management.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\SoftDeletes;
use Spatie\MediaLibrary\HasMedia;
use Spatie\MediaLibrary\InteractsWithMedia;
use Spatie\Activitylog\Traits\LogsActivity;
use App\Traits\HasUuid;
use App\Enums\CourseStatus;
use App\Enums\ContentAccessLevel;
use App\Enums\DifficultyLevel;
```

**Methods**:
- category(): BelongsTo (Category relationship)
- author(): BelongsTo (User relationship)
- language(): BelongsTo (Language relationship)
- currency(): BelongsTo (Currency relationship)
- episodes(): HasMany (Episode relationship)
- enrollments(): BelongsToMany (User relationship)
- transactions(): HasMany (Transaction relationship)
- getStatusAttribute(): CourseStatus
- getAccessLevelAttribute(): ContentAccessLevel
- getDifficultyLevelAttribute(): DifficultyLevel
- isAccessibleBy(): bool
- getFormattedPriceAttribute(): string
- getTotalDurationAttribute(): int
- scopePublished(): Builder
- scopeByAccessLevel(): Builder
- scopeByDifficulty(): Builder

**Do**:
- Use Spatie MediaLibrary for cover images and thumbnails
- Implement proper access control based on user level and payment
- Store SEO metadata as JSON for flexibility
- Use soft deletes for course archival
- Log all course modifications

**Don't**:
- Allow direct status changes without proper validation
- Store prices without currency association
- Skip access level validation for content display
- Allow course deletion with existing enrollments

---

### app/Models/Course/Episode.php
**Purpose**: Individual course episodes with content files, access control, and progress tracking.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\SoftDeletes;
use App\Traits\HasUuid;
use App\Enums\CourseStatus;
use App\Enums\ContentAccessLevel;
```

**Methods**:
- course(): BelongsTo (DocuCourse relationship)
- parts(): HasMany (EpisodePart relationship)
- progress(): HasMany (UserProgress relationship)
- notes(): HasMany (UserNote relationship)  
- reactions(): HasMany (UserReaction relationship)
- getStatusAttribute(): CourseStatus
- getAccessLevelAttribute(): ContentAccessLevel
- isAccessibleBy(): bool
- getContentPath(): string
- getEstimatedReadingTimeAttribute(): string
- scopePublished(): Builder
- scopeOrderBySortOrder(): Builder

**Do**:
- Validate file paths and ensure content exists
- Implement granular access control per episode
- Use sort order for proper episode sequencing
- Track reading time estimates for user planning

**Don't**:
- Allow access to unpublished episodes without proper authorization
- Skip file validation when updating content paths
- Allow episode deletion with existing user progress

---

### app/Models/Course/EpisodePart.php
**Purpose**: Episode subdivisions for better content organization and granular access control.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\ContentAccessLevel;
```

**Methods**:
- episode(): BelongsTo (Episode relationship)
- getAccessLevelAttribute(): ContentAccessLevel
- isAccessibleBy(): bool
- getContentPath(): string
- scopeOrderByPartNumber(): Builder

**Do**:
- Maintain proper part numbering sequence
- Implement access control inheritance from parent episode
- Validate file paths and content existence

**Don't**:
- Allow gaps in part numbering
- Skip access validation for part content
- Allow part deletion without proper cleanup

---

### app/Models/Course/Category.php  
**Purpose**: Hierarchical course categorization with nested tree structure and sorting.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
```

**Methods**:
- parent(): BelongsTo (self-referential)
- children(): HasMany (self-referential)
- courses(): HasMany (DocuCourse relationship)
- getPathAttribute(): string
- getFullNameAttribute(): string
- scopeRoots(): Builder
- scopeActive(): Builder
- isAncestorOf(): bool
- isDescendantOf(): bool

**Do**:
- Implement proper tree validation to prevent cycles
- Use slug for SEO-friendly URLs
- Maintain sort order for category display
- Validate parent-child relationships

**Don't**:
- Allow circular references in category hierarchy
- Delete categories with existing courses
- Skip slug validation for uniqueness

---

### app/Models/Course/CourseUpload.php
**Purpose**: Track course file uploads with processing status and metadata for bulk content import.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\UploadStatus;
```

**Methods**:
- user(): BelongsTo (User relationship)
- course(): BelongsTo (DocuCourse relationship) 
- getStatusAttribute(): UploadStatus
- getMetadataAttribute(): array
- getFileSizeFormattedAttribute(): string
- isProcessed(): bool
- markAsProcessed(): void
- scopeByStatus(): Builder

**Do**:
- Store comprehensive metadata for processing
- Implement file validation and virus scanning
- Track processing status for user feedback
- Clean up temporary files after processing

**Don't**:
- Store files without proper validation
- Skip virus scanning for uploaded content
- Allow unlimited file sizes without proper handling

---

## Payment Models

### app/Models/Payment/Currency.php
**Purpose**: System currency definitions supporting fiat, crypto, and custom platform currencies.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\CurrencyType;
```

**Methods**:
- courses(): HasMany (DocuCourse relationship)
- transactions(): HasMany (Transaction relationship)
- invoices(): HasMany (Invoice relationship)
- conversions(): HasMany (Conversion relationship)
- commissions(): HasMany (Commission relationship)
- getTypeAttribute(): CurrencyType
- getFormattedSymbolAttribute(): string
- isActive(): bool
- formatAmount(): string
- scopeActive(): Builder
- scopeByType(): Builder

**Do**:
- Implement exchange rate updates for fiat currencies
- Support custom platform currencies for rewards
- Use proper decimal precision for financial calculations
- Maintain currency code uniqueness

**Don't**:
- Allow deletion of currencies with existing transactions
- Skip validation for exchange rate values
- Use floating point arithmetic for monetary calculations

---

### app/Models/Payment/Transaction.php
**Purpose**: Financial transactions with multi-provider support and status tracking.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\TransactionType;
use App\Enums\PaymentStatus;
use App\Enums\PaymentProvider;
```

**Methods**:
- user(): BelongsTo (User relationship)
- currency(): BelongsTo (Currency relationship)
- invoice(): HasOne (Invoice relationship)
- getTypeAttribute(): TransactionType
- getStatusAttribute(): PaymentStatus
- getProviderAttribute(): PaymentProvider
- getMetadataAttribute(): array
- isCompleted(): bool
- getFormattedAmountAttribute(): string
- scopeCompleted(): Builder
- scopeByProvider(): Builder
- scopeByType(): Builder

**Do**:
- Store provider transaction IDs for reconciliation
- Implement proper transaction status workflows
- Use metadata for provider-specific information
- Maintain transaction immutability after completion

**Don't**:
- Allow transaction modifications after completion
- Skip provider validation for payment processing
- Store sensitive payment data in metadata

---

### app/Models/Payment/PaymentMethod.php
**Purpose**: User payment methods with provider integration and verification status.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\PaymentProvider;
use App\Enums\PaymentMethodType;
```

**Methods**:
- user(): BelongsTo (User relationship)
- transactions(): HasMany (Transaction relationship)
- getTypeAttribute(): PaymentMethodType
- getProviderAttribute(): PaymentProvider
- getMetadataAttribute(): array
- isVerified(): bool
- isDefault(): bool
- setAsDefault(): void
- getMaskedDetailsAttribute(): string

**Do**:
- Encrypt sensitive payment method data
- Implement proper verification workflows
- Allow only one default method per user
- Store minimal required metadata

**Don't**:
- Store complete payment card details
- Allow unverified methods for transactions
- Display sensitive payment information in logs

---

### app/Models/Payment/Invoice.php
**Purpose**: Generated invoices for transactions with itemized billing and tax calculations.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\InvoiceStatus;
```

**Methods**:
- user(): BelongsTo (User relationship)
- transaction(): BelongsTo (Transaction relationship)
- currency(): BelongsTo (Currency relationship)
- getStatusAttribute(): InvoiceStatus
- getItemsAttribute(): array
- getTotalAmountAttribute(): string
- isPaid(): bool
- isOverdue(): bool
- calculateTax(): float
- scopePaid(): Builder
- scopeOverdue(): Builder

**Do**:
- Generate unique invoice numbers
- Calculate taxes based on user location
- Store itemized billing details
- Implement proper payment tracking

**Don't**:
- Allow invoice modifications after payment
- Skip tax calculations for applicable jurisdictions
- Generate duplicate invoice numbers

---

## Affiliate Models

### app/Models/Affiliate/Affiliate.php
**Purpose**: Affiliate program participants with commission rates and payout tracking.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\AffiliateStatus;
```

**Methods**:
- user(): BelongsTo (User relationship)
- referralCodes(): HasMany (ReferralCode relationship)
- clicks(): HasMany (AffiliateClick relationship)
- conversions(): HasMany (Conversion relationship)
- commissions(): HasMany (Commission relationship)
- getStatusAttribute(): AffiliateStatus
- getBalanceAttribute(): string
- getTotalEarningsAttribute(): string
- getCommissionRateAttribute(): float
- isActive(): bool
- calculateCommission(): float
- scopeActive(): Builder

**Do**:
- Implement tiered commission structures
- Track total earnings and payouts separately
- Validate commission rate changes
- Maintain affiliate status workflows

**Don't**:
- Allow commission rate changes without approval
- Process payouts without proper verification
- Delete affiliates with pending commissions

---

### app/Models/Affiliate/ReferralCode.php
**Purpose**: Unique referral codes with expiration, usage limits, and type classification.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\ReferralType;
```

**Methods**:
- affiliate(): BelongsTo (Affiliate relationship)
- clicks(): HasMany (AffiliateClick relationship)
- getTypeAttribute(): ReferralType
- isActive(): bool
- isExpired(): bool
- hasReachedLimit(): bool
- canBeUsed(): bool
- incrementUseCount(): void
- scopeActive(): Builder

**Do**:
- Generate unique, URL-safe referral codes
- Implement proper expiration handling
- Track usage limits and counts
- Validate code availability before use

**Don't**:
- Allow duplicate referral codes
- Skip expiration validation
- Allow usage beyond set limits

---

### app/Models/Affiliate/AffiliateClick.php
**Purpose**: Track affiliate link clicks with session data and attribution tracking.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
```

**Methods**:
- affiliate(): BelongsTo (Affiliate relationship)
- referralCode(): BelongsTo (ReferralCode relationship)
- conversion(): HasOne (Conversion relationship)
- hasConverted(): bool
- getAttributionWindowAttribute(): int
- isWithinAttributionWindow(): bool
- scopeWithinWindow(): Builder

**Do**:
- Store comprehensive tracking data
- Implement attribution window validation
- Track unique sessions and users
- Maintain click-to-conversion mapping

**Don't**:
- Store personally identifiable information
- Skip bot detection and filtering
- Allow click manipulation or fraud

---

### app/Models/Affiliate/Conversion.php
**Purpose**: Track successful affiliate conversions with monetary values and approval workflow.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\ConversionType;
use App\Enums\ConversionStatus;
```

**Methods**:
- affiliateClick(): BelongsTo (AffiliateClick relationship)
- user(): BelongsTo (User relationship)
- currency(): BelongsTo (Currency relationship)
- commission(): HasOne (Commission relationship)
- getTypeAttribute(): ConversionType
- getStatusAttribute(): ConversionStatus
- isApproved(): bool
- getFormattedAmountAttribute(): string
- approve(): void
- reject(): void
- scopeApproved(): Builder
- scopePending(): Builder

**Do**:
- Implement conversion approval workflows
- Track conversion values accurately
- Validate conversion attribution
- Prevent duplicate conversions

**Don't**:
- Auto-approve high-value conversions
- Allow conversions without proper click attribution
- Skip fraud detection validation

---

### app/Models/Affiliate/Commission.php
**Purpose**: Calculate and track affiliate commissions with payout schedules and status.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\CommissionStatus;
```

**Methods**:
- affiliate(): BelongsTo (Affiliate relationship)
- conversion(): BelongsTo (Conversion relationship)
- currency(): BelongsTo (Currency relationship)
- getStatusAttribute(): CommissionStatus
- getFormattedAmountAttribute(): string
- isPayable(): bool
- isPaid(): bool
- markAsPaid(): void
- calculateAmount(): float
- scopePayable(): Builder
- scopePaid(): Builder

**Do**:
- Calculate commissions based on conversion values
- Implement payout hold periods
- Track commission payment status
- Validate commission calculations

**Don't**:
- Pay commissions before hold period expires
- Allow commission modifications after payment
- Skip validation for commission rates

---

### app/Models/Affiliate/SocialShare.php
**Purpose**: Track social media shares for attribution and engagement analytics.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\SocialPlatform;
use App\Enums\ContentType;
```

**Methods**:
- user(): BelongsTo (User relationship)
- getPlatformAttribute(): SocialPlatform
- getContentTypeAttribute(): ContentType
- generateShareUrl(): string
- trackClick(): void
- getEngagementMetrics(): array
- scopeByPlatform(): Builder
- scopeByContentType(): Builder

**Do**:
- Generate trackable share URLs
- Track engagement metrics per platform
- Implement proper attribution tracking
- Validate content accessibility before sharing

**Don't**:
- Share private or restricted content
- Skip URL validation for external platforms
- Allow sharing without proper user authentication

---

## System Models

### app/Models/System/Language.php
**Purpose**: Supported languages with localization settings, direction, and activation status.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\LanguageDirection;
```

**Methods**:
- courses(): HasMany (DocuCourse relationship)
- users(): HasMany (User relationship)
- getDirectionAttribute(): LanguageDirection
- isActive(): bool
- isRTL(): bool
- getDisplayNameAttribute(): string
- scopeActive(): Builder
- scopeRTL(): Builder

**Do**:
- Support RTL languages properly
- Maintain language code standards (ISO 639-1)
- Implement proper locale formatting
- Validate language activation

**Don't**:
- Delete languages with existing content
- Allow invalid language codes
- Skip RTL layout considerations

---

### app/Models/System/Configuration.php
**Purpose**: Dynamic system configuration with type validation and grouping for admin management.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\ConfigType;
```

**Methods**:
- getTypeAttribute(): ConfigType
- getValueAttribute(): mixed
- getTypedValue(): mixed
- isPublic(): bool
- scopePublic(): Builder
- scopeByGroup(): Builder
- scopeByType(): Builder

**Do**:
- Implement proper type casting for values
- Use groups for logical configuration organization
- Validate configuration value types
- Cache frequently accessed configurations

**Don't**:
- Store sensitive data in public configurations
- Allow invalid type casting
- Skip validation for configuration updates

---

### app/Models/System/ActivityLog.php
**Purpose**: Extended activity logging with user attribution and subject tracking using Spatie ActivityLog.

**Dependencies**:
```php
use Spatie\Activitylog\Models\Activity;
use App\Traits\HasUuid;
use App\Enums\ActivityType;
```

**Methods**:
- getTypeAttribute(): ActivityType
- getPropertiesAttribute(): array
- user(): BelongsTo (User relationship)
- subject(): MorphTo
- getFormattedDescriptionAttribute(): string
- scopeForUser(): Builder
- scopeByType(): Builder
- scopeRecent(): Builder

**Do**:
- Log all significant user actions
- Store relevant context in properties
- Implement proper data retention policies
- Use morph relationships for flexible subject tracking

**Don't**:
- Log sensitive information in descriptions
- Skip activity logging for security-critical actions
- Store excessive detail in activity properties

---

### app/Models/System/SystemMetric.php
**Purpose**: System performance and usage metrics with time-series data and aggregation support.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\MetricPeriod;
use App\Enums\MetricType;
```

**Methods**:
- getTypeAttribute(): MetricType
- getPeriodAttribute(): MetricPeriod
- getMetadataAttribute(): array
- getFormattedValueAttribute(): string
- scopeByType(): Builder
- scopeByPeriod(): Builder
- scopeRecent(): Builder

**Do**:
- Implement efficient time-series storage
- Use appropriate aggregation periods
- Store metadata for metric context
- Implement data retention policies

**Don't**:
- Store metrics without proper timestamps
- Allow unlimited metric data accumulation
- Skip metric validation and sanitization

---

## Analytics Models

### app/Models/Analytics/UserSession.php
**Purpose**: User session tracking with device information and location data for analytics.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\DeviceType;
```

**Methods**:
- user(): BelongsTo (User relationship)
- contentViews(): HasMany (ContentView relationship)
- getDeviceTypeAttribute(): DeviceType
- isActive(): bool
- getDurationAttribute(): int
- getLocationAttribute(): string
- scopeActive(): Builder
- scopeByDevice(): Builder

**Do**:
- Implement session timeout handling
- Track device and browser information
- Store location data responsibly
- Maintain session security

**Don't**:
- Store personally identifiable location data
- Allow session hijacking
- Skip session validation and cleanup

---

### app/Models/Analytics/ContentView.php
**Purpose**: Track content views with duration and engagement metrics for analytics.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\ContentType;
```

**Methods**:
- user(): BelongsTo (User relationship)
- userSession(): BelongsTo (UserSession relationship)
- getContentTypeAttribute(): ContentType
- getViewDurationHoursAttribute(): float
- isEngagedView(): bool
- scopeEngaged(): Builder
- scopeByContentType(): Builder

**Do**:
- Track meaningful engagement metrics
- Implement view duration thresholds
- Filter out bot traffic
- Aggregate data for performance insights

**Don't**:
- Count brief visits as meaningful views
- Store excessive granular tracking data
- Skip bot and crawler filtering

---

### app/Models/Analytics/EngagementMetric.php
**Purpose**: User engagement metrics aggregation with periodic summaries and trends.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\MetricPeriod;
```

**Methods**:
- user(): BelongsTo (User relationship)
- getPeriodAttribute(): MetricPeriod
- getTrendAttribute(): string
- getEngagementScoreAttribute(): float
- scopeByPeriod(): Builder
- scopeTrending(): Builder

**Do**:
- Calculate meaningful engagement scores
- Implement proper aggregation periods
- Track engagement trends over time
- Use efficient data storage for metrics

**Don't**:
- Calculate engagement without sufficient data
- Store redundant metric calculations
- Skip data validation for metric accuracy

---

### app/Models/Analytics/ConversionTracking.php
**Purpose**: Marketing campaign conversion tracking with attribution and source analysis.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\ConversionType;
```

**Methods**:
- user(): BelongsTo (User relationship)
- getConversionTypeAttribute(): ConversionType
- getAttributionDataAttribute(): array
- getCampaignDataAttribute(): array
- scopeByType(): Builder
- scopeBySource(): Builder
- scopeRecent(): Builder

**Do**:
- Implement proper attribution modeling
- Track campaign effectiveness
- Store UTM parameters and source data
- Validate conversion authenticity

**Don't**:
- Attribute conversions without proper tracking
- Store incomplete campaign data
- Skip conversion validation

---

## API Models

### app/Models/ApiKey.php
**Purpose**: API key management with scopes, rate limiting, and usage tracking for external integrations.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Traits\HasUuid;
use App\Enums\ApiKeyStatus;
```

**Methods**:
- user(): BelongsTo (User relationship)
- getStatusAttribute(): ApiKeyStatus
- getScopesAttribute(): array
- isActive(): bool
- hasScope(): bool
- incrementUsage(): void
- isRateLimited(): bool
- getUsageStatsAttribute(): array
- scopeActive(): Builder

**Do**:
- Generate cryptographically secure API keys
- Implement proper scope validation
- Track API usage and rate limiting
- Validate key permissions for endpoints

**Don't**:
- Store API keys in plain text
- Allow unlimited API access without rate limiting
- Skip scope validation for API requests

---
