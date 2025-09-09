# DOC_RESOURCES.md

## Laravel Docu-Course Platform - Resources Component Documentation

**Project:** Laravel Docu-Course Platform with Modules Marketplace  
**Laravel Version:** 12.24.0 (EXACT VERSION REQUIRED)  
**PHP Version:** 8.2+ (minimum 8.2.0)  

---

### File: 2030b/app/Http/Resources/BaseResource.php

**Purpose**: Foundation resource class providing common transformation logic, meta data handling, and standardized API resource structure for all API responses.

**Dependencies**:
```php
use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;
use Illuminate\Support\Facades\Auth;
use Carbon\Carbon;
```

**Methods**:
- toArray(Request $request): array
- with(Request $request): array
- formatTimestamp(Carbon $date): string
- includeRelationship(string $relationship): bool
- getUserContext(): array
- formatCurrency(float $amount, string $currency): array
- addMetaData(array $meta): self

**Do**:
- Standardize resource output format
- Include consistent meta information
- Format dates and currencies uniformly
- Handle user context appropriately
- Implement conditional field inclusion
- Support nested resource relationships
- Provide debugging information in development
- Maintain API versioning compatibility

**Don't**:
- Don't expose sensitive internal data
- Don't ignore user permissions in transformations
- Don't include unnecessary data
- Don't break API backward compatibility
- Don't forget to handle null values

---

### File: 2030b/app/Http/Resources/BaseCollection.php

**Purpose**: Base collection resource for handling paginated data, collection metadata, and standardized collection response formatting across the API.

**Dependencies**:
```php
use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\ResourceCollection;
use Illuminate\Pagination\LengthAwarePaginator;
```

**Methods**:
- toArray(Request $request): array
- with(Request $request): array
- paginationInformation(Request $request, array $paginated, array $default): array
- collectionInformation(): array
- formatCollectionMeta(int $total, int $perPage): array

**Do**:
- Provide consistent collection structure
- Include pagination metadata
- Handle empty collections gracefully
- Support collection filtering information
- Include collection statistics
- Maintain consistent response format
- Support custom collection metadata

**Don't**:
- Don't expose collection implementation details
- Don't include unnecessary pagination data
- Don't ignore collection permissions
- Don't break pagination standards
- Don't forget collection context

---

### File: 2030b/app/Http/Resources/PaginatedCollection.php

**Purpose**: Specialized collection resource for advanced pagination handling, cursor pagination, and enhanced pagination metadata with performance optimization.

**Dependencies**:
```php
use App\Http\Resources\BaseCollection;
use Illuminate\Http\Request;
use Illuminate\Pagination\CursorPaginator;
use Illuminate\Support\Collection;
```

**Methods**:
- toArray(Request $request): array
- cursorPaginationInformation(Request $request, array $paginated): array
- getNextCursor(): ?string
- getPreviousCursor(): ?string
- formatPaginationLinks(): array
- includePaginationMeta(): bool

**Do**:
- Support both offset and cursor pagination
- Provide efficient pagination for large datasets
- Include proper pagination links
- Handle pagination edge cases
- Optimize pagination queries
- Support custom pagination sizes
- Include pagination performance metrics

**Don't**:
- Don't expose pagination implementation
- Don't ignore pagination limits
- Don't allow unlimited page sizes
- Don't break pagination consistency
- Don't skip cursor validation

---

### File: 2030b/app/Http/Resources/User/UserResource.php

**Purpose**: User data transformation for API responses including profile information, permissions, achievements, and privacy-compliant user data formatting.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
use App\Enums\UserStatus;
use App\Enums\UserLevel;
use Spatie\Permission\Models\Role;
```

**Methods**:
- toArray(Request $request): array
- includeRoles(): array
- includePermissions(): array
- includeAchievements(): array
- includeStatistics(): array
- formatUserLevel(): array
- includePrivateFields(): bool
- maskSensitiveData(): array

**Do**:
- Respect user privacy settings
- Include role and permission information
- Format user levels and achievements
- Handle user status appropriately
- Include relevant user statistics
- Support conditional field inclusion
- Mask sensitive information properly
- Include user preferences when authorized

**Don't**:
- Don't expose private user data
- Don't include unauthorized information
- Don't ignore privacy settings
- Don't expose sensitive authentication data
- Don't include other users' private fields

---

### File: 2030b/app/Http/Resources/User/UserProfileResource.php

**Purpose**: Detailed user profile transformation including personal information, social links, preferences, and extended profile data with privacy controls.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use App\Http\Resources\Media\MediaResource;
use Illuminate\Http\Request;
use App\Enums\Privacy;
```

**Methods**:
- toArray(Request $request): array
- includePersonalInfo(): array
- includeSocialLinks(): array
- includePreferences(): array
- includeMediaFiles(): array
- checkPrivacyLevel(string $field): bool
- formatContactInfo(): array
- includeProfileStats(): array

**Do**:
- Implement granular privacy controls
- Include profile media files
- Format contact information safely
- Include social media links
- Handle profile completeness metrics
- Support profile customization settings
- Include relevant profile statistics
- Validate privacy permissions

**Don't**:
- Don't ignore privacy settings
- Don't expose contact information inappropriately
- Don't include unauthorized profile data
- Don't bypass privacy controls
- Don't expose profile edit permissions

---

### File: 2030b/app/Http/Resources/User/UserProgressResource.php

**Purpose**: User learning progress transformation including course completion, achievements, points, level progression, and learning analytics data.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
use App\Enums\UserLevel;
use App\Models\Course\Enrollment;
```

**Methods**:
- toArray(Request $request): array
- includeCourseProgress(): array
- includeAchievements(): array
- includePointsBreakdown(): array
- includeLevelProgression(): array
- formatProgressStats(): array
- includeRecentActivity(): array
- calculateCompletionRate(): float

**Do**:
- Include detailed progress metrics
- Show course completion percentages
- Include achievement information
- Format points and level data
- Provide learning analytics
- Include recent learning activity
- Calculate accurate completion rates
- Include progression timelines

**Don't**:
- Don't expose progress calculation logic
- Don't include other users' progress
- Don't ignore progress privacy settings
- Don't provide inaccurate progress data
- Don't skip progress validation

---

### File: 2030b/app/Http/Resources/User/UserLevelResource.php

**Purpose**: User level and tier information transformation including level details, requirements, benefits, and progression tracking with gamification elements.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
use App\Enums\UserLevel;
use App\Services\User\LevelService;
```

**Methods**:
- toArray(Request $request): array
- includeLevelDetails(): array
- includeRequirements(): array
- includeBenefits(): array
- includeProgressToNext(): array
- formatLevelPerks(): array
- includeHistoricalLevels(): array
- calculateProgressPercentage(): float

**Do**:
- Include current level information
- Show requirements for next level
- Include level benefits and perks
- Calculate progress to next level
- Include level achievement dates
- Format level requirements clearly
- Include gamification elements
- Support level comparison features

**Don't**:
- Don't expose level calculation formulas
- Don't include inappropriate level data
- Don't ignore level achievement rules
- Don't provide inaccurate progression data
- Don't skip level validation

---

### File: 2030b/app/Http/Resources/User/UserCollection.php

**Purpose**: User collection resource for handling multiple users with filtering, sorting, search results, and bulk user data transformation.

**Dependencies**:
```php
use App\Http\Resources\BaseCollection;
use App\Http\Resources\User\UserResource;
use Illuminate\Http\Request;
```

**Methods**:
- toArray(Request $request): array
- collectionInformation(): array
- includeFilterInfo(): array
- includeSortingInfo(): array
- includeSearchMeta(): array
- formatUserSummary(): array

**Do**:
- Include search and filter metadata
- Provide collection statistics
- Support user search results
- Include sorting information
- Handle large user collections efficiently
- Include relevant collection context
- Support user directory features

**Don't**:
- Don't expose user collection without authorization
- Don't include private user data in collections
- Don't ignore collection privacy settings
- Don't allow unlimited collection sizes
- Don't skip collection validation

---

### File: 2030b/app/Http/Resources/Course/DocuCourseResource.php

**Purpose**: Course transformation including course details, enrollment information, progress tracking, pricing, and course metadata with access control.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use App\Http\Resources\User\UserResource;
use App\Http\Resources\Media\MediaResource;
use Illuminate\Http\Request;
use App\Enums\CourseStatus;
```

**Methods**:
- toArray(Request $request): array
- includeCourseDetails(): array
- includeEnrollmentInfo(): array
- includeProgressData(): array
- includePricingInfo(): array
- includeInstructorInfo(): array
- includeMediaFiles(): array
- formatCourseMeta(): array
- checkCourseAccess(): bool

**Do**:
- Include comprehensive course information
- Handle course access permissions
- Include enrollment and pricing data
- Format course media properly
- Include instructor information
- Show course progress for enrolled users
- Include course ratings and reviews
- Handle course status appropriately

**Don't**:
- Don't expose draft courses to unauthorized users
- Don't include pricing for free courses
- Don't ignore course access restrictions
- Don't expose course edit permissions inappropriately
- Don't skip course content validation

---

### File: 2030b/app/Http/Resources/Course/EpisodeResource.php

**Purpose**: Episode data transformation including video information, transcripts, progress tracking, interactive elements, and episode metadata.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use App\Http\Resources\Media\MediaResource;
use Illuminate\Http\Request;
use App\Enums\EpisodeStatus;
```

**Methods**:
- toArray(Request $request): array
- includeVideoInfo(): array
- includeTranscriptData(): array
- includeProgressInfo(): array
- includeInteractiveElements(): array
- formatDurationInfo(): array
- includeDownloadLinks(): array
- checkEpisodeAccess(): bool

**Do**:
- Include video streaming information
- Handle episode access permissions
- Include transcript data when available
- Show episode progress for users
- Include interactive elements
- Format video duration properly
- Include download links for authorized users
- Handle episode status correctly

**Don't**:
- Don't expose restricted episodes
- Don't include video URLs without authorization
- Don't ignore episode prerequisites
- Don't skip access control validation
- Don't expose episode processing details

---

### File: 2030b/app/Http/Resources/Course/EpisodePartResource.php

**Purpose**: Episode segment transformation for granular content delivery, bookmarking, note-taking, and detailed progress tracking within episodes.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
use App\Models\Course\EpisodePart;
```

**Methods**:
- toArray(Request $request): array
- includeTimestampInfo(): array
- includeContentData(): array
- includeNotesData(): array
- includeBookmarkInfo(): array
- formatPartDuration(): array
- includeInteractiveData(): array

**Do**:
- Include precise timestamp information
- Handle part-level progress tracking
- Include user notes and bookmarks
- Format content sections clearly
- Include interactive elements
- Support granular content delivery
- Handle part-level access control

**Don't**:
- Don't expose unauthorized content parts
- Don't ignore part-level permissions
- Don't skip content validation
- Don't include inappropriate part data
- Don't bypass episode access control

---

### File: 2030b/app/Http/Resources/Course/CategoryResource.php

**Purpose**: Course category transformation including hierarchical structure, category metadata, course counts, and SEO-optimized category information.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use App\Http\Resources\Media\MediaResource;
use Illuminate\Http\Request;
```

**Methods**:
- toArray(Request $request): array
- includeHierarchy(): array
- includeCourseCount(): int
- includeSubcategories(): array
- includeCategoryMeta(): array
- formatSeoData(): array
- includeIconData(): array

**Do**:
- Include category hierarchy information
- Show course counts per category
- Include subcategory relationships
- Format SEO metadata properly
- Include category icons and images
- Handle category access permissions
- Support category navigation data

**Don't**:
- Don't expose empty categories inappropriately
- Don't ignore category visibility settings
- Don't include unauthorized category data
- Don't skip category validation
- Don't expose category management details

---

### File: 2030b/app/Http/Resources/Course/CourseCollection.php

**Purpose**: Course collection resource for course listings, search results, category-based collections, and bulk course data with filtering and sorting.

**Dependencies**:
```php
use App\Http\Resources\BaseCollection;
use App\Http\Resources\Course\DocuCourseResource;
use Illuminate\Http\Request;
```

**Methods**:
- toArray(Request $request): array
- collectionInformation(): array
- includeFilterInfo(): array
- includeSortingOptions(): array
- formatCourseStats(): array
- includeSearchMeta(): array

**Do**:
- Include course search and filter metadata
- Provide course collection statistics
- Support course directory features
- Include relevant sorting options
- Handle large course collections efficiently
- Include course availability information
- Support course recommendation data

**Don't**:
- Don't include unauthorized courses
- Don't expose draft courses in collections
- Don't ignore course visibility settings
- Don't allow unlimited collection sizes
- Don't skip course access validation

---

### File: 2030b/app/Http/Resources/Course/EpisodeCollection.php

**Purpose**: Episode collection resource for course episode listings, playlist functionality, and episode sequence management with progress tracking.

**Dependencies**:
```php
use App\Http\Resources\BaseCollection;
use App\Http\Resources\Course\EpisodeResource;
use Illuminate\Http\Request;
```

**Methods**:
- toArray(Request $request): array
- includePlaylistInfo(): array
- includeProgressSummary(): array
- formatEpisodeSequence(): array
- includeWatchedStatus(): array
- includeDurationSummary(): array

**Do**:
- Include episode sequence information
- Show progress across all episodes
- Include playlist functionality
- Format episode durations
- Include watched status for users
- Support episode navigation
- Handle episode availability

**Don't**:
- Don't expose restricted episodes
- Don't ignore episode prerequisites
- Don't skip episode access validation
- Don't include inappropriate episode data
- Don't break episode sequence logic

---

### File: 2030b/app/Http/Resources/Payment/TransactionResource.php

**Purpose**: Transaction data transformation including payment details, status information, invoice data, and financial transaction metadata with security measures.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
use App\Enums\TransactionStatus;
use App\Enums\PaymentMethod;
```

**Methods**:
- toArray(Request $request): array
- includePaymentDetails(): array
- includeTransactionMeta(): array
- formatAmountInfo(): array
- includeStatusHistory(): array
- maskSensitiveData(): array
- includeReceiptInfo(): array

**Do**:
- Include comprehensive transaction information
- Mask sensitive payment data
- Format currency amounts properly
- Include transaction status history
- Show receipt information
- Handle transaction permissions
- Include payment method details
- Format transaction timestamps

**Don't**:
- Don't expose sensitive payment data
- Don't include unauthorized transaction details
- Don't ignore transaction privacy
- Don't expose payment processing details
- Don't skip transaction validation

---

### File: 2030b/app/Http/Resources/Payment/InvoiceResource.php

**Purpose**: Invoice data transformation including itemized billing, tax calculations, payment status, and invoice metadata with PDF generation support.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
use App\Enums\InvoiceStatus;
```

**Methods**:
- toArray(Request $request): array
- includeInvoiceItems(): array
- includeTaxCalculations(): array
- includePaymentInfo(): array
- formatInvoiceAmounts(): array
- includeCustomerInfo(): array
- includePdfDownloadLink(): ?string

**Do**:
- Include detailed invoice line items
- Calculate taxes accurately
- Format currency amounts properly
- Include customer information
- Show payment status and history
- Include PDF download links
- Handle invoice permissions
- Format invoice dates correctly

**Don't**:
- Don't expose unauthorized invoice data
- Don't include incorrect tax calculations
- Don't ignore invoice privacy settings
- Don't expose sensitive customer data
- Don't skip invoice validation

---

### File: 2030b/app/Http/Resources/Payment/PaymentMethodResource.php

**Purpose**: Payment method transformation including method details, validation status, security information, and payment method metadata.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
use App\Enums\PaymentProvider;
```

**Methods**:
- toArray(Request $request): array
- includeMethodDetails(): array
- maskPaymentInfo(): array
- includeValidationStatus(): array
- formatExpirationDate(): ?string
- includeSecurityInfo(): array

**Do**:
- Mask sensitive payment method data
- Include payment method validation status
- Format expiration dates properly
- Include security verification information
- Handle payment method permissions
- Show supported currencies
- Include method-specific features

**Don't**:
- Don't expose full payment method details
- Don't include unauthorized payment methods
- Don't ignore payment method security
- Don't expose validation details
- Don't skip payment method validation

---

### File: 2030b/app/Http/Resources/Payment/CurrencyResource.php

**Purpose**: Currency information transformation including exchange rates, formatting rules, symbol display, and currency conversion data.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
use Money\Currency;
```

**Methods**:
- toArray(Request $request): array
- includeExchangeRates(): array
- formatCurrencyRules(): array
- includeSymbolInfo(): array
- includeConversionData(): array
- formatDecimalPlaces(): int

**Do**:
- Include current exchange rates
- Format currency display rules
- Include currency symbols and codes
- Show decimal place requirements
- Include conversion information
- Handle currency availability
- Format currency names properly

**Don't**:
- Don't expose stale exchange rates
- Don't ignore currency formatting rules
- Don't include unauthorized currency data
- Don't skip currency validation
- Don't expose rate calculation details

---

### File: 2030b/app/Http/Resources/Payment/PaymentCollection.php

**Purpose**: Payment collection resource for transaction histories, payment reports, and bulk payment data with filtering and analytics.

**Dependencies**:
```php
use App\Http\Resources\BaseCollection;
use App\Http\Resources\Payment\TransactionResource;
use Illuminate\Http\Request;
```

**Methods**:
- toArray(Request $request): array
- includePaymentSummary(): array
- formatAmountTotals(): array
- includeStatusBreakdown(): array
- includeFilterInfo(): array

**Do**:
- Include payment summary information
- Calculate total amounts accurately
- Show payment status breakdowns
- Include filtering metadata
- Handle payment collection permissions
- Format financial summaries properly
- Include relevant analytics data

**Don't**:
- Don't expose unauthorized payment data
- Don't include sensitive financial details
- Don't ignore payment privacy settings
- Don't skip payment authorization
- Don't expose payment processing details

---

### File: 2030b/app/Http/Resources/Affiliate/AffiliateResource.php

**Purpose**: Affiliate data transformation including affiliate information, commission data, performance metrics, and referral tracking with privacy controls.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use App\Http\Resources\User\UserResource;
use Illuminate\Http\Request;
use App\Enums\AffiliateStatus;
```

**Methods**:
- toArray(Request $request): array
- includeAffiliateInfo(): array
- includePerformanceMetrics(): array
- includeCommissionData(): array
- includeReferralStats(): array
- formatEarnings(): array
- includePayoutInfo(): array

**Do**:
- Include affiliate performance metrics
- Show commission and earnings data
- Include referral statistics
- Format financial information properly
- Handle affiliate permissions
- Include payout information
- Show affiliate status clearly
- Include relevant tracking data

**Don't**:
- Don't expose other affiliates' data
- Don't include unauthorized affiliate information
- Don't ignore affiliate privacy settings
- Don't expose commission calculation details
- Don't skip affiliate validation

---

### File: 2030b/app/Http/Resources/Affiliate/CommissionResource.php

**Purpose**: Commission data transformation including commission details, calculation breakdown, payment status, and commission tracking information.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
use App\Enums\CommissionStatus;
```

**Methods**:
- toArray(Request $request): array
- includeCommissionDetails(): array
- formatCommissionAmount(): array
- includeCalculationBreakdown(): array
- includePaymentStatus(): array
- includeReferralSource(): array

**Do**:
- Include detailed commission information
- Format commission amounts properly
- Show calculation breakdowns
- Include payment status and dates
- Handle commission permissions
- Include referral source information
- Format commission periods correctly

**Don't**:
- Don't expose commission calculation formulas
- Don't include unauthorized commission data
- Don't ignore commission privacy
- Don't expose sensitive calculation details
- Don't skip commission validation

---

### File: 2030b/app/Http/Resources/Affiliate/ReferralCodeResource.php

**Purpose**: Referral code transformation including code details, usage statistics, performance metrics, and tracking information with security measures.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
```

**Methods**:
- toArray(Request $request): array
- includeCodeDetails(): array
- includeUsageStats(): array
- formatPerformanceData(): array
- includeTrackingInfo(): array
- maskCodeSecurity(): array

**Do**:
- Include referral code information
- Show usage statistics
- Include performance metrics
- Handle code permissions properly
- Include tracking information
- Format usage data clearly
- Show code status and validity

**Don't**:
- Don't expose other users' referral codes
- Don't include unauthorized code data
- Don't ignore code security measures
- Don't expose code generation details
- Don't skip code validation

---

### File: 2030b/app/Http/Resources/Affiliate/ConversionResource.php

**Purpose**: Conversion tracking transformation including conversion details, attribution data, funnel analytics, and conversion performance metrics.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
use App\Enums\ConversionType;
```

**Methods**:
- toArray(Request $request): array
- includeConversionDetails(): array
- includeAttributionData(): array
- formatFunnelData(): array
- includeTimingInfo(): array
- includeValueMetrics(): array

**Do**:
- Include conversion tracking information
- Show attribution data properly
- Include funnel analytics
- Format timing information
- Include conversion value metrics
- Handle conversion permissions
- Show conversion sources clearly

**Don't**:
- Don't expose conversion tracking details
- Don't include unauthorized conversion data
- Don't ignore conversion privacy
- Don't expose attribution algorithms
- Don't skip conversion validation

---

### File: 2030b/app/Http/Resources/Affiliate/AffiliateCollection.php

**Purpose**: Affiliate collection resource for affiliate directories, performance rankings, and bulk affiliate data with analytics and filtering.

**Dependencies**:
```php
use App\Http\Resources\BaseCollection;
use App\Http\Resources\Affiliate\AffiliateResource;
use Illuminate\Http\Request;
```

**Methods**:
- toArray(Request $request): array
- includePerformanceSummary(): array
- formatRankingData(): array
- includeFilterInfo(): array
- includeAnalyticsSummary(): array

**Do**:
- Include affiliate performance summaries
- Show ranking information appropriately
- Include filtering metadata
- Handle affiliate collection permissions
- Format analytics data properly
- Include relevant comparison data

**Don't**:
- Don't expose unauthorized affiliate data
- Don't include sensitive performance data
- Don't ignore affiliate privacy settings
- Don't expose ranking algorithms
- Don't skip affiliate authorization

---

### File: 2030b/app/Http/Resources/Media/MediaResource.php

**Purpose**: Media file transformation including file information, metadata, processing status, and media accessibility with CDN integration.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Illuminate\Http\Request;
```

**Methods**:
- toArray(Request $request): array
- includeFileInfo(): array
- includeMetadata(): array
- includeProcessingStatus(): array
- formatFileSize(): string
- includeDownloadUrls(): array
- includeConversions(): array

**Do**:
- Include comprehensive file information
- Show processing status for uploads
- Format file sizes appropriately
- Include download URLs
- Show available file conversions
- Handle media permissions
- Include media metadata
- Support CDN URL generation

**Don't**:
- Don't expose unauthorized media files
- Don't ignore media permissions
- Don't expose processing details
- Don't include inappropriate file data
- Don't skip media validation

---

### File: 2030b/app/Http/Resources/Media/MediaCollection.php

**Purpose**: Media collection resource for file galleries, media libraries, and bulk media operations with organization and filtering capabilities.

**Dependencies**:
```php
use App\Http\Resources\BaseCollection;
use App\Http\Resources\Media\MediaResource;
use Illuminate\Http\Request;
```

**Methods**:
- toArray(Request $request): array
- includeGalleryInfo(): array
- formatStorageStats(): array
- includeFilterOptions(): array
- includeOrganizationData(): array

**Do**:
- Include media gallery information
- Show storage statistics
- Include filtering options
- Handle media collection permissions
- Format storage usage data
- Include organization metadata
- Support media browsing features

**Don't**:
- Don't expose unauthorized media collections
- Don't ignore media privacy settings
- Don't include inappropriate media data
- Don't expose storage implementation details
- Don't skip media authorization

---

### File: 2030b/app/Http/Resources/System/LanguageResource.php

**Purpose**: Language and localization transformation including language details, translation status, locale information, and internationalization data.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
use App\Models\System\Language;
```

**Methods**:
- toArray(Request $request): array
- includeLanguageDetails(): array
- includeTranslationStatus(): array
- formatLocaleInfo(): array
- includeRtlSupport(): bool
- includeFlagIcon(): ?string

**Do**:
- Include language information
- Show translation completion status
- Include locale and region data
- Handle RTL language support
- Include language flags and icons
- Format language names properly
- Show language availability

**Don't**:
- Don't expose incomplete translations inappropriately
- Don't ignore language availability
- Don't include unauthorized language data
- Don't skip locale validation
- Don't expose translation keys

---

### File: 2030b/app/Http/Resources/System/ConfigurationResource.php

**Purpose**: System configuration transformation including settings data, feature flags, configuration metadata, and system parameter information.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
use App\Enums\ConfigType;
```

**Methods**:
- toArray(Request $request): array
- includeConfigDetails(): array
- includeFeatureFlags(): array
- formatConfigValues(): array
- maskSensitiveConfig(): array
- includeConfigMeta(): array

**Do**:
- Include appropriate configuration data
- Handle configuration permissions
- Mask sensitive configuration values
- Include feature flag information
- Format configuration properly
- Show configuration categories
- Include configuration descriptions

**Don't**:
- Don't expose sensitive configuration
- Don't ignore configuration permissions
- Don't include unauthorized config data
- Don't expose system secrets
- Don't skip configuration validation

---

### File: 2030b/app/Http/Resources/System/ActivityLogResource.php

**Purpose**: Activity log transformation including user actions, system events, audit trails, and activity metadata with privacy and security controls.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
use Spatie\Activitylog\Models\Activity;
```

**Methods**:
- toArray(Request $request): array
- includeActivityDetails(): array
- formatActivityData(): array
- includeUserInfo(): array
- maskSensitiveData(): array
- includeContextInfo(): array

**Do**:
- Include relevant activity information
- Format activity data appropriately
- Handle activity log permissions
- Mask sensitive activity data
- Include user context when appropriate
- Show activity timestamps clearly
- Include activity categories

**Don't**:
- Don't expose unauthorized activity logs
- Don't include sensitive activity data
- Don't ignore activity privacy settings
- Don't expose system internals
- Don't skip activity authorization

---

### File: 2030b/app/Http/Resources/Permission/RoleResource.php

**Purpose**: Role information transformation including role details, permissions, user assignments, and role hierarchy with authorization controls.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Spatie\Permission\Models\Role;
use Illuminate\Http\Request;
```

**Methods**:
- toArray(Request $request): array
- includeRoleDetails(): array
- includePermissions(): array
- includeUserCount(): int
- formatRoleHierarchy(): array
- includeRoleCapabilities(): array

**Do**:
- Include role information appropriately
- Show role permissions
- Include user assignment counts
- Handle role hierarchy
- Format role capabilities
- Include role descriptions
- Show role status information

**Don't**:
- Don't expose unauthorized role data
- Don't ignore role permissions
- Don't include inappropriate role information
- Don't expose role management details
- Don't skip role authorization

---

### File: 2030b/app/Http/Resources/Permission/PermissionResource.php

**Purpose**: Permission data transformation including permission details, scope information, resource assignments, and permission metadata.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Spatie\Permission\Models\Permission;
use Illuminate\Http\Request;
```

**Methods**:
- toArray(Request $request): array
- includePermissionDetails(): array
- formatPermissionScope(): array
- includeResourceInfo(): array
- includeRoleAssignments(): array

**Do**:
- Include permission information
- Format permission scopes clearly
- Show resource assignments
- Include role relationships
- Handle permission descriptions
- Format permission categories
- Show permission status

**Don't**:
- Don't expose unauthorized permission data
- Don't ignore permission scope
- Don't include inappropriate permission info
- Don't expose permission internals
- Don't skip permission validation

---

### File: 2030b/app/Http/Resources/Permission/UserPermissionsResource.php

**Purpose**: User-specific permission transformation including effective permissions, role-based permissions, direct assignments, and permission inheritance.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
use App\Models\User;
```

**Methods**:
- toArray(Request $request): array
- includeEffectivePermissions(): array
- includeRolePermissions(): array
- includeDirectPermissions(): array
- formatPermissionInheritance(): array
- includePermissionContext(): array

**Do**:
- Include effective user permissions
- Show role-based permissions
- Include direct permission assignments
- Format permission inheritance
- Handle permission context
- Show permission sources
- Include permission explanations

**Don't**:
- Don't expose other users' permissions
- Don't ignore permission hierarchy
- Don't include unauthorized permission data
- Don't expose permission calculation logic
- Don't skip permission authorization

---

### File: 2030b/app/Http/Resources/Analytics/AnalyticsResource.php

**Purpose**: Analytics data transformation including metrics, charts, performance data, and business intelligence with data aggregation and visualization support.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
use Carbon\Carbon;
```

**Methods**:
- toArray(Request $request): array
- includeMetricsData(): array
- formatChartData(): array
- includePerformanceIndicators(): array
- formatTimeSeriesData(): array
- includeComparisonData(): array
- aggregateAnalyticsData(): array

**Do**:
- Include relevant analytics metrics
- Format chart data appropriately
- Handle analytics permissions
- Include performance indicators
- Format time series data properly
- Include comparison information
- Aggregate data efficiently

**Don't**:
- Don't expose unauthorized analytics data
- Don't ignore analytics privacy settings
- Don't include inappropriate metrics
- Don't expose analytics calculations
- Don't skip analytics authorization

---

### File: 2030b/app/Http/Resources/Analytics/MetricResource.php

**Purpose**: Individual metric transformation including metric values, trends, benchmarks, and performance indicators with historical data tracking.

**Dependencies**:
```php
use App\Http\Resources\BaseResource;
use Illuminate\Http\Request;
use App\Enums\MetricType;
```

**Methods**:
- toArray(Request $request): array
- includeMetricValue(): mixed
- includeTrendData(): array
- formatBenchmarkData(): array
- includeHistoricalData(): array
- calculatePercentageChange(): float

**Do**:
- Include metric values appropriately
- Show trend information
- Include benchmark comparisons
- Format historical data properly
- Calculate percentage changes
- Handle metric permissions
- Include metric context

**Don't**:
- Don't expose unauthorized metrics
- Don't ignore metric privacy settings
- Don't include inappropriate metric data
- Don't expose metric calculation details
- Don't skip metric validation
