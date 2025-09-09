# DOC_CONTRACTS_INTERFACES.md: Contracts & Interfaces

## Component Overview
**Component 7: Contracts** - Service interfaces, repository abstracts, and dependency injection contracts for the Laravel docu-course platform. These interfaces define the contract boundaries between different layers of the application, ensuring loose coupling and testability.

---

## 📁 Service Contracts

### app/Contracts/Services/UserServiceInterface.php
**Purpose:** Defines the contract for user management services, including user creation, updates, level progression, and profile management.

**Dependencies:**
```php
use App\DTOs\User\ProfileData;
use App\DTOs\User\ProgressData;
use App\Models\User;
use App\Enums\UserStatus;
use App\Enums\UserLevel;
use Illuminate\Support\Collection;
use Illuminate\Pagination\LengthAwarePaginator;
```

**Methods:**
- create
- update
- delete
- findById
- findByUuid
- findByEmail
- updateProfile
- calculateUserLevel
- updateUserLevel
- getUserProgress
- getUserStatistics
- searchUsers
- getPaginatedUsers
- activateUser
- deactivateUser
- banUser

**Do:**
✅ Use DTOs for data transfer between layers
✅ Return standardized response formats
✅ Include proper type hints for all parameters and return values
✅ Handle user level calculations consistently
✅ Implement proper error handling for user operations

**Don't:**
❌ Directly access Eloquent models in the interface
❌ Include implementation details in method signatures
❌ Mix authentication logic with user management
❌ Return raw database results without transformation
❌ Ignore user permission checks

---

### app/Contracts/Services/AuthServiceInterface.php
**Purpose:** Defines authentication service contract including login, registration, password management, and two-factor authentication.

**Dependencies:**
```php
use App\DTOs\Auth\LoginData;
use App\DTOs\Auth\RegisterData;
use App\DTOs\Auth\PasswordResetData;
use App\DTOs\Auth\TwoFactorData;
use App\DTOs\Auth\SanctumTokenData;
use App\Models\User;
use App\Enums\AuthProvider;
use App\Enums\TokenType;
use Laravel\Sanctum\PersonalAccessToken;
```

**Methods:**
- login
- register
- logout
- refreshToken
- forgotPassword
- resetPassword
- changePassword
- enableTwoFactor
- disableTwoFactor
- verifyTwoFactor
- socialLogin
- createApiToken
- revokeApiToken
- validateToken
- getUserFromToken

**Do:**
✅ Implement secure password handling
✅ Support multiple authentication providers
✅ Handle two-factor authentication properly
✅ Generate secure API tokens
✅ Log authentication events for security

**Don't:**
❌ Store passwords in plain text
❌ Skip rate limiting for authentication attempts
❌ Expose sensitive authentication data
❌ Allow weak password policies
❌ Ignore failed authentication logging

---

### app/Contracts/Services/PaymentServiceInterface.php
**Purpose:** Defines payment processing service contract supporting multiple payment providers including Stripe and Binance.

**Dependencies:**
```php
use App\DTOs\Payment\PaymentData;
use App\DTOs\Payment\TransactionData;
use App\DTOs\Payment\InvoiceData;
use App\Models\Payment\Transaction;
use App\Models\Payment\Currency;
use App\Enums\PaymentStatus;
use App\Enums\PaymentProvider;
use App\Enums\TransactionType;
use Money\Money;
```

**Methods:**
- processPayment
- createTransaction
- updateTransactionStatus
- generateInvoice
- refundPayment
- getTransactionHistory
- validatePaymentData
- calculateFees
- convertCurrency
- handleWebhook
- getPaymentMethods
- addPaymentMethod
- removePaymentMethod

**Do:**
✅ Use Money objects for precise currency calculations
✅ Support multiple payment providers
✅ Implement proper webhook validation
✅ Handle payment failures gracefully
✅ Log all payment activities for audit

**Don't:**
❌ Process payments without proper validation
❌ Store sensitive payment data unencrypted
❌ Skip transaction logging
❌ Ignore payment provider webhooks
❌ Use floating point for currency calculations

---

### app/Contracts/Services/CourseServiceInterface.php
**Purpose:** Defines course management service contract including course creation, publishing, enrollment, and content management.

**Dependencies:**
```php
use App\DTOs\Course\CourseData;
use App\DTOs\Course\EpisodeData;
use App\DTOs\Course\CategoryData;
use App\Models\Course\DocuCourse;
use App\Models\Course\Episode;
use App\Enums\CourseStatus;
use App\Enums\ContentAccessLevel;
use App\Enums\DifficultyLevel;
use Illuminate\Support\Collection;
```

**Methods:**
- createCourse
- updateCourse
- deleteCourse
- publishCourse
- unpublishCourse
- getCourseById
- getCourseByAlias
- searchCourses
- getCoursesByCategory
- enrollUser
- unenrollUser
- getUserEnrollments
- trackProgress
- getCourseStatistics
- validateCourseAccess
- generateCourseCertificate

**Do:**
✅ Validate user access levels before enrollment
✅ Track user progress accurately
✅ Handle course publishing workflow properly
✅ Support multi-language course content
✅ Implement proper course search functionality

**Don't:**
❌ Allow access to unpublished courses without permission
❌ Skip course content validation
❌ Ignore user level requirements
❌ Allow duplicate course enrollments
❌ Forget to update course statistics

---

### app/Contracts/Services/AffiliateServiceInterface.php
**Purpose:** Defines affiliate program service contract including referral tracking, commission calculations, and payout management.

**Dependencies:**
```php
use App\DTOs\Affiliate\AffiliateData;
use App\DTOs\Affiliate\ReferralCodeData;
use App\DTOs\Affiliate\CommissionData;
use App\DTOs\Affiliate\ConversionData;
use App\Models\Affiliate\Affiliate;
use App\Models\Affiliate\Commission;
use App\Enums\AffiliateStatus;
use App\Enums\CommissionStatus;
use App\Enums\ConversionType;
```

**Methods:**
- createAffiliate
- updateAffiliateStatus
- generateReferralCode
- trackClick
- trackConversion
- calculateCommission
- processCommissionPayment
- getAffiliateStatistics
- getCommissionHistory
- validateReferralCode
- getPendingCommissions
- approveCommission
- rejectCommission

**Do:**
✅ Track all affiliate clicks and conversions accurately
✅ Calculate commissions based on user levels
✅ Handle commission approval workflow
✅ Provide detailed affiliate statistics
✅ Validate referral codes before use

**Don't:**
❌ Allow duplicate commission calculations
❌ Skip affiliate click tracking
❌ Process payments without approval
❌ Ignore commission rate validation
❌ Allow self-referrals

---

### app/Contracts/Services/MediaServiceInterface.php
**Purpose:** Defines media management service contract using Spatie Media Library for file uploads, optimization, and storage.

**Dependencies:**
```php
use App\DTOs\Media\MediaData;
use App\DTOs\Media\FileUploadData;
use App\DTOs\Media\ImageOptimizationData;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use App\Enums\MediaCollection;
use App\Enums\FileType;
use App\Enums\StorageDisk;
use Illuminate\Http\UploadedFile;
```

**Methods:**
- uploadFile
- uploadImage
- uploadVideo
- uploadDocument
- optimizeImage
- generateThumbnails
- deleteMedia
- getMediaByCollection
- validateFileType
- validateFileSize
- convertFileFormat
- getMediaUrl
- moveToCollection

**Do:**
✅ Validate file types and sizes before upload
✅ Optimize images automatically
✅ Generate multiple image conversions
✅ Use appropriate storage disks
✅ Clean up temporary files

**Don't:**
❌ Upload files without validation
❌ Store large files on local disk
❌ Skip virus scanning for uploads
❌ Allow unlimited file sizes
❌ Forget to generate thumbnails for images

---

### app/Contracts/Services/NotificationServiceInterface.php
**Purpose:** Defines notification service contract supporting multiple channels including email, SMS, and push notifications.

**Dependencies:**
```php
use App\Models\User;
use Illuminate\Notifications\Notification;
use App\Enums\NotificationChannel;
use Illuminate\Support\Collection;
```

**Methods:**
- send
- sendToUser
- sendToUsers
- sendBulk
- schedule
- cancel
- getNotificationHistory
- markAsRead
- markAllAsRead
- getUnreadCount
- getUserPreferences
- updateUserPreferences

**Do:**
✅ Respect user notification preferences
✅ Support multiple notification channels
✅ Queue bulk notifications for performance
✅ Track notification delivery status
✅ Handle notification failures gracefully

**Don't:**
❌ Send notifications without user consent
❌ Ignore user unsubscribe preferences
❌ Send bulk notifications synchronously
❌ Skip notification logging
❌ Allow spam notifications

---

### app/Contracts/Services/CacheServiceInterface.php
**Purpose:** Defines caching service contract for application-wide cache management with Redis support.

**Dependencies:**
```php
use Illuminate\Cache\Repository;
use Illuminate\Support\Collection;
use Carbon\Carbon;
```

**Methods:**
- get
- set
- forget
- flush
- remember
- rememberForever
- increment
- decrement
- tags
- invalidateTag
- lock
- release

**Do:**
✅ Use appropriate cache TTL values
✅ Implement cache tagging for organized invalidation
✅ Handle cache misses gracefully
✅ Use cache locks for critical sections
✅ Monitor cache hit ratios

**Don't:**
❌ Cache sensitive user data
❌ Set extremely long cache TTL
❌ Forget to invalidate stale cache
❌ Cache large objects unnecessarily
❌ Ignore cache failures

---

### app/Contracts/Services/SearchServiceInterface.php
**Purpose:** Defines search service contract supporting full-text search with Laravel Scout and Elasticsearch.

**Dependencies:**
```php
use Laravel\Scout\Builder;
use Illuminate\Support\Collection;
use App\Models\Course\DocuCourse;
use App\Models\User;
```

**Methods:**
- search
- searchCourses
- searchUsers
- searchContent
- buildQuery
- addFilter
- addSort
- paginate
- highlight
- suggest
- updateIndex
- deleteFromIndex

**Do:**
✅ Index searchable content properly
✅ Implement fuzzy search for better UX
✅ Support advanced filtering
✅ Highlight search terms in results
✅ Update search index when content changes

**Don't:**
❌ Search without proper indexing
❌ Ignore search performance optimization
❌ Return raw search results without formatting
❌ Skip search analytics
❌ Allow unrestricted search queries

---

## 📁 Repository Contracts

### app/Contracts/Repositories/BaseRepositoryInterface.php
**Purpose:** Base repository contract defining common CRUD operations and query patterns for all repository implementations.

**Dependencies:**
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Collection;
use Illuminate\Pagination\LengthAwarePaginator;
use Illuminate\Support\Collection as SupportCollection;
```

**Methods:**
- create
- update
- delete
- find
- findOrFail
- findBy
- findWhere
- first
- all
- paginate
- count
- exists
- with
- withTrashed
- onlyTrashed
- restore
- forceDelete

**Do:**
✅ Implement consistent method signatures across repositories
✅ Support soft deletes where applicable
✅ Use eager loading to prevent N+1 queries
✅ Return DTOs instead of raw models
✅ Implement proper error handling

**Don't:**
❌ Return null without proper handling
❌ Skip input validation
❌ Ignore soft delete functionality
❌ Expose raw Eloquent queries
❌ Allow unlimited result sets

---

### app/Contracts/Repositories/UserRepositoryInterface.php
**Purpose:** User repository contract extending base repository with user-specific query methods and relationships.

**Dependencies:**
```php
use App\Contracts\Repositories\BaseRepositoryInterface;
use App\Models\User;
use App\Enums\UserStatus;
use App\Enums\UserLevel;
use Illuminate\Support\Collection;
```

**Methods:**
- findByEmail
- findByUsername
- findByReferralCode
- getUsersByLevel
- getUsersByStatus
- getActiveUsers
- getInactiveUsers
- getUserProgress
- getUserNotes
- getUserReactions
- searchUsers
- getUsersWithRoles
- getUsersWithPermissions

**Do:**
✅ Index frequently queried fields
✅ Use UUID for external references
✅ Support user level filtering
✅ Include user relationship data efficiently
✅ Implement user search functionality

**Don't:**
❌ Query users without proper indexing
❌ Expose sensitive user data
❌ Skip user status validation
❌ Allow duplicate email addresses
❌ Ignore user privacy settings

---

### app/Contracts/Repositories/CourseRepositoryInterface.php
**Purpose:** Course repository contract for managing docu-courses, episodes, and related content with access control.

**Dependencies:**
```php
use App\Contracts\Repositories\BaseRepositoryInterface;
use App\Models\Course\DocuCourse;
use App\Models\Course\Episode;
use App\Enums\CourseStatus;
use App\Enums\ContentAccessLevel;
use Illuminate\Support\Collection;
```

**Methods:**
- findByAlias
- getCoursesByCategory
- getCoursesByAuthor
- getPublishedCourses
- getDraftCourses
- getCoursesByStatus
- getCoursesByAccessLevel
- getCoursesRequiringLevel
- getPopularCourses
- getRecentCourses
- searchCourses
- getCourseWithEpisodes
- getUserEnrolledCourses

**Do:**
✅ Filter courses by publication status
✅ Respect user access levels
✅ Include course statistics in results
✅ Support multi-language content
✅ Optimize queries for course listings

**Don't:**
❌ Return unpublished courses to unauthorized users
❌ Skip access level validation
❌ Query courses without proper indexing
❌ Ignore course enrollment status
❌ Allow access to premium content without payment

---

### app/Contracts/Repositories/PaymentRepositoryInterface.php
**Purpose:** Payment repository contract for managing transactions, invoices, and payment methods with currency support.

**Dependencies:**
```php
use App\Contracts\Repositories\BaseRepositoryInterface;
use App\Models\Payment\Transaction;
use App\Models\Payment\Invoice;
use App\Enums\PaymentStatus;
use App\Enums\PaymentProvider;
use Illuminate\Support\Collection;
```

**Methods:**
- getTransactionsByUser
- getTransactionsByStatus
- getTransactionsByProvider
- getPendingTransactions
- getCompletedTransactions
- getFailedTransactions
- getRefundedTransactions
- getTransactionsByDateRange
- getUserInvoices
- getPendingInvoices
- getOverdueInvoices
- getPaymentMethods

**Do:**
✅ Filter transactions by status and provider
✅ Support date range queries for reporting
✅ Include currency information in results
✅ Handle payment method security properly
✅ Implement proper transaction logging

**Don't:**
❌ Store sensitive payment data unencrypted
❌ Skip transaction status validation
❌ Allow unauthorized access to payment data
❌ Ignore payment provider responses
❌ Return payment methods without security checks

---

### app/Contracts/Repositories/AffiliateRepositoryInterface.php
**Purpose:** Affiliate repository contract for managing affiliate programs, commissions, and referral tracking.

**Dependencies:**
```php
use App\Contracts\Repositories\BaseRepositoryInterface;
use App\Models\Affiliate\Affiliate;
use App\Models\Affiliate\Commission;
use App\Enums\AffiliateStatus;
use App\Enums\CommissionStatus;
use Illuminate\Support\Collection;
```

**Methods:**
- getAffiliateByUser
- getActiveAffiliates
- getPendingAffiliates
- getSuspendedAffiliates
- getCommissionsByAffiliate
- getPendingCommissions
- getApprovedCommissions
- getPaidCommissions
- getClicksByAffiliate
- getConversionsByAffiliate
- getTopPerformingAffiliates
- getAffiliateStatistics

**Do:**
✅ Track affiliate performance metrics
✅ Filter by affiliate and commission status
✅ Support affiliate analytics queries
✅ Include conversion tracking data
✅ Calculate commission totals accurately

**Don't:**
❌ Allow unauthorized access to affiliate data
❌ Skip commission validation
❌ Ignore affiliate status when processing
❌ Allow duplicate commission entries
❌ Forget to track click-to-conversion rates

---

## 📁 Payment Gateway Contracts

### app/Contracts/Payment/PaymentGatewayInterface.php
**Purpose:** Main payment gateway contract defining standardized interface for all payment providers.

**Dependencies:**
```php
use App\DTOs\Payment\PaymentData;
use App\DTOs\Payment\PaymentRequestInterface;
use App\DTOs\Payment\PaymentResponseInterface;
use Money\Money;
```

**Methods:**
- charge
- refund
- capturePayment
- voidPayment
- createPaymentMethod
- updatePaymentMethod
- deletePaymentMethod
- validateWebhook
- processWebhook
- getTransactionStatus
- getSupportedCurrencies
- calculateFees

**Do:**
✅ Implement consistent error handling across providers
✅ Support all major payment operations
✅ Validate webhook signatures properly
✅ Handle currency conversions correctly
✅ Log all payment gateway interactions

**Don't:**
❌ Skip payment validation steps
❌ Ignore gateway-specific errors
❌ Store sensitive payment credentials insecurely
❌ Process payments without proper authorization
❌ Forget to handle webhook failures

---

### app/Contracts/Payment/StripeGatewayInterface.php
**Purpose:** Stripe-specific payment gateway contract extending base payment interface with Stripe features.

**Dependencies:**
```php
use App\Contracts\Payment\PaymentGatewayInterface;
use Stripe\PaymentIntent;
use Stripe\Customer;
use Stripe\Subscription;
```

**Methods:**
- createPaymentIntent
- confirmPaymentIntent
- createCustomer
- updateCustomer
- createSubscription
- cancelSubscription
- createSetupIntent
- attachPaymentMethod
- handleStripeWebhook
- processInvoicePayment

**Do:**
✅ Use Stripe's latest API version
✅ Handle 3D Secure authentication
✅ Implement proper idempotency
✅ Support subscription billing
✅ Handle Stripe-specific error codes

**Don't:**
❌ Skip Stripe webhook verification
❌ Ignore payment intent status changes
❌ Hard-code Stripe configuration values
❌ Process payments without customer creation
❌ Forget to handle payment method updates

---

### app/Contracts/Payment/BinanceGatewayInterface.php
**Purpose:** Binance-specific payment gateway contract for cryptocurrency payments and trading operations.

**Dependencies:**
```php
use App\Contracts\Payment\PaymentGatewayInterface;
use Binance\API;
use App\Enums\BinanceOrderType;
```

**Methods:**
- createOrder
- getOrderStatus
- cancelOrder
- getAccountInfo
- getExchangeRate
- getCryptoPrices
- validateAddress
- processWithdrawal
- handleBinanceWebhook
- convertCurrency

**Do:**
✅ Validate cryptocurrency addresses
✅ Handle market price fluctuations
✅ Implement proper API rate limiting
✅ Support multiple cryptocurrencies
✅ Handle network confirmations properly

**Don't:**
❌ Skip address validation for crypto payments
❌ Ignore market price changes during payment
❌ Exceed Binance API rate limits
❌ Process crypto payments without confirmations
❌ Store API keys insecurely

---

## 📁 Notification Contracts

### app/Contracts/Notifications/NotificationChannelInterface.php
**Purpose:** Base notification channel contract defining interface for all notification delivery methods.

**Dependencies:**
```php
use Illuminate\Notifications\Notification;
use App\Models\User;
```

**Methods:**
- send
- shouldSend
- formatMessage
- validateRecipient
- handleFailure
- getDeliveryStatus
- retryDelivery

**Do:**
✅ Validate recipients before sending
✅ Format messages for specific channels
✅ Handle delivery failures gracefully
✅ Implement retry logic for failed notifications
✅ Track delivery status

**Don't:**
❌ Send notifications without recipient validation
❌ Ignore channel-specific formatting requirements
❌ Skip delivery failure handling
❌ Allow unlimited retry attempts
❌ Forget to update delivery status

---

### app/Contracts/Notifications/EmailChannelInterface.php
**Purpose:** Email notification channel contract with template support and delivery tracking.

**Dependencies:**
```php
use App\Contracts\Notifications\NotificationChannelInterface;
use Illuminate\Mail\Mailable;
```

**Methods:**
- sendEmail
- sendBulkEmail
- validateEmailAddress
- loadTemplate
- renderTemplate
- trackEmailOpen
- trackEmailClick
- handleBounce
- handleComplaint

**Do:**
✅ Validate email addresses before sending
✅ Use email templates for consistency
✅ Track email delivery and engagement
✅ Handle bounces and complaints properly
✅ Support HTML and plain text formats

**Don't:**
❌ Send emails to invalid addresses
❌ Skip email template validation
❌ Ignore bounce and complaint notifications
❌ Send emails without unsubscribe links
❌ Forget to handle email service failures

---

### app/Contracts/Notifications/SmsChannelInterface.php
**Purpose:** SMS notification channel contract with carrier support and delivery confirmation.

**Dependencies:**
```php
use App\Contracts\Notifications\NotificationChannelInterface;
```

**Methods:**
- sendSms
- validatePhoneNumber
- formatPhoneNumber
- checkDeliveryStatus
- handleDeliveryReport
- getSmsBalance
- estimateCost

**Do:**
✅ Validate phone numbers before sending
✅ Format numbers according to international standards
✅ Track SMS delivery status
✅ Handle delivery reports properly
✅ Monitor SMS service balance

**Don't:**
❌ Send SMS to invalid phone numbers
❌ Skip phone number formatting
❌ Ignore delivery failure reports
❌ Send SMS without proper opt-in
❌ Exceed SMS rate limits

---

## 📁 Cache Strategy Contracts

### app/Contracts/Cache/CacheStrategyInterface.php
**Purpose:** Base cache strategy contract defining caching patterns and invalidation rules.

**Dependencies:**
```php
use Illuminate\Cache\Repository;
use Carbon\Carbon;
```

**Methods:**
- getCacheKey
- getCacheTtl
- shouldCache
- invalidateCache
- warmCache
- getCacheStats
- tagCache

**Do:**
✅ Use meaningful cache keys
✅ Set appropriate TTL values
✅ Implement cache warming strategies
✅ Use cache tags for organized invalidation
✅ Monitor cache performance

**Don't:**
❌ Use overly complex cache keys
❌ Set inappropriate TTL values
❌ Cache sensitive data
❌ Forget to invalidate stale cache
❌ Ignore cache hit/miss ratios

---

### app/Contracts/Cache/UserCacheStrategyInterface.php
**Purpose:** User-specific cache strategy contract for user data, preferences, and session information.

**Dependencies:**
```php
use App\Contracts\Cache\CacheStrategyInterface;
use App\Models\User;
```

**Methods:**
- cacheUserProfile
- cacheUserPreferences
- cacheUserProgress
- cacheUserPermissions
- invalidateUserCache
- getUserCacheKey
- warmUserCache

**Do:**
✅ Cache frequently accessed user data
✅ Invalidate cache on user updates
✅ Respect user privacy in caching
✅ Use user-specific cache keys
✅ Implement cache warming for active users

**Don't:**
❌ Cache sensitive user information
❌ Use global cache keys for user data
❌ Ignore user privacy settings in cache
❌ Set extremely long TTL for user data
❌ Forget to invalidate on permission changes

---

### app/Contracts/Cache/CourseCacheStrategyInterface.php
**Purpose:** Course-specific cache strategy contract for course content, metadata, and user enrollment data.

**Dependencies:**
```php
use App\Contracts\Cache\CacheStrategyInterface;
use App\Models\Course\DocuCourse;
```

**Methods:**
- cacheCourseContent
- cacheCourseMetadata
- cacheUserEnrollments
- cacheCourseStatistics
- invalidateCourseCache
- getCourseCacheKey
- warmCourseCache

**Do:**
✅ Cache course content for performance
✅ Invalidate cache on content updates
✅ Cache course statistics separately
✅ Use hierarchical cache keys
✅ Implement selective cache invalidation

**Don't:**
❌ Cache unpublished course content
❌ Ignore course status in caching
❌ Use overly broad cache invalidation
❌ Cache user-specific course data globally
❌ Forget to update cache on enrollment changes

---

## 📁 File Storage Contracts

### app/Contracts/FileStorage/FileStorageInterface.php
**Purpose:** File storage service contract supporting multiple storage backends and file operations.

**Dependencies:**
```php
use Illuminate\Http\UploadedFile;
use Illuminate\Filesystem\FilesystemAdapter;
use App\Enums\StorageDisk;
```

**Methods:**
- store
- retrieve
- delete
- move
- copy
- exists
- getUrl
- getSize
- getMimeType
- getMetadata
- setVisibility
- getVisibility

**Do:**
✅ Support multiple storage backends
✅ Validate files before storage
✅ Generate secure file URLs
✅ Handle storage failures gracefully
✅ Implement proper file permissions

**Don't:**
❌ Store files without validation
❌ Ignore storage quota limits
❌ Generate predictable file paths
❌ Skip virus scanning for uploads
❌ Allow unlimited file sizes

---

### app/Contracts/FileStorage/ImageOptimizerInterface.php
**Purpose:** Image optimization contract for resizing, compression, and format conversion using Spatie Image.

**Dependencies:**
```php
use Spatie\Image\Image;
use Spatie\Image\Manipulations;
use App\Enums\ImageConversion;
```

**Methods:**
- optimize
- resize
- crop
- watermark
- convertFormat
- generateThumbnails
- compressImage
- getImageInfo
- validateImage

**Do:**
✅ Optimize images automatically on upload
✅ Generate multiple image sizes
✅ Support modern image formats
✅ Maintain aspect ratios during resize
✅ Add watermarks for copyright protection

**Don't:**
❌ Skip image optimization
❌ Generate unnecessary image variants
❌ Ignore image quality settings
❌ Process extremely large images synchronously
❌ Forget to clean up temporary files

---

### app/Contracts/FileStorage/FileValidatorInterface.php
**Purpose:** File validation contract for security checks, type validation, and content scanning.

**Dependencies:**
```php
use Illuminate\Http\UploadedFile;
use App\Enums\FileType;
```

**Methods:**
- validateFileType
- validateFileSize
- validateMimeType
- scanForViruses
- validateFileContent
- checkFileSignature
- validateImageDimensions
- validateVideoDuration
- validateDocumentStructure

**Do:**
✅ Validate file types and sizes
✅ Scan files for malware
✅ Check file signatures for authenticity
✅ Validate file content structure
✅ Reject potentially dangerous files

**Don't:**
❌ Trust client-provided file information
❌ Skip virus scanning
❌ Allow dangerous file types
❌ Ignore file size limits
❌ Process files without validation

---

## 📁 Search Engine Contracts

### app/Contracts/Search/SearchEngineInterface.php
**Purpose:** Search engine contract supporting full-text search with multiple backends (Algolia, Elasticsearch, etc.).

**Dependencies:**
```php
use Laravel\Scout\Builder;
use Illuminate\Database\Eloquent\Collection;
```

**Methods:**
- search
- index
- update
- delete
- flush
- buildQuery
- addFilter
- addSort
- paginate
- highlight
- suggest
- getStats

**Do:**
✅ Support multiple search backends
✅ Implement proper query building
✅ Handle search errors gracefully
✅ Optimize search performance
✅ Track search analytics

**Don't:**
❌ Index sensitive data
❌ Allow unlimited search results
❌ Skip search query sanitization
❌ Ignore search performance metrics
❌ Forget to update index on data changes

---

### app/Contracts/Search/CourseSearchInterface.php
**Purpose:** Course-specific search contract with content filtering, access control, and relevance scoring.

**Dependencies:**
```php
use App\Contracts\Search\SearchEngineInterface;
use App\Models\Course\DocuCourse;
use App\Enums\ContentAccessLevel;
```

**Methods:**
- searchCourses
- filterByCategory
- filterByLevel
- filterByLanguage
- filterByPrice
- sortByRelevance
- sortByPopularity
- sortByDate
- getFacets
- suggestCourses

**Do:**
✅ Filter courses by access level
✅ Implement relevance scoring
✅ Support faceted search
✅ Provide search suggestions
✅ Track course search patterns

**Don't:**
❌ Return restricted courses in search results
❌ Ignore user permission levels
❌ Skip search result ranking
❌ Allow unrestricted course access
❌ Forget to update course index

---

### app/Contracts/Search/UserSearchInterface.php
**Purpose:** User search contract with privacy controls and administrative search capabilities.

**Dependencies:**
```php
use App\Contracts\Search\SearchEngineInterface;
use App\Models\User;
use App\Enums\UserStatus;
```

**Methods:**
- searchUsers
- searchByName
- searchByEmail
- searchByRole
- filterByStatus
- filterByLevel
- respectPrivacySettings
- getPublicUsers
- adminSearch

**Do:**
✅ Respect user privacy settings
✅ Filter by user status and roles
✅ Implement admin search capabilities
✅ Support public user directories
✅ Track user search activity

**Don't:**
❌ Index private user information
❌ Allow unauthorized user searches
❌ Ignore privacy settings
❌ Return inactive users in public search
❌ Skip user consent for search inclusion

---

## 📁 Event System Contracts

### app/Events/Contracts/DomainEventInterface.php
**Purpose:** Domain event contract defining structure for business events in the application.

**Dependencies:**
```php
use Carbon\Carbon;
```

**Methods:**
- getEventName
- getEventData
- getOccurredAt
- getAggregateId
- serialize
- deserialize

**Do:**
✅ Include relevant event data
✅ Add timestamp information
✅ Support event serialization
✅ Include aggregate identifiers
✅ Follow consistent naming conventions

**Don't:**
❌ Include sensitive data in events
❌ Create overly complex event payloads
❌ Forget event versioning
❌ Skip event data validation
❌ Ignore event ordering

---

### app/Events/Contracts/ShouldBroadcastInterface.php
**Purpose:** Broadcasting contract for real-time event distribution via WebSockets or Server-Sent Events.

**Dependencies:**
```php
use Illuminate\Broadcasting\Channel;
```

**Methods:**
- broadcastOn
- broadcastWith
- broadcastAs
- broadcastWhen
- broadcastQueue

**Do:**
✅ Define appropriate broadcast channels
✅ Filter sensitive data before broadcasting
✅ Use conditional broadcasting
✅ Queue resource-intensive broadcasts
✅ Handle broadcast failures

**Don't:**
❌ Broadcast sensitive information
❌ Use overly broad broadcast channels
❌ Skip broadcast authorization
❌ Ignore broadcast failures
❌ Broadcast without proper authentication

---

### app/Events/Contracts/ShouldQueueInterface.php
**Purpose:** Queue contract for asynchronous event processing and background job execution.

**Dependencies:**
```php
use Illuminate\Queue\SerializesModels;
```

**Methods:**
- getQueue
- getConnection
- getDelay
- getTries
- getRetryAfter
- failed

**Do:**
✅ Use appropriate queue connections
✅ Set reasonable retry limits
✅ Handle job failures properly
✅ Implement exponential backoff
✅ Monitor queue performance

**Don't:**
❌ Queue resource-intensive operations on default queue
❌ Set unlimited retry attempts
❌ Ignore failed job handling
❌ Skip job timeout configuration
❌ Forget to handle job failures

---

### app/Events/Contracts/EventHandlerInterface.php
**Purpose:** Event handler contract defining structure for event listeners and subscribers.

**Dependencies:**
```php
use App\Events\Contracts\DomainEventInterface;
```

**Methods:**
- handle
- shouldHandle
- getHandledEvents
- getPriority
- failed

**Do:**
✅ Handle events efficiently
✅ Implement conditional event handling
✅ Set appropriate handler priorities
✅ Handle processing failures
✅ Log event handling activities

**Don't:**
❌ Perform heavy operations in event handlers
❌ Ignore event handling failures
❌ Create circular event dependencies
❌ Skip event handler error logging
❌ Block application flow in handlers

---

## 📁 Job System Contracts

### app/Jobs/Contracts/JobInterface.php
**Purpose:** Base job contract defining structure for all background job implementations.

**Dependencies:**
```php
use Illuminate\Queue\SerializesModels;
use Illuminate\Queue\InteractsWithQueue;
```

**Methods:**
- handle
- failed
- getJobId
- getQueue
- getConnection
- shouldRetry
- getRetryDelay

**Do:**
✅ Implement idempotent job logic
✅ Handle job failures gracefully
✅ Use appropriate retry strategies
✅ Log job execution details
✅ Clean up resources after job completion

**Don't:**
❌ Create non-idempotent jobs
❌ Ignore job failure scenarios
❌ Skip job progress tracking
❌ Forget to handle job timeouts
❌ Leave resources uncleaned

---

### app/Jobs/Contracts/ShouldQueueInterface.php
**Purpose:** Queueable job contract for background processing with queue configuration.

**Dependencies:**
```php
use Illuminate\Contracts\Queue\ShouldQueue;
```

**Methods:**
- getQueue
- getConnection
- getDelay
- getTries
- getTimeout
- getRetryAfter

**Do:**
✅ Configure appropriate queue settings
✅ Set reasonable job timeouts
✅ Implement proper retry logic
✅ Use priority queues when needed
✅ Monitor job execution metrics

**Don't:**
❌ Use default queue for all jobs
❌ Set inappropriate timeout values
❌ Skip retry configuration
❌ Ignore queue monitoring
❌ Forget to handle job cleanup

---

### app/Jobs/Contracts/SynchronousJobInterface.php
**Purpose:** Synchronous job contract for immediate execution without queueing.

**Dependencies:**
```php
use Illuminate\Contracts\Queue\ShouldBeEncrypted;
```

**Methods:**
- handle
- shouldRunSynchronously
- getMaxExecutionTime
- getMemoryLimit
- failed

**Do:**
✅ Keep synchronous jobs lightweight
✅ Set execution time limits
✅ Monitor memory usage
✅ Handle immediate failures
✅ Log synchronous job execution

**Don't:**
❌ Run heavy operations synchronously
❌ Skip execution time monitoring
❌ Ignore memory limitations
❌ Block user requests with sync jobs
❌ Forget to handle sync job failures

---

### app/Jobs/Contracts/JobHandlerInterface.php
**Purpose:** Job handler contract for complex job processing with middleware support.

**Dependencies:**
```php
use App\Jobs\Contracts\JobInterface;
```

**Methods:**
- handle
- middleware
- beforeHandle
- afterHandle
- onFailure
- onSuccess

**Do:**
✅ Use job middleware for cross-cutting concerns
✅ Implement pre and post processing hooks
✅ Handle success and failure scenarios
✅ Support job chaining
✅ Implement job batching where appropriate

**Don't:**
❌ Skip job middleware configuration
❌ Ignore job lifecycle hooks
❌ Create tightly coupled job handlers
❌ Forget to handle job dependencies
❌ Skip job result tracking

---

## 📁 Validation Rule Contracts

### app/Rules/Contracts/RuleInterface.php
**Purpose:** Base validation rule contract for custom validation logic throughout the application.

**Dependencies:**
```php
use Illuminate\Contracts\Validation\Rule;
```

**Methods:**
- passes
- message
- getRuleName
- getParameters
- validate

**Do:**
✅ Implement clear validation logic
✅ Provide descriptive error messages
✅ Support rule parameters
✅ Make rules reusable
✅ Test validation rules thoroughly

**Don't:**
❌ Create overly complex validation rules
❌ Return vague error messages
❌ Skip rule parameter validation
❌ Ignore edge cases in validation
❌ Forget to localize error messages

---

### app/Rules/Contracts/CompositeRule.php
**Purpose:** Composite rule contract for combining multiple validation rules with logical operators.

**Dependencies:**
```php
use App\Rules\Contracts\RuleInterface;
use Illuminate\Support\Collection;
```

**Methods:**
- addRule
- removeRule
- getRules
- combineWith
- passes
- getAllMessages

**Do:**
✅ Support rule composition
✅ Implement logical operators (AND, OR)
✅ Collect all validation messages
✅ Support nested rule combinations
✅ Optimize composite rule execution

**Don't:**
❌ Create circular rule dependencies
❌ Skip rule combination validation
❌ Ignore rule execution order
❌ Forget to aggregate error messages
❌ Create overly nested rule structures

---

### app/Rules/Contracts/ValidatableRule.php
**Purpose:** Advanced validation rule contract with conditional validation and context awareness.

**Dependencies:**
```php
use App\Rules\Contracts\RuleInterface;
use Illuminate\Validation\Validator;
```

**Methods:**
- passes
- when
- unless
- withContext
- withValidator
- shouldValidate

**Do:**
✅ Support conditional validation
✅ Provide context-aware validation
✅ Integrate with Laravel validator
✅ Support validation dependencies
✅ Implement dynamic validation rules

**Don't:**
❌ Ignore validation context
❌ Skip conditional validation logic
❌ Create context-dependent side effects
❌ Forget to handle validation state
❌ Ignore validator integration

---

## 📁 Middleware Contracts

### app/Http/Middleware/Contracts/MiddlewareInterface.php
**Purpose:** Base middleware contract defining structure for all HTTP middleware implementations.

**Dependencies:**
```php
use Illuminate\Http\Request;
use Closure;
```

**Methods:**
- handle
- shouldProcess
- beforeHandle
- afterHandle
- terminate

**Do:**
✅ Keep middleware focused on single responsibility
✅ Support middleware chaining
✅ Implement proper request/response handling
✅ Handle middleware failures gracefully
✅ Log middleware execution when needed

**Don't:**
❌ Perform heavy operations in middleware
❌ Create circular middleware dependencies
❌ Skip error handling in middleware
❌ Ignore middleware execution order
❌ Forget to handle middleware termination

---

### app/Http/Middleware/Contracts/AuthenticatableMiddleware.php
**Purpose:** Authentication middleware contract for user identity verification and session management.

**Dependencies:**
```php
use App\Http\Middleware\Contracts\MiddlewareInterface;
use Illuminate\Auth\AuthManager;
```

**Methods:**
- authenticate
- guard
- redirectPath
- handleUnauthenticated
- validateCredentials

**Do:**
✅ Validate authentication properly
✅ Support multiple auth guards
✅ Handle unauthenticated requests gracefully
✅ Implement proper session management
✅ Log authentication attempts

**Don't:**
❌ Skip authentication validation
❌ Ignore guard configuration
❌ Allow weak authentication
❌ Forget to handle auth failures
❌ Skip authentication logging

---

### app/Http/Middleware/Contracts/AuthorizableMiddleware.php
**Purpose:** Authorization middleware contract for permission and role-based access control using Spatie Permission.

**Dependencies:**
```php
use App\Http\Middleware\Contracts\MiddlewareInterface;
use Spatie\Permission\Models\Role;
use Spatie\Permission\Models\Permission;
```

**Methods:**
- authorize
- hasRole
- hasPermission
- hasAnyRole
- hasAllRoles
- handleUnauthorized

**Do:**
✅ Check user permissions before access
✅ Support role-based authorization
✅ Implement granular permission checks
✅ Handle unauthorized access properly
✅ Log authorization failures

**Don't:**
❌ Skip permission validation
❌ Allow unauthorized access
❌ Ignore role hierarchies
❌ Forget to handle authorization errors
❌ Skip authorization audit logging

---

### app/Http/Middleware/Contracts/ValidatableMiddleware.php
**Purpose:** Validation middleware contract for request data validation and sanitization.

**Dependencies:**
```php
use App\Http\Middleware\Contracts\MiddlewareInterface;
use Illuminate\Validation\Factory;
```

**Methods:**
- validate
- getRules
- getMessages
- sanitize
- handleValidationFailure

**Do:**
✅ Validate request data early
✅ Sanitize input data
✅ Provide clear validation messages
✅ Handle validation failures properly
✅ Support custom validation rules

**Don't:**
❌ Skip input validation
❌ Allow unsanitized data through
❌ Ignore validation failures
❌ Return generic error messages
❌ Forget to validate all inputs

---

### app/Http/Middleware/Contracts/RateLimitableMiddleware.php
**Purpose:** Rate limiting middleware contract for API and request throttling with Redis support.

**Dependencies:**
```php
use App\Http\Middleware\Contracts\MiddlewareInterface;
use Illuminate\Cache\RateLimiter;
```

**Methods:**
- checkRateLimit
- getRateLimitKey
- getRateLimit
- handleRateLimitExceeded
- resetRateLimit

**Do:**
✅ Implement proper rate limiting
✅ Use appropriate limit keys
✅ Handle limit exceeded scenarios
✅ Provide rate limit headers
✅ Monitor rate limit usage

**Don't:**
❌ Skip rate limiting for APIs
❌ Use overly restrictive limits
❌ Ignore rate limit headers
❌ Forget to handle limit resets
❌ Skip rate limit monitoring

---

## Summary

The Contracts component provides a comprehensive abstraction layer for the Laravel docu-course platform, ensuring loose coupling between application layers and enabling testability through dependency injection. Each contract defines clear interfaces for services, repositories, payment gateways, and other critical system components.

**Key Benefits:**
- **Loose Coupling:** Interfaces separate implementation from contract
- **Testability:** Easy mocking and testing of dependencies
- **Flexibility:** Multiple implementations of the same contract
- **Consistency:** Standardized method signatures across services
- **Maintainability:** Clear separation of concerns and responsibilities

**Integration Points:**
- Service Layer: All services implement corresponding interfaces
- Repository Layer: Consistent data access patterns
- Payment Processing: Standardized gateway interfaces
- Caching Strategy: Organized cache management
- Event System: Structured event handling
- Validation: Reusable validation rules
- Middleware: Standardized request processing

These contracts form the foundation for a robust, scalable, and maintainable Laravel application architecture.