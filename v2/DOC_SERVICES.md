# DOC_SERVICES.md

## Services Component - Production File Documentation

This component contains all business logic services organized by domain modules, providing core functionality for user management, course operations, payment processing, affiliate programs, media handling, system operations, and more.

---

### File: 2030b/app/Services/User/UserService.php

**Purpose**: Core user management service handling user operations, level progression, account management, and user lifecycle operations within the docu-course platform.

**Dependencies**:
```php
use App\Contracts\Services\UserServiceInterface;
use App\Contracts\Repositories\UserRepositoryInterface;
use App\DTOs\User\ProfileData;
use App\DTOs\User\ProgressData;
use App\Models\User;
use App\Models\UserProfile;
use App\Enums\UserStatus;
use App\Enums\UserLevel;
use App\Events\UserLevelChanged;
use App\Events\UserRegistered;
use App\Events\UserUpdated;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Auth;
use Spatie\Activitylog\Traits\LogsActivity;
use Spatie\Permission\Traits\HasRoles;
```

**Methods**:
- createUser(ProfileData $profileData): User
- updateProfile(int $userId, ProfileData $profileData): User
- upgradeUserLevel(int $userId): bool
- calculateUserLevel(int $points): int
- addUserPoints(int $userId, int $points, string $source): bool
- deductUserPoints(int $userId, int $points, string $reason): bool
- getUserStats(int $userId): array
- activateUser(int $userId): bool
- deactivateUser(int $userId, string $reason): bool
- resetPassword(string $email): bool
- changePassword(int $userId, string $newPassword): bool
- getUserAchievements(int $userId): array
- awardBadge(int $userId, string $badgeType): bool
- getReferralRewards(int $userId): array
- syncUserPermissions(int $userId, array $permissions): bool
- getUserActivity(int $userId, int $limit = 50): array

**Do**:
- Use database transactions for complex operations
- Fire events for user state changes
- Cache user statistics and levels
- Validate user permissions before operations
- Log important user activities with Spatie ActivityLog
- Handle user progression automatically
- Implement proper password security with Hash facade
- Use repository pattern for data access

**Don't**:
- Don't expose sensitive user data in responses
- Don't bypass user status validation
- Don't ignore user privacy settings
- Don't perform operations without authorization
- Don't cache sensitive information like passwords
- Don't allow invalid level transitions
- Don't skip activity logging for important actions

---

### File: 2030b/app/Services/User/AuthService.php

**Purpose**: Handles authentication operations including login, registration, password reset, two-factor authentication, and session management using Laravel Sanctum and Breeze.

**Dependencies**:
```php
use App\Contracts\Services\AuthServiceInterface;
use App\DTOs\Auth\LoginData;
use App\DTOs\Auth\RegisterData;
use App\DTOs\Auth\ResetPasswordData;
use App\Models\User;
use App\Enums\UserStatus;
use App\Events\UserLoggedIn;
use App\Events\UserLoggedOut;
use App\Events\UserRegistered;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Hash;
use Laravel\Sanctum\PersonalAccessToken;
use PragmaRX\Google2FA\Google2FA;
use Spatie\Activitylog\Facades\CauserResolver;
```

**Methods**:
- login(LoginData $loginData): array
- register(RegisterData $registerData): User
- logout(): bool
- refreshToken(): string
- resetPassword(ResetPasswordData $resetData): bool
- sendResetPasswordLink(string $email): bool
- verifyEmail(string $token): bool
- enable2FA(int $userId): array
- disable2FA(int $userId): bool
- verify2FA(int $userId, string $code): bool
- createApiToken(int $userId, string $tokenName): string
- revokeApiToken(string $tokenId): bool
- getUserSessions(int $userId): array
- terminateSession(string $sessionId): bool
- checkAccountLockout(string $email): bool
- recordLoginAttempt(string $email, bool $successful): void

**Do**:
- Use Laravel Sanctum for API authentication
- Implement rate limiting for login attempts
- Log authentication events for security auditing
- Validate user status before authentication
- Use secure password hashing with Hash facade
- Implement proper 2FA with Google2FA package
- Handle token expiration gracefully
- Use CSRF protection for web routes

**Don't**:
- Don't store passwords in plain text
- Don't allow authentication of inactive users
- Don't skip rate limiting on sensitive operations
- Don't expose internal authentication details
- Don't allow weak passwords
- Don't bypass email verification when required
- Don't ignore failed login attempts

---

### File: 2030b/app/Services/User/ProfileService.php

**Purpose**: Manages user profile operations including profile updates, avatar management, preferences, and personal information using Spatie MediaLibrary for file handling.

**Dependencies**:
```php
use App\Contracts\Services\ProfileServiceInterface;
use App\DTOs\User\ProfileData;
use App\DTOs\User\PreferencesData;
use App\Models\User;
use App\Models\UserProfile;
use App\Enums\Gender;
use App\Enums\TextDirection;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Intervention\Image\Facades\Image;
use Illuminate\Http\UploadedFile;
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Facades\Cache;
```

**Methods**:
- updateProfile(int $userId, ProfileData $profileData): UserProfile
- uploadAvatar(int $userId, UploadedFile $file): Media
- removeAvatar(int $userId): bool
- updatePreferences(int $userId, PreferencesData $preferences): bool
- getProfileCompleteness(int $userId): int
- updateLanguagePreference(int $userId, string $language): bool
- updateTimezone(int $userId, string $timezone): bool
- updateNotificationSettings(int $userId, array $settings): bool
- getProfileHistory(int $userId): array
- exportProfileData(int $userId): array
- validateProfileData(array $data): bool
- optimizeProfileImage(Media $media): void
- generateProfileUrl(int $userId): string

**Do**:
- Use Spatie MediaLibrary for file uploads
- Optimize images with Intervention Image
- Validate file types and sizes
- Cache profile data for performance
- Track profile completion percentage
- Log profile changes for audit trail
- Implement proper file naming conventions
- Use cloud storage for scalability

**Don't**:
- Don't accept unauthorized file types
- Don't skip image optimization
- Don't store large files locally
- Don't expose direct file paths
- Don't allow profile updates without validation
- Don't ignore storage quotas
- Don't cache sensitive personal data

---

### File: 2030b/app/Services/User/ProgressService.php

**Purpose**: Tracks and manages user progress through courses, lessons, achievements, and learning analytics including completion rates and skill assessments.

**Dependencies**:
```php
use App\Contracts\Services\ProgressServiceInterface;
use App\DTOs\User\ProgressData;
use App\Models\User;
use App\Models\UserProgress;
use App\Models\DocuCourse;
use App\Models\Episode;
use App\Enums\ProgressStatus;
use App\Enums\CompletionType;
use App\Events\ProgressUpdated;
use App\Events\CourseCompleted;
use App\Events\AchievementUnlocked;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\DB;
```

**Methods**:
- updateProgress(int $userId, int $episodeId, ProgressData $progressData): UserProgress
- markEpisodeComplete(int $userId, int $episodeId): bool
- markCourseComplete(int $userId, int $courseId): bool
- calculateCourseProgress(int $userId, int $courseId): float
- getUserCompletionRate(int $userId): float
- getProgressStats(int $userId): array
- resetProgress(int $userId, int $courseId): bool
- getRecommendedContent(int $userId): array
- trackTimeSpent(int $userId, int $episodeId, int $seconds): void
- getStudyStreak(int $userId): int
- updateStudyStreak(int $userId): void
- calculateSkillLevel(int $userId, string $skill): int
- awardAchievement(int $userId, string $achievementType): bool
- getProgressReport(int $userId, array $filters = []): array

**Do**:
- Track granular progress data for analytics
- Fire events for progress milestones
- Cache progress calculations for performance
- Use database transactions for consistency
- Calculate completion rates accurately
- Award achievements automatically
- Provide detailed progress reports
- Track time spent learning

**Don't**:
- Don't allow progress manipulation
- Don't skip validation of progress data
- Don't ignore completion prerequisites
- Don't cache stale progress information
- Don't allow negative progress values
- Don't bypass achievement criteria
- Don't lose progress data during updates

---

### File: 2030b/app/Services/User/LevelService.php

**Purpose**: Manages user level system including experience points, level calculations, rewards distribution, and level-based access control throughout the platform.

**Dependencies**:
```php
use App\Contracts\Services\LevelServiceInterface;
use App\Models\User;
use App\Models\UserLevel;
use App\Enums\UserLevel as UserLevelEnum;
use App\Enums\PointSource;
use App\Events\UserLevelChanged;
use App\Events\RewardEarned;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Config;
```

**Methods**:
- calculateLevel(int $points): UserLevelEnum
- addPoints(int $userId, int $points, PointSource $source, string $description = null): bool
- deductPoints(int $userId, int $points, string $reason): bool
- getCurrentLevel(int $userId): UserLevelEnum
- getPointsToNextLevel(int $userId): int
- getLevelRequirements(UserLevelEnum $level): array
- processLevelUp(int $userId): bool
- getLevelRewards(UserLevelEnum $level): array
- distributeRewards(int $userId, UserLevelEnum $level): void
- getPointsHistory(int $userId, int $limit = 100): array
- getLevelProgressPercentage(int $userId): float
- checkLevelAccess(int $userId, string $feature): bool
- resetUserLevel(int $userId): bool
- getLevelLeaderboard(int $limit = 50): array

**Do**:
- Use enum for level definitions
- Cache level calculations for performance
- Fire events for level changes
- Track point sources for transparency
- Implement fair progression curves
- Validate point transactions
- Provide clear level requirements
- Use database transactions for point changes

**Don't**:
- Don't allow negative point balances
- Don't skip level progression validation
- Don't manipulate levels without proper checks
- Don't ignore reward distribution
- Don't cache sensitive point data indefinitely
- Don't allow point duplication
- Don't bypass access control checks

---

### File: 2030b/app/Services/Course/CourseManagementService.php

**Purpose**: Comprehensive course management service handling course creation, updates, publishing, categorization, and lifecycle management for the docu-course platform.

**Dependencies**:
```php
use App\Contracts\Services\CourseManagementServiceInterface;
use App\DTOs\Course\CourseData;
use App\DTOs\Course\CourseUpdateData;
use App\Models\DocuCourse;
use App\Models\CourseCategory;
use App\Models\User;
use App\Enums\CourseStatus;
use App\Enums\ContentAccessLevel;
use App\Events\CourseCreated;
use App\Events\CoursePublished;
use App\Events\CourseUpdated;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Spatie\Sluggable\HasSlug;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Cache;
```

**Methods**:
- createCourse(CourseData $courseData, int $instructorId): DocuCourse
- updateCourse(int $courseId, CourseUpdateData $updateData): DocuCourse
- publishCourse(int $courseId): bool
- unpublishCourse(int $courseId, string $reason): bool
- deleteCourse(int $courseId): bool
- duplicateCourse(int $courseId, int $newInstructorId): DocuCourse
- assignInstructor(int $courseId, int $instructorId): bool
- setCourseCategories(int $courseId, array $categoryIds): bool
- uploadCourseThumbnail(int $courseId, UploadedFile $file): Media
- generateCourseSlug(string $title): string
- validateCourseAccess(int $courseId, int $userId): bool
- getCourseStatistics(int $courseId): array
- archiveCourse(int $courseId): bool
- restoreCourse(int $courseId): bool
- bulkUpdateCourses(array $courseIds, array $updates): int

**Do**:
- Use database transactions for complex operations
- Fire events for important course lifecycle changes
- Validate instructor permissions before operations
- Generate SEO-friendly slugs automatically
- Cache course statistics for performance
- Use Spatie MediaLibrary for file handling
- Implement soft deletes for data recovery
- Track course modification history

**Don't**:
- Don't allow unauthorized course modifications
- Don't skip content validation before publishing
- Don't expose draft courses publicly
- Don't delete courses with active enrollments
- Don't ignore course access level restrictions
- Don't bypass instructor assignment validation
- Don't cache sensitive course data

---

### File: 2030b/app/Services/Course/ContentService.php

**Purpose**: Manages course content operations including lesson creation, content versioning, media attachments, content optimization, and content delivery management.

**Dependencies**:
```php
use App\Contracts\Services\ContentServiceInterface;
use App\DTOs\Course\ContentData;
use App\Models\Episode;
use App\Models\DocuCourse;
use App\Enums\ContentType;
use App\Enums\ContentStatus;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use League\HTMLToMarkdown\HtmlConverter;
use Spatie\LaravelMarkdown\MarkdownRenderer;
use Intervention\Image\Facades\Image;
use Illuminate\Http\UploadedFile;
use Illuminate\Support\Facades\Storage;
```

**Methods**:
- createContent(int $courseId, ContentData $contentData): Episode
- updateContent(int $episodeId, ContentData $contentData): Episode
- deleteContent(int $episodeId): bool
- attachMedia(int $episodeId, UploadedFile $file, string $type): Media
- removeMedia(int $episodeId, int $mediaId): bool
- convertHtmlToMarkdown(string $html): string
- convertMarkdownToHtml(string $markdown): string
- optimizeContentMedia(Media $media): void
- generateContentPreview(int $episodeId): string
- validateContentStructure(array $content): bool
- reorderContent(int $courseId, array $episodeOrder): bool
- duplicateContent(int $episodeId, int $targetCourseId): Episode
- exportContent(int $courseId, string $format): string
- importContent(int $courseId, UploadedFile $file): array
- generateContentToc(int $courseId): array

**Do**:
- Use League HTML to Markdown for content conversion
- Optimize media files with Intervention Image
- Validate content structure before saving
- Support multiple content formats (HTML, Markdown)
- Implement content versioning for changes
- Use cloud storage for media files
- Generate table of contents automatically
- Validate file types and sizes

**Don't**:
- Don't accept malicious file uploads
- Don't skip content sanitization
- Don't ignore media optimization
- Don't lose content during conversions
- Don't allow oversized file uploads
- Don't expose direct file URLs
- Don't skip content validation

---

### File: 2030b/app/Services/Course/EnrollmentService.php

**Purpose**: Handles course enrollment operations including registration, access control, enrollment tracking, refunds, and enrollment analytics for learners and instructors.

**Dependencies**:
```php
use App\Contracts\Services\EnrollmentServiceInterface;
use App\DTOs\Course\EnrollmentData;
use App\Models\CourseEnrollment;
use App\Models\DocuCourse;
use App\Models\User;
use App\Enums\EnrollmentStatus;
use App\Enums\EnrollmentType;
use App\Events\UserEnrolled;
use App\Events\EnrollmentCancelled;
use App\Services\Payment\PaymentService;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Cache;
use Money\Money;
```

**Methods**:
- enrollUser(EnrollmentData $enrollmentData): CourseEnrollment
- cancelEnrollment(int $enrollmentId, string $reason): bool
- refundEnrollment(int $enrollmentId): bool
- checkEnrollmentAccess(int $userId, int $courseId): bool
- getEnrollmentStatus(int $userId, int $courseId): ?EnrollmentStatus
- bulkEnroll(array $userIds, int $courseId, EnrollmentType $type): array
- transferEnrollment(int $enrollmentId, int $newUserId): bool
- extendEnrollmentAccess(int $enrollmentId, int $days): bool
- getEnrollmentStatistics(int $courseId): array
- getUserEnrollments(int $userId, array $filters = []): array
- processEnrollmentPayment(int $enrollmentId, Money $amount): bool
- generateEnrollmentCertificate(int $enrollmentId): string
- validateEnrollmentEligibility(int $userId, int $courseId): bool
- getEnrollmentHistory(int $courseId): array

**Do**:
- Use database transactions for enrollment operations
- Fire events for enrollment state changes
- Integrate with payment service for paid courses
- Validate enrollment eligibility before processing
- Track enrollment analytics for reporting
- Handle refunds through payment service
- Generate certificates upon completion
- Cache enrollment status for performance

**Don't**:
- Don't allow duplicate enrollments
- Don't bypass payment verification
- Don't ignore enrollment prerequisites
- Don't process refunds without validation
- Don't expose sensitive enrollment data
- Don't allow enrollment in archived courses
- Don't skip access level verification

---

### File: 2030b/app/Services/Course/EpisodeService.php

**Purpose**: Manages individual episode operations within courses including episode creation, ordering, content management, progress tracking, and episode-specific analytics.

**Dependencies**:
```php
use App\Contracts\Services\EpisodeServiceInterface;
use App\DTOs\Course\EpisodeData;
use App\Models\Episode;
use App\Models\DocuCourse;
use App\Models\UserProgress;
use App\Enums\EpisodeStatus;
use App\Enums\EpisodeType;
use App\Events\EpisodeCreated;
use App\Events\EpisodeCompleted;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Illuminate\Support\Facades\Cache;
```

**Methods**:
- createEpisode(int $courseId, EpisodeData $episodeData): Episode
- updateEpisode(int $episodeId, EpisodeData $episodeData): Episode
- deleteEpisode(int $episodeId): bool
- reorderEpisodes(int $courseId, array $episodeOrder): bool
- duplicateEpisode(int $episodeId, int $targetCourseId): Episode
- getEpisodeProgress(int $episodeId, int $userId): ?UserProgress
- markEpisodeComplete(int $episodeId, int $userId, array $data = []): bool
- getNextEpisode(int $episodeId): ?Episode
- getPreviousEpisode(int $episodeId): ?Episode
- getEpisodeStatistics(int $episodeId): array
- validateEpisodeAccess(int $episodeId, int $userId): bool
- attachEpisodeMedia(int $episodeId, UploadedFile $file): Media
- getEpisodeTranscript(int $episodeId): ?string
- updateEpisodeTranscript(int $episodeId, string $transcript): bool
- calculateEpisodeDuration(int $episodeId): int

**Do**:
- Maintain proper episode ordering within courses
- Track user progress for each episode
- Fire events for episode lifecycle changes
- Cache episode statistics for performance
- Validate episode access permissions
- Support multiple episode types (video, text, quiz)
- Generate transcripts for accessibility
- Calculate accurate episode durations

**Don't**:
- Don't allow episodes without parent courses
- Don't skip progress tracking updates
- Don't expose episodes to unauthorized users
- Don't ignore episode ordering constraints
- Don't cache sensitive episode data
- Don't allow invalid episode transitions
- Don't skip episode completion validation

---

### File: 2030b/app/Services/Course/SearchService.php

**Purpose**: Provides comprehensive search functionality for courses using Laravel Scout with advanced filtering, faceted search, and search analytics integration.

**Dependencies**:
```php
use App\Contracts\Services\SearchServiceInterface;
use App\Models\DocuCourse;
use App\Models\Episode;
use App\Enums\CourseStatus;
use App\Enums\ContentAccessLevel;
use Laravel\Scout\Searchable;
use Spatie\LaravelSearchable\Search;
use Spatie\QueryBuilder\QueryBuilder;
use Illuminate\Support\Facades\Cache;
use Illuminate\Database\Eloquent\Collection;
```

**Methods**:
- searchCourses(string $query, array $filters = []): Collection
- searchEpisodes(string $query, array $filters = []): Collection
- getSearchSuggestions(string $partial): array
- getPopularSearchTerms(int $limit = 20): array
- getFacetedResults(string $query, array $facets): array
- advancedSearch(array $criteria): Collection
- searchByInstructor(string $instructorName): Collection
- searchByCategory(int $categoryId, string $query = ''): Collection
- trackSearchQuery(string $query, int $resultsCount, int $userId = null): void
- getSearchAnalytics(array $filters = []): array
- buildSearchIndex(string $model): bool
- optimizeSearchIndex(): bool
- getRelatedCourses(int $courseId, int $limit = 5): Collection
- filterCoursesByAccess(Collection $courses, int $userId): Collection

**Do**:
- Use Laravel Scout for full-text search capabilities
- Implement faceted search for better filtering
- Cache popular search results for performance
- Track search analytics for optimization
- Support advanced search criteria
- Filter results based on user access levels
- Provide search suggestions for better UX
- Optimize search indices regularly

**Don't**:
- Don't expose private courses in search results
- Don't ignore search query sanitization
- Don't cache search results indefinitely
- Don't skip search analytics tracking
- Don't allow search without proper filtering
- Don't expose sensitive course information
- Don't ignore search performance optimization

---

### File: 2030b/app/Services/Payment/PaymentService.php

**Purpose**: Core payment processing service orchestrating multiple payment providers including Stripe and Binance, handling transactions, refunds, and payment analytics with multi-currency support.

**Dependencies**:
```php
use App\Contracts\Services\PaymentServiceInterface;
use App\Services\Payment\StripeService;
use App\Services\Payment\BinanceService;
use App\Services\Payment\CurrencyService;
use App\DTOs\Payment\PaymentData;
use App\DTOs\Payment\RefundData;
use App\Models\Payment;
use App\Models\PaymentMethod;
use App\Enums\PaymentStatus;
use App\Enums\PaymentProvider;
use App\Events\PaymentProcessed;
use App\Events\RefundProcessed;
use Money\Money;
use Money\Currency;
use Illuminate\Support\Facades\DB;
```

**Methods**:
- processPayment(PaymentData $paymentData): Payment
- processRefund(RefundData $refundData): bool
- getPaymentStatus(string $paymentId): PaymentStatus
- getSupportedCurrencies(): array
- convertCurrency(Money $amount, Currency $targetCurrency): Money
- validatePaymentMethod(int $paymentMethodId, int $userId): bool
- calculateFees(Money $amount, PaymentProvider $provider): Money
- getPaymentHistory(int $userId, array $filters = []): array
- processRecurringPayment(int $paymentMethodId, Money $amount): Payment
- handlePaymentWebhook(string $provider, array $payload): bool
- getPaymentAnalytics(array $filters = []): array
- retryFailedPayment(int $paymentId): Payment
- cancelPendingPayment(int $paymentId): bool
- getPaymentMethods(int $userId): array

**Do**:
- Use Money objects for precise currency calculations
- Support multiple payment providers (Stripe, Binance)
- Handle webhooks for payment confirmations
- Calculate accurate fees and taxes
- Use database transactions for payment operations
- Fire events for payment state changes
- Implement proper error handling and retries
- Track payment analytics for reporting

**Don't**:
- Don't store sensitive payment data locally
- Don't process payments without validation
- Don't ignore webhook security verification
- Don't skip currency conversion validation
- Don't bypass payment method verification
- Don't cache payment credentials
- Don't ignore PCI compliance requirements

---

### File: 2030b/app/Services/Payment/StripeService.php

**Purpose**: Stripe payment provider integration handling credit card payments, subscriptions, customer management, and Stripe-specific webhook processing using the Stripe PHP SDK.

**Dependencies**:
```php
use App\Contracts\Services\StripeServiceInterface;
use App\DTOs\Payment\StripePaymentData;
use App\Models\Payment;
use App\Models\PaymentMethod;
use Stripe\StripeClient;
use Stripe\Customer;
use Stripe\PaymentIntent;
use Stripe\Subscription;
use Stripe\WebhookEndpoint;
use Money\Money;
use Illuminate\Support\Facades\Config;
use Illuminate\Support\Facades\Log;
```

**Methods**:
- createPaymentIntent(Money $amount, string $currency, array $metadata = []): PaymentIntent
- confirmPayment(string $paymentIntentId): PaymentIntent
- createCustomer(int $userId, array $customerData): Customer
- attachPaymentMethod(string $paymentMethodId, string $customerId): bool
- createSubscription(string $customerId, string $priceId): Subscription
- cancelSubscription(string $subscriptionId): Subscription
- processRefund(string $paymentIntentId, Money $amount = null): bool
- handleWebhook(string $payload, string $signature): array
- getCustomerPaymentMethods(string $customerId): array
- updateCustomer(string $customerId, array $data): Customer
- retrievePaymentIntent(string $paymentIntentId): PaymentIntent
- calculateApplicationFee(Money $amount): Money
- createConnectedAccount(array $accountData): array
- processTransfer(string $destinationAccount, Money $amount): bool

**Do**:
- Use Stripe PHP SDK for all API interactions
- Implement proper webhook signature verification
- Handle Stripe exceptions gracefully
- Use idempotency keys for payment operations
- Store Stripe customer IDs for future transactions
- Implement proper error logging
- Follow Stripe best practices for security
- Handle 3D Secure authentication flows

**Don't**:
- Don't store Stripe secret keys in code
- Don't ignore webhook signature validation
- Don't process payments without customer verification
- Don't skip error handling for API calls
- Don't expose Stripe internal errors to users
- Don't bypass Stripe compliance requirements
- Don't ignore payment confirmation flows

---

### File: 2030b/app/Services/Payment/BinanceService.php

**Purpose**: Binance payment integration for cryptocurrency transactions, handling Bitcoin, Ethereum, and other crypto payments using the Binance Connector PHP SDK.

**Dependencies**:
```php
use App\Contracts\Services\BinanceServiceInterface;
use App\DTOs\Payment\CryptoPaymentData;
use App\Models\CryptoPayment;
use Binance\Spot;
use Money\Money;
use Money\Currency;
use Illuminate\Support\Facades\Config;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Facades\Cache;
```

**Methods**:
- createCryptoPayment(CryptoPaymentData $paymentData): CryptoPayment
- checkPaymentStatus(string $transactionHash): string
- getCryptoCurrencies(): array
- convertFiatToCrypto(Money $fiatAmount, string $cryptoCurrency): array
- generatePaymentAddress(string $currency): string
- validateTransaction(string $transactionHash, string $expectedAmount): bool
- getTransactionDetails(string $transactionHash): array
- processWebhook(array $payload): bool
- getExchangeRates(array $symbols): array
- calculateNetworkFees(string $currency, Money $amount): Money
- getWalletBalance(string $currency): Money
- createWithdrawal(string $currency, Money $amount, string $address): array
- getTransactionHistory(array $filters = []): array
- validateWalletAddress(string $address, string $currency): bool

**Do**:
- Use Binance Connector PHP for API interactions
- Validate cryptocurrency addresses before transactions
- Cache exchange rates for performance
- Handle network confirmations properly
- Calculate accurate network fees
- Implement proper transaction monitoring
- Use secure API key management
- Handle blockchain confirmation delays

**Don't**:
- Don't store private keys or mnemonics
- Don't ignore transaction confirmations
- Don't skip address validation
- Don't bypass security measures
- Don't cache sensitive wallet data
- Don't ignore network fee calculations
- Don't process unconfirmed transactions

---

### File: 2030b/app/Services/Payment/CurrencyService.php

**Purpose**: Multi-currency support service handling currency conversion, exchange rates, localization, and currency formatting using Money PHP and Exchanger libraries.

**Dependencies**:
```php
use App\Contracts\Services\CurrencyServiceInterface;
use Money\Money;
use Money\Currency;
use Money\Formatter\LocalizedFormatter;
use Money\Currencies\ISOCurrencies;
use Exchanger\ExchangeRateProvider;
use Florianv\Exchanger\Exchanger;
use Akaunting\Money\Money as LaravelMoney;
use Illuminate\Support\Facades\Config;
use Illuminate\Support\Facades\Cache;
```

**Methods**:
- convertCurrency(Money $amount, Currency $targetCurrency): Money
- getExchangeRate(Currency $baseCurrency, Currency $targetCurrency): float
- getSupportedCurrencies(): array
- formatMoney(Money $amount, string $locale = null): string
- parseMoney(string $amount, Currency $currency): Money
- getCurrencySymbol(Currency $currency): string
- getDefaultCurrency(): Currency
- setUserCurrency(int $userId, Currency $currency): bool
- getUserCurrency(int $userId): Currency
- updateExchangeRates(): bool
- getCachedExchangeRate(Currency $base, Currency $target): ?float
- calculateTaxAmount(Money $amount, float $taxRate, Currency $currency): Money
- roundAmount(Money $amount, string $roundingMode = 'half_up'): Money
- isZeroAmount(Money $amount): bool

**Do**:
- Use Money objects for all currency calculations
- Cache exchange rates for performance
- Support multiple currency providers
- Handle currency formatting by locale
- Implement proper rounding strategies
- Use ISO currency codes consistently
- Update exchange rates regularly
- Validate currency inputs

**Don't**:
- Don't use floating point for money calculations
- Don't ignore currency conversion errors
- Don't cache exchange rates indefinitely
- Don't skip currency validation
- Don't ignore rounding precision
- Don't assume single currency operations
- Don't bypass currency formatting rules

---

### File: 2030b/app/Services/Payment/TransactionService.php

**Purpose**: Transaction management service handling payment tracking, transaction history, reconciliation, dispute management, and financial reporting with audit trails.

**Dependencies**:
```php
use App\Contracts\Services\TransactionServiceInterface;
use App\DTOs\Payment\TransactionData;
use App\Models\Transaction;
use App\Models\Payment;
use App\Enums\TransactionStatus;
use App\Enums\TransactionType;
use App\Events\TransactionCreated;
use App\Events\TransactionUpdated;
use Money\Money;
use Illuminate\Support\Facades\DB;
use Spatie\Activitylog\Traits\LogsActivity;
```

**Methods**:
- createTransaction(TransactionData $transactionData): Transaction
- updateTransactionStatus(int $transactionId, TransactionStatus $status): bool
- getTransactionHistory(int $userId, array $filters = []): array
- reconcileTransactions(array $transactionIds): array
- processDispute(int $transactionId, array $disputeData): bool
- calculateDailyRevenue(string $date): Money
- getRevenueReport(string $startDate, string $endDate): array
- getTransactionsByStatus(TransactionStatus $status): array
- reverseTransaction(int $transactionId, string $reason): Transaction
- getTransactionDetails(int $transactionId): Transaction
- bulkUpdateTransactions(array $transactionIds, array $updates): int
- exportTransactions(array $filters, string $format): string
- getTransactionStatistics(array $filters = []): array
- validateTransactionIntegrity(int $transactionId): bool

**Do**:
- Use database transactions for financial operations
- Maintain comprehensive audit trails
- Fire events for transaction state changes
- Implement proper reconciliation processes
- Track all financial activities
- Use Money objects for calculations
- Validate transaction integrity regularly
- Support transaction reversal workflows

**Don't**:
- Don't modify transactions without audit trails
- Don't ignore transaction validation
- Don't skip reconciliation processes
- Don't lose transaction history
- Don't bypass dispute handling procedures
- Don't ignore transaction status updates
- Don't cache sensitive financial data

---

### File: 2030b/app/Services/Payment/InvoiceService.php

**Purpose**: Invoice generation and management service handling invoice creation, PDF generation, payment tracking, and invoice automation using DomPDF and mPDF libraries.

**Dependencies**:
```php
use App\Contracts\Services\InvoiceServiceInterface;
use App\DTOs\Payment\InvoiceData;
use App\Models\Invoice;
use App\Models\InvoiceItem;
use App\Models\Payment;
use App\Enums\InvoiceStatus;
use Barryvdh\DomPDF\Facade\Pdf;
use Mpdf\Mpdf;
use Money\Money;
use Illuminate\Support\Facades\Storage;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
```

**Methods**:
- createInvoice(InvoiceData $invoiceData): Invoice
- generateInvoicePdf(int $invoiceId): Media
- markInvoicePaid(int $invoiceId, int $paymentId): bool
- sendInvoiceEmail(int $invoiceId): bool
- getInvoiceTemplate(string $type): string
- calculateInvoiceTotal(array $items, float $taxRate = 0): Money
- addInvoiceItem(int $invoiceId, array $itemData): InvoiceItem
- updateInvoiceItem(int $itemId, array $itemData): InvoiceItem
- removeInvoiceItem(int $itemId): bool
- getOverdueInvoices(): array
- sendPaymentReminder(int $invoiceId): bool
- voidInvoice(int $invoiceId, string $reason): bool
- duplicateInvoice(int $invoiceId): Invoice
- getInvoiceStatistics(array $filters = []): array

**Do**:
- Generate professional PDF invoices
- Support multiple invoice templates
- Track invoice payment status
- Send automated payment reminders
- Calculate taxes accurately
- Use proper invoice numbering
- Store invoices securely
- Maintain invoice history

**Don't**:
- Don't generate invoices without validation
- Don't skip tax calculations
- Don't ignore payment tracking
- Don't lose invoice data
- Don't bypass invoice approval workflows
- Don't ignore overdue invoice handling
- Don't skip invoice numbering sequence

---

### File: 2030b/app/Services/Affiliate/AffiliateService.php

**Purpose**: Core affiliate program management service handling affiliate registration, tracking, commission calculations, and affiliate relationship management with multi-tier support.

**Dependencies**:
```php
use App\Contracts\Services\AffiliateServiceInterface;
use App\DTOs\Affiliate\AffiliateData;
use App\Models\Affiliate;
use App\Models\User;
use App\Enums\AffiliateStatus;
use App\Enums\AffiliateType;
use App\Events\AffiliateRegistered;
use App\Events\AffiliateStatusChanged;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Str;
```

**Methods**:
- registerAffiliate(AffiliateData $affiliateData): Affiliate
- approveAffiliate(int $affiliateId): bool
- rejectAffiliate(int $affiliateId, string $reason): bool
- suspendAffiliate(int $affiliateId, string $reason): bool
- generateAffiliateCode(int $userId): string
- getAffiliateByCode(string $code): ?Affiliate
- updateAffiliateProfile(int $affiliateId, AffiliateData $data): Affiliate
- getAffiliateStatistics(int $affiliateId): array
- getAffiliateCommissions(int $affiliateId, array $filters = []): array
- getAffiliateReferrals(int $affiliateId): array
- calculateTotalEarnings(int $affiliateId): Money
- getTopAffiliates(int $limit = 50): array
- bulkApproveAffiliates(array $affiliateIds): int
- exportAffiliateData(int $affiliateId): array
- deactivateAffiliate(int $affiliateId): bool

**Do**:
- Generate unique affiliate codes automatically
- Track affiliate performance metrics
- Fire events for status changes
- Validate affiliate eligibility
- Support multi-tier affiliate structures
- Implement proper approval workflows
- Track affiliate relationships
- Calculate accurate commission earnings

**Don't**:
- Don't allow duplicate affiliate codes
- Don't bypass approval processes
- Don't ignore affiliate terms compliance
- Don't skip performance tracking
- Don't allow unauthorized status changes
- Don't expose sensitive affiliate data
- Don't ignore affiliate agreement violations

---

### File: 2030b/app/Services/Affiliate/ReferralService.php

**Purpose**: Referral tracking and management service handling referral links, conversion tracking, attribution, and referral campaign analytics with cookie-based tracking.

**Dependencies**:
```php
use App\Contracts\Services\ReferralServiceInterface;
use App\DTOs\Affiliate\ReferralData;
use App\Models\Referral;
use App\Models\Conversion;
use App\Enums\ReferralStatus;
use App\Events\ReferralCreated;
use App\Events\ConversionTracked;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Cookie;
use Illuminate\Support\Facades\Cache;
```

**Methods**:
- trackReferral(ReferralData $referralData): Referral
- processConversion(int $referralId, array $conversionData): Conversion
- getReferralByToken(string $token): ?Referral
- generateReferralLink(int $affiliateId, array $parameters = []): string
- trackClick(string $referralToken, Request $request): bool
- getConversionRate(int $affiliateId): float
- getReferralStatistics(int $affiliateId): array
- getTopReferralSources(int $limit = 20): array
- attributeConversion(int $userId, array $trackingData): ?Referral
- cleanExpiredReferrals(): int
- getReferralChain(int $conversionId): array
- updateReferralStatus(int $referralId, ReferralStatus $status): bool
- getClickThroughRate(int $affiliateId): float
- exportReferralData(array $filters): array

**Do**:
- Use secure tokens for referral tracking
- Implement cookie-based attribution
- Track referral click-through rates
- Support multiple attribution models
- Cache referral data for performance
- Fire events for conversion tracking
- Validate referral authenticity
- Clean expired tracking data regularly

**Don't**:
- Don't allow referral manipulation
- Don't ignore attribution windows
- Don't skip click validation
- Don't expose referral tracking tokens
- Don't cache sensitive referral data indefinitely
- Don't bypass conversion validation
- Don't ignore referral fraud detection

---

### File: 2030b/app/Services/Affiliate/CommissionService.php

**Purpose**: Commission calculation and payment service handling commission structures, payment processing, tax calculations, and commission reporting with multi-currency support.

**Dependencies**:
```php
use App\Contracts\Services\CommissionServiceInterface;
use App\DTOs\Affiliate\CommissionData;
use App\Models\Commission;
use App\Models\Affiliate;
use App\Enums\CommissionStatus;
use App\Enums\CommissionType;
use App\Events\CommissionEarned;
use App\Events\CommissionPaid;
use Money\Money;
use Illuminate\Support\Facades\DB;
```

**Methods**:
- calculateCommission(int $affiliateId, Money $saleAmount, CommissionType $type): Money
- createCommission(CommissionData $commissionData): Commission
- processCommissionPayment(int $commissionId): bool
- getCommissionStructure(int $affiliateId): array
- getPendingCommissions(int $affiliateId): array
- bulkPayCommissions(array $commissionIds): int
- holdCommission(int $commissionId, string $reason): bool
- releaseHeldCommission(int $commissionId): bool
- calculateTaxWithholding(Money $commission, string $taxRegion): Money
- getCommissionReport(array $filters): array
- reverseCommission(int $commissionId, string $reason): bool
- getCommissionHistory(int $affiliateId): array
- updateCommissionRates(int $affiliateId, array $rates): bool
- validateCommissionEligibility(int $affiliateId, int $saleId): bool

**Do**:
- Use Money objects for commission calculations
- Support multiple commission structures
- Calculate tax withholding accurately
- Fire events for commission state changes
- Validate commission eligibility
- Use database transactions for payments
- Track commission payment history
- Implement commission holds for disputes

**Don't**:
- Don't pay commissions without validation
- Don't ignore tax compliance requirements
- Don't skip commission calculation validation
- Don't bypass payment approval workflows
- Don't lose commission tracking data
- Don't ignore commission rate changes
- Don't process duplicate commission payments

---

### File: 2030b/app/Services/Affiliate/TrackingService.php

**Purpose**: Advanced tracking service for affiliate marketing campaigns including pixel tracking, analytics integration, fraud detection, and comprehensive performance monitoring.

**Dependencies**:
```php
use App\Contracts\Services\TrackingServiceInterface;
use App\Models\TrackingPixel;
use App\Models\ClickTracking;
use App\Models\ConversionTracking;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Log;
use Jenssegers\Agent\Agent;
```

**Methods**:
- createTrackingPixel(int $affiliateId, array $parameters): TrackingPixel
- trackClick(string $pixelId, Request $request): ClickTracking
- trackConversion(string $pixelId, array $conversionData): ConversionTracking
- detectFraud(array $trackingData): bool
- getTrackingAnalytics(int $affiliateId, array $filters): array
- generateTrackingReport(array $filters): array
- getClicksByTimeRange(string $startDate, string $endDate): array
- getConversionsBySource(int $affiliateId): array
- detectBotTraffic(Request $request): bool
- getDeviceStatistics(int $affiliateId): array
- getGeographicDistribution(int $affiliateId): array
- blacklistIpAddress(string $ipAddress): bool
- whitelistIpAddress(string $ipAddress): bool
- getTrafficQualityScore(int $affiliateId): float

**Do**:
- Implement comprehensive fraud detection
- Track device and browser information
- Monitor traffic quality metrics
- Use IP-based fraud prevention
- Cache tracking analytics for performance
- Log suspicious tracking activities
- Support real-time tracking updates
- Validate tracking data integrity

**Don't**:
- Don't ignore bot traffic detection
- Don't skip fraud validation
- Don't store excessive tracking data
- Don't ignore suspicious click patterns
- Don't bypass IP blacklist checks
- Don't ignore traffic quality issues
- Don't cache sensitive tracking data

---

### File: 2030b/app/Services/Media/MediaService.php

**Purpose**: Core media management service using Spatie MediaLibrary for file uploads, storage, processing, and media organization with cloud storage integration.

**Dependencies**:
```php
use App\Contracts\Services\MediaServiceInterface;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Spatie\MediaLibrary\MediaCollections\FileAdder;
use Spatie\ImageOptimizer\OptimizerChain;
use Intervention\Image\Facades\Image;
use Illuminate\Http\UploadedFile;
use Illuminate\Support\Facades\Storage;
use League\Flysystem\AwsS3V3\AwsS3V3Adapter;
```

**Methods**:
- uploadFile(UploadedFile $file, string $collection, $model): Media
- deleteFile(int $mediaId): bool
- getFileUrl(int $mediaId, string $conversion = ''): string
- generateFileConversions(Media $media): void
- optimizeImage(Media $media): void
- validateFileType(UploadedFile $file, array $allowedTypes): bool
- calculateFileChecksum(UploadedFile $file): string
- moveToCloud(Media $media): bool
- createThumbnail(Media $media, int $width, int $height): Media
- getFileMetadata(int $mediaId): array
- bulkUpload(array $files, string $collection, $model): array
- syncMediaToCloud(): int
- cleanupOrphanedFiles(): int
- getStorageStatistics(): array

**Do**:
- Use Spatie MediaLibrary for file management
- Optimize images automatically with ImageOptimizer
- Validate file types and sizes before upload
- Generate thumbnails and conversions
- Use cloud storage for scalability
- Calculate file checksums for integrity
- Clean up orphaned files regularly
- Track storage usage statistics

**Don't**:
- Don't accept unlimited file sizes
- Don't skip file type validation
- Don't ignore image optimization
- Don't store files without virus scanning
- Don't bypass file naming conventions
- Don't ignore storage quotas
- Don't cache large media files locally

---

### File: 2030b/app/Services/Media/ImageOptimizationService.php

**Purpose**: Advanced image optimization service handling image compression, format conversion, responsive image generation, and automated optimization workflows using Intervention Image and Spatie ImageOptimizer.

**Dependencies**:
```php
use App\Contracts\Services\ImageOptimizationServiceInterface;
use Intervention\Image\Facades\Image;
use Spatie\ImageOptimizer\OptimizerChain;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Spatie\MediaLibrary\Conversions\Conversion;
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Facades\Config;
```

**Methods**:
- optimizeImage(Media $media, array $options = []): void
- generateResponsiveImages(Media $media, array $breakpoints): array
- convertImageFormat(Media $media, string $targetFormat): Media
- compressImage(Media $media, int $quality): void
- generateWebPVersion(Media $media): Media
- createImageVariants(Media $media, array $variants): array
- calculateOptimizationSavings(Media $media): array
- batchOptimizeImages(array $mediaIds): array
- getOptimizationSettings(string $imageType): array
- validateImageDimensions(Media $media, array $constraints): bool
- watermarkImage(Media $media, string $watermarkPath): void
- cropImageToAspectRatio(Media $media, float $aspectRatio): Media
- generateImageMetadata(Media $media): array
- removeImageExifData(Media $media): void

**Do**:
- Use Intervention Image for image manipulation
- Implement progressive JPEG encoding
- Generate WebP versions for modern browsers
- Create responsive image variants
- Remove EXIF data for privacy
- Calculate optimization savings
- Support batch optimization
- Validate image constraints

**Don't**:
- Don't over-compress images losing quality
- Don't ignore image aspect ratios
- Don't skip responsive image generation
- Don't keep EXIF data unnecessarily
- Don't optimize images without backups
- Don't ignore image format compatibility
- Don't process extremely large images without limits

---

### File: 2030b/app/Services/Media/FileStorageService.php

**Purpose**: File storage abstraction service managing local and cloud storage operations with AWS S3 integration, file versioning, and distributed storage management.

**Dependencies**:
```php
use App\Contracts\Services\FileStorageServiceInterface;
use League\Flysystem\Filesystem;
use League\Flysystem\AwsS3V3\AwsS3V3Adapter;
use Aws\S3\S3Client;
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Facades\Config;
use Illuminate\Http\UploadedFile;
```

**Methods**:
- storeFile(UploadedFile $file, string $path, string $disk = 'default'): string
- deleteFile(string $path, string $disk = 'default'): bool
- moveFile(string $fromPath, string $toPath, string $disk = 'default'): bool
- copyFile(string $fromPath, string $toPath, string $disk = 'default'): bool
- getFileContents(string $path, string $disk = 'default'): string
- getFileUrl(string $path, string $disk = 'default'): string
- generatePresignedUrl(string $path, int $expiresInMinutes = 60): string
- syncToCloud(string $localPath): bool
- getFileMetadata(string $path, string $disk = 'default'): array
- listFiles(string $directory, string $disk = 'default'): array
- createDirectory(string $path, string $disk = 'default'): bool
- deleteDirectory(string $path, string $disk = 'default'): bool
- getStorageUsage(string $disk = 'default'): array
- cleanupTemporaryFiles(): int

**Do**:
- Use Laravel's Storage facade for abstraction
- Support multiple storage drivers (local, S3, etc.)
- Generate presigned URLs for secure access
- Implement proper file path strategies
- Monitor storage usage and quotas
- Clean up temporary files regularly
- Use appropriate file permissions
- Implement file versioning when needed

**Don't**:
- Don't expose direct file system paths
- Don't ignore storage driver failures
- Don't skip file existence validation
- Don't bypass storage security measures
- Don't ignore storage quota limits
- Don't leave temporary files unmanaged
- Don't hardcode storage configurations

---

### File: 2030b/app/Services/File/BackupService.php

**Purpose**: Automated backup service using Spatie Laravel Backup for database backups, file system backups, backup scheduling, and backup integrity verification with cloud storage.

**Dependencies**:
```php
use App\Contracts\Services\BackupServiceInterface;
use Spatie\Backup\Tasks\Backup\BackupJob;
use Spatie\Backup\BackupDestination\BackupDestination;
use Spatie\Backup\Tasks\Cleanup\CleanupJob;
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Facades\Artisan;
use Illuminate\Support\Facades\Log;
```

**Methods**:
- createBackup(array $options = []): bool
- createDatabaseBackup(): bool
- createFilesBackup(): bool
- restoreFromBackup(string $backupName): bool
- listBackups(): array
- deleteBackup(string $backupName): bool
- cleanupOldBackups(): int
- verifyBackupIntegrity(string $backupName): bool
- scheduleBackup(string $schedule): bool
- getBackupStatus(): array
- monitorBackupHealth(): array
- compressBackup(string $backupName): bool
- encryptBackup(string $backupName): bool
- sendBackupNotification(string $status, array $details): void
- getBackupSize(string $backupName): int

**Do**:
- Use Spatie Backup for reliable backup creation
- Schedule regular automated backups
- Verify backup integrity after creation
- Clean up old backups based on retention policy
- Encrypt sensitive backup data
- Monitor backup health and status
- Send notifications for backup events
- Store backups in multiple locations

**Don't**:
- Don't skip backup verification
- Don't ignore backup failures
- Don't keep unlimited backup history
- Don't backup without encryption
- Don't ignore backup storage limits
- Don't skip backup testing
- Don't backup temporary or cache files

---

### File: 2030b/app/Services/File/FileValidatorService.php

**Purpose**: Comprehensive file validation service handling file type validation, virus scanning, content analysis, and security checks to prevent malicious file uploads.

**Dependencies**:
```php
use App\Contracts\Services\FileValidatorServiceInterface;
use Illuminate\Http\UploadedFile;
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Facades\Config;
use Illuminate\Validation\Rules\File;
```

**Methods**:
- validateFile(UploadedFile $file, array $rules): array
- validateFileType(UploadedFile $file, array $allowedTypes): bool
- validateFileSize(UploadedFile $file, int $maxSizeBytes): bool
- scanForViruses(UploadedFile $file): bool
- validateImageContent(UploadedFile $file): bool
- validateDocumentStructure(UploadedFile $file): bool
- checkFileSignature(UploadedFile $file): bool
- validateFileName(string $fileName): bool
- detectMaliciousContent(UploadedFile $file): bool
- validateMimeType(UploadedFile $file, array $allowedMimes): bool
- checkFileExecutability(UploadedFile $file): bool
- validateArchiveContents(UploadedFile $file): array
- getFileValidationRules(string $fileType): array
- generateValidationReport(UploadedFile $file): array

**Do**:
- Validate file extensions against whitelist
- Check MIME types for consistency
- Scan files for malicious content
- Validate file signatures (magic numbers)
- Implement file size limitations
- Check for executable file uploads
- Validate archive contents before extraction
- Generate detailed validation reports

**Don't**:
- Don't trust client-provided MIME types
- Don't skip virus scanning
- Don't allow executable file uploads
- Don't ignore file size limits
- Don't validate only file extensions
- Don't skip content analysis
- Don't allow uploads without validation

---

### File: 2030b/app/Services/File/VirusScannerService.php

**Purpose**: Virus scanning service integrating with antivirus engines to scan uploaded files for malware, viruses, and other security threats before processing.

**Dependencies**:
```php
use App\Contracts\Services\VirusScannerServiceInterface;
use Illuminate\Http\UploadedFile;
use Illuminate\Support\Facades\Process;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Facades\Config;
```

**Methods**:
- scanFile(UploadedFile $file): array
- scanFileContents(string $fileContents): array
- scanFilePath(string $filePath): array
- isFileClean(UploadedFile $file): bool
- quarantineFile(string $filePath): bool
- getVirusSignatures(): array
- updateVirusDefinitions(): bool
- getScannerStatus(): array
- bulkScanFiles(array $filePaths): array
- scheduleScan(string $filePath): bool
- getQuarantinedFiles(): array
- deleteQuarantinedFile(string $filePath): bool
- generateScanReport(array $scanResults): string
- configureScannerSettings(array $settings): bool

**Do**:
- Integrate with reliable antivirus engines
- Quarantine suspicious files immediately
- Update virus definitions regularly
- Log all scan results for audit trails
- Scan files before processing
- Generate detailed scan reports
- Monitor scanner health status
- Configure appropriate scan sensitivity

**Don't**:
- Don't process files without scanning
- Don't ignore quarantine recommendations
- Don't skip virus definition updates
- Don't expose scan engine details
- Don't allow bypassing virus scans
- Don't ignore scanner failures
- Don't delete quarantined files immediately

---

### File: 2030b/app/Services/File/FileConverterService.php

**Purpose**: File format conversion service handling document conversions, media format transformations, and automated file processing workflows for multiple file types.

**Dependencies**:
```php
use App\Contracts\Services\FileConverterServiceInterface;
use League\HTMLToMarkdown\HtmlConverter;
use Spatie\LaravelMarkdown\MarkdownRenderer;
use Barryvdh\DomPDF\Facade\Pdf;
use Maatwebsite\Excel\Facades\Excel;
use Illuminate\Http\UploadedFile;
use Illuminate\Support\Facades\Storage;
```

**Methods**:
- convertFile(UploadedFile $file, string $targetFormat): string
- convertHtmlToPdf(string $html): string
- convertMarkdownToHtml(string $markdown): string
- convertHtmlToMarkdown(string $html): string
- convertExcelToPdf(UploadedFile $excelFile): string
- convertImageFormat(UploadedFile $image, string $targetFormat): string
- convertDocumentToText(UploadedFile $document): string
- batchConvertFiles(array $files, string $targetFormat): array
- getSupportedFormats(): array
- getConversionOptions(string $sourceFormat, string $targetFormat): array
- validateConversionSupport(string $sourceFormat, string $targetFormat): bool
- getConversionProgress(string $jobId): array
- cancelConversion(string $jobId): bool
- scheduleConversion(UploadedFile $file, string $targetFormat): string

**Do**:
- Support multiple file format conversions
- Use appropriate libraries for each format
- Validate format compatibility before conversion
- Handle large file conversions asynchronously
- Preserve document formatting when possible
- Generate conversion progress reports
- Clean up temporary conversion files
- Implement conversion quality settings

**Don't**:
- Don't convert files without validation
- Don't ignore conversion errors
- Don't keep temporary files indefinitely
- Don't attempt unsupported conversions
- Don't lose document metadata during conversion
- Don't ignore conversion quality issues
- Don't skip format-specific validations

---

### File: 2030b/app/Services/File/ArchiveService.php

**Purpose**: Archive management service handling ZIP file creation, extraction, compression, and archive manipulation with security validation and content management.

**Dependencies**:
```php
use App\Contracts\Services\ArchiveServiceInterface;
use ZipArchive;
use PharData;
use Illuminate\Http\UploadedFile;
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Facades\File;
use Illuminate\Support\Collection;
```

**Methods**:
- createZipArchive(array $files, string $archiveName): string
- extractArchive(UploadedFile $archive, string $destinationPath): array
- addFileToArchive(string $archivePath, string $filePath, string $archiveFilePath): bool
- removeFileFromArchive(string $archivePath, string $fileInArchive): bool
- listArchiveContents(string $archivePath): array
- validateArchiveIntegrity(string $archivePath): bool
- compressDirectory(string $directoryPath, string $archiveName): string
- extractToDirectory(string $archivePath, string $destinationPath): bool
- getArchiveMetadata(string $archivePath): array
- passwordProtectArchive(string $archivePath, string $password): bool
- validateArchiveContents(string $archivePath): array
- calculateArchiveSize(string $archivePath): int
- repairCorruptedArchive(string $archivePath): bool
- convertArchiveFormat(string $archivePath, string $targetFormat): string

**Do**:
- Validate archive contents before extraction
- Check for malicious files in archives
- Implement extraction path validation
- Use password protection for sensitive archives
- Limit extraction file sizes
- Validate archive integrity
- Clean up temporary extraction files
- Monitor archive operations

**Don't**:
- Don't extract archives without validation
- Don't ignore path traversal vulnerabilities
- Don't extract files outside designated directories
- Don't skip archive content scanning
- Don't ignore corrupted archive warnings
- Don't allow unlimited extraction sizes
- Don't keep extracted files indefinitely

---

### File: 2030b/app/Services/System/ConfigurationService.php

**Purpose**: System configuration management service handling dynamic configuration updates, environment management, and system settings with caching and validation.

**Dependencies**:
```php
use App\Contracts\Services\ConfigurationServiceInterface;
use App\Models\SystemConfiguration;
use App\Enums\ConfigurationType;
use Illuminate\Support\Facades\Config;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Artisan;
```

**Methods**:
- getConfiguration(string $key, $default = null): mixed
- setConfiguration(string $key, mixed $value): bool
- deleteConfiguration(string $key): bool
- getAllConfigurations(): array
- getConfigurationsByType(ConfigurationType $type): array
- validateConfiguration(string $key, mixed $value): bool
- cacheConfiguration(string $key, mixed $value, int $ttl = 3600): void
- clearConfigurationCache(): bool
- exportConfigurations(array $keys = []): array
- importConfigurations(array $configurations): int
- getConfigurationHistory(string $key): array
- revertConfiguration(string $key, string $version): bool
- getDefaultConfigurations(): array
- resetToDefaults(array $keys = []): int

**Do**:
- Cache frequently accessed configurations
- Validate configuration values before saving
- Track configuration change history
- Support configuration versioning
- Implement configuration type validation
- Use proper data types for configuration values
- Clear relevant caches when updating configs
- Provide configuration backup and restore

**Don't**:
- Don't cache sensitive configurations indefinitely
- Don't skip configuration validation
- Don't allow invalid configuration types
- Don't ignore configuration dependencies
- Don't bypass configuration security checks
- Don't lose configuration history
- Don't update configurations without validation

---

### File: 2030b/app/Services/System/LocalizationService.php

**Purpose**: Multi-language support service using mcamara/laravel-localization and Spatie translatable for dynamic language switching, content translation, and locale management.

**Dependencies**:
```php
use App\Contracts\Services\LocalizationServiceInterface;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
use Spatie\TranslatablePackage\HasTranslations;
use App\Models\Translation;
use App\Enums\TextDirection;
use Illuminate\Support\Facades\App;
use Illuminate\Support\Facades\Cache;
```

**Methods**:
- setLocale(string $locale): bool
- getCurrentLocale(): string
- getSupportedLocales(): array
- translateKey(string $key, array $parameters = [], string $locale = null): string
- translateModel($model, array $attributes, string $locale): array
- addTranslation(string $key, string $value, string $locale): bool
- getTranslations(string $key): array
- deleteTranslation(string $key, string $locale = null): bool
- exportTranslations(string $locale, string $format = 'json'): string
- importTranslations(string $locale, array $translations): int
- getLocaleDirection(string $locale): TextDirection
- getMissingTranslations(string $locale): array
- generateLocaleUrls(string $url): array
- cacheTranslations(string $locale): void

**Do**:
- Use Laravel Localization for URL-based switching
- Support RTL languages with proper text direction
- Cache translations for performance
- Validate locale codes before setting
- Support pluralization rules
- Handle missing translations gracefully
- Use Spatie Translatable for model translations
- Generate SEO-friendly localized URLs

**Don't**:
- Don't hardcode language strings in code
- Don't ignore text direction for RTL languages
- Don't cache all translations indefinitely
- Don't expose untranslated content
- Don't ignore locale validation
- Don't skip pluralization handling
- Don't bypass locale-specific formatting

---

### File: 2030b/app/Services/System/AnalyticsService.php

**Purpose**: System analytics and reporting service providing business intelligence, performance metrics, user behavior analytics, and comprehensive reporting with data visualization support.

**Dependencies**:
```php
use App\Contracts\Services\AnalyticsServiceInterface;
use App\Models\AnalyticsEvent;
use App\Models\SystemMetric;
use App\Enums\EventType;
use App\Enums\MetricType;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Cache;
use Carbon\Carbon;
```

**Methods**:
- trackEvent(string $eventName, array $properties, int $userId = null): void
- getEventStatistics(string $eventName, array $filters = []): array
- getUserBehaviorAnalytics(int $userId): array
- getCourseAnalytics(int $courseId): array
- getRevenueAnalytics(array $filters): array
- getSystemPerformanceMetrics(): array
- generateReport(string $reportType, array $parameters): array
- getPopularContent(int $limit = 20): array
- getUserEngagementMetrics(array $filters): array
- getConversionFunnelData(string $funnelName): array
- trackPageView(string $url, int $userId = null): void
- getRetentionAnalytics(array $cohortFilters): array
- exportAnalyticsData(array $filters, string $format): string
- getRealtimeMetrics(): array

**Do**:
- Track user interactions and events
- Generate actionable business insights
- Cache analytics data for performance
- Support real-time metrics display
- Implement data retention policies
- Aggregate metrics efficiently
- Provide drill-down capabilities
- Export analytics data for external tools

**Don't**:
- Don't track sensitive personal data
- Don't ignore data privacy regulations
- Don't store analytics data indefinitely
- Don't skip data anonymization
- Don't ignore performance impact
- Don't expose raw analytics data
- Don't bypass data retention policies

---

### File: 2030b/app/Services/System/NotificationService.php

**Purpose**: Comprehensive notification system service handling email notifications, push notifications, in-app notifications, and notification preferences management with template support.

**Dependencies**:
```php
use App\Contracts\Services\NotificationServiceInterface;
use App\Models\Notification;
use App\Models\NotificationTemplate;
use App\Enums\NotificationType;
use App\Enums\NotificationChannel;
use Illuminate\Support\Facades\Notification as NotificationFacade;
use Illuminate\Notifications\DatabaseNotification;
use Spatie\DatabaseMailTemplates\MailTemplate;
```

**Methods**:
- sendNotification(int $userId, string $type, array $data, array $channels = []): bool
- sendBulkNotification(array $userIds, string $type, array $data): int
- markAsRead(int $notificationId): bool
- markAllAsRead(int $userId): int
- deleteNotification(int $notificationId): bool
- getUserNotifications(int $userId, array $filters = []): array
- getUnreadCount(int $userId): int
- updateNotificationPreferences(int $userId, array $preferences): bool
- getNotificationPreferences(int $userId): array
- createNotificationTemplate(string $name, array $template): NotificationTemplate
- sendScheduledNotification(int $userId, string $type, array $data, Carbon $scheduledFor): bool
- cancelScheduledNotification(string $notificationId): bool
- getNotificationStatistics(array $filters = []): array
- subscribeToChannel(int $userId, string $channel): bool

**Do**:
- Support multiple notification channels
- Use database mail templates for consistency
- Respect user notification preferences
- Track notification delivery status
- Implement notification scheduling
- Provide unsubscribe mechanisms
- Cache notification preferences
- Generate notification analytics

**Don't**:
- Don't send notifications without user consent
- Don't ignore notification preferences
- Don't spam users with excessive notifications
- Don't skip notification delivery validation
- Don't expose sensitive data in notifications
- Don't ignore notification failures
- Don't bypass rate limiting for notifications

---

### File: 2030b/app/Services/Permission/PermissionService.php

**Purpose**: Permission management service using Spatie Laravel Permission for role-based access control, permission assignment, and authorization workflows with hierarchical permissions.

**Dependencies**:
```php
use App\Contracts\Services\PermissionServiceInterface;
use Spatie\Permission\Models\Permission;
use Spatie\Permission\Models\Role;
use App\Models\User;
use Spatie\Permission\Exceptions\PermissionDoesNotExist;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Collection;
```

**Methods**:
- createPermission(string $name, string $guardName = 'web'): Permission
- assignPermissionToUser(int $userId, string $permission): bool
- revokePermissionFromUser(int $userId, string $permission): bool
- assignPermissionToRole(string $roleName, string $permission): bool
- revokePermissionFromRole(string $roleName, string $permission): bool
- getUserPermissions(int $userId): Collection
- getRolePermissions(string $roleName): Collection
- hasPermission(int $userId, string $permission): bool
- syncUserPermissions(int $userId, array $permissions): void
- syncRolePermissions(string $roleName, array $permissions): void
- getPermissionHierarchy(): array
- bulkAssignPermissions(array $userIds, array $permissions): int
- getPermissionUsage(string $permission): array
- deletePermission(string $permission): bool

**Do**:
- Use Spatie Permission for RBAC implementation
- Cache permission checks for performance
- Implement hierarchical permission structures
- Validate permissions before assignment
- Track permission usage and assignments
- Use guard names for multi-auth systems
- Support bulk permission operations
- Maintain permission audit trails

**Don't**:
- Don't bypass permission checks
- Don't create duplicate permissions
- Don't ignore permission hierarchy rules
- Don't cache permissions indefinitely
- Don't assign permissions without validation
- Don't delete permissions in use
- Don't ignore guard name consistency

---

### File: 2030b/app/Services/Permission/RoleService.php

**Purpose**: Role management service for creating, managing, and assigning user roles with Spatie Permission integration, supporting role hierarchies and role-based access control.

**Dependencies**:
```php
use App\Contracts\Services\RoleServiceInterface;
use Spatie\Permission\Models\Role;
use Spatie\Permission\Models\Permission;
use App\Models\User;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Collection;
use Illuminate\Support\Facades\DB;
```

**Methods**:
- createRole(string $name, string $guardName = 'web'): Role
- assignRoleToUser(int $userId, string $roleName): bool
- removeRoleFromUser(int $userId, string $roleName): bool
- getUserRoles(int $userId): Collection
- getRoleUsers(string $roleName): Collection
- updateRolePermissions(string $roleName, array $permissions): bool
- deleteRole(string $roleName): bool
- duplicateRole(string $sourceRole, string $newRoleName): Role
- getRoleHierarchy(): array
- hasRole(int $userId, string $roleName): bool
- syncUserRoles(int $userId, array $roles): void
- bulkAssignRoles(array $userIds, array $roles): int
- getRoleStatistics(): array
- validateRoleAssignment(int $userId, string $roleName): bool

**Do**:
- Use Spatie Permission for role management
- Support role hierarchies and inheritance
- Cache role assignments for performance
- Validate role assignments before processing
- Track role usage statistics
- Support bulk role operations
- Maintain role assignment audit trails
- Use consistent guard names

**Don't**:
- Don't create conflicting role hierarchies
- Don't assign roles without validation
- Don't delete roles with active users
- Don't bypass role assignment rules
- Don't cache role data indefinitely
- Don't ignore role permission conflicts
- Don't allow circular role dependencies

---

### File: 2030b/app/Services/Api/ApiKeyService.php

**Purpose**: API key management service handling API key generation, validation, rate limiting, and API access control with Laravel Sanctum integration for secure API authentication.

**Dependencies**:
```php
use App\Contracts\Services\ApiKeyServiceInterface;
use App\Models\ApiKey;
use App\Models\User;
use Laravel\Sanctum\PersonalAccessToken;
use Illuminate\Support\Str;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\Cache;
use Carbon\Carbon;
```

**Methods**:
- generateApiKey(int $userId, string $name, array $abilities = ['*']): ApiKey
- validateApiKey(string $key): ?ApiKey
- revokeApiKey(string $keyId): bool
- renewApiKey(string $keyId): string
- getApiKeys(int $userId): array
- updateApiKeyAbilities(string $keyId, array $abilities): bool
- trackApiUsage(string $keyId, string $endpoint): void
- getApiUsageStatistics(string $keyId): array
- rotateApiKey(string $keyId): ApiKey
- setApiKeyExpiration(string $keyId, Carbon $expiresAt): bool
- bulkRevokeApiKeys(array $keyIds): int
- getApiKeysByUser(int $userId): array
- isApiKeyActive(string $keyId): bool
- getApiKeyRateLimits(string $keyId): array

**Do**:
- Use Laravel Sanctum for token management
- Generate cryptographically secure API keys
- Implement API key expiration
- Track API usage and analytics
- Support ability-based permissions
- Rate limit API key usage
- Hash API keys for security
- Provide API key rotation capabilities

**Don't**:
- Don't store API keys in plain text
- Don't generate predictable API keys
- Don't ignore API key expiration
- Don't skip API usage tracking
- Don't allow unlimited API access
- Don't expose API key details
- Don't bypass API authentication

---

### File: 2030b/app/Services/Api/ApiRateLimitService.php

**Purpose**: API rate limiting service implementing configurable rate limits, throttling mechanisms, and API usage monitoring with Redis-based rate limiting for scalability.

**Dependencies**:
```php
use App\Contracts\Services\ApiRateLimitServiceInterface;
use Illuminate\Cache\RateLimiter;
use Illuminate\Support\Facades\Redis;
use Illuminate\Http\Request;
use App\Models\ApiKey;
use Carbon\Carbon;
```

**Methods**:
- checkRateLimit(string $key, int $maxAttempts, int $decayMinutes): bool
- incrementAttempts(string $key, int $decayMinutes): int
- getRemainingAttempts(string $key, int $maxAttempts, int $decayMinutes): int
- getRetryAfter(string $key, int $decayMinutes): int
- clearRateLimit(string $key): bool
- setCustomRateLimit(string $key, int $maxAttempts, int $decayMinutes): bool
- getRateLimitStatus(string $key): array
- applyApiKeyLimits(ApiKey $apiKey): array
- trackApiCall(string $endpoint, string $apiKey): void
- getApiUsageReport(string $apiKey, string $period): array
- blockIpAddress(string $ipAddress, int $minutes): bool
- unblockIpAddress(string $ipAddress): bool
- getBlockedIpAddresses(): array
- isIpBlocked(string $ipAddress): bool

**Do**:
- Use Redis for distributed rate limiting
- Support different rate limits per API endpoint
- Implement sliding window rate limiting
- Track API usage patterns
- Provide rate limit status in headers
- Support IP-based blocking
- Cache rate limit configurations
- Generate rate limiting reports

**Don't**:
- Don't ignore rate limit breaches
- Don't use memory-based rate limiting in production
- Don't apply same limits to all endpoints
- Don't skip rate limit validation
- Don't expose rate limiting internals
- Don't ignore suspicious API usage patterns
- Don't bypass rate limiting for any requests

---

### File: 2030b/app/Services/Api/ApiDocumentationService.php

**Purpose**: API documentation generation and management service creating OpenAPI specifications, interactive documentation, and API versioning support with automated documentation generation.

**Dependencies**:
```php
use App\Contracts\Services\ApiDocumentationServiceInterface;
use OpenApi\Generator;
use OpenApi\Annotations as OA;
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Facades\Route;
use Symfony\Component\Yaml\Yaml;
```

**Methods**:
- generateApiDocumentation(): array
- generateOpenApiSpec(): string
- updateApiDocumentation(): bool
- getApiVersion(): string
- setApiVersion(string $version): bool
- generateSwaggerUI(): string
- exportDocumentation(string $format = 'json'): string
- validateApiSpec(): array
- getApiEndpoints(): array
- generatePostmanCollection(): array
- updateEndpointDocumentation(string $endpoint, array $documentation): bool
- generateApiChangelog(): string
- getApiMetrics(): array
- syncDocumentationChanges(): bool

**Do**:
- Generate OpenAPI 3.0 specifications
- Support interactive Swagger UI
- Version API documentation properly
- Validate API specifications automatically
- Export documentation in multiple formats
- Generate Postman collections
- Track API documentation changes
- Provide comprehensive examples

**Don't**:
- Don't expose internal API details
- Don't ignore API documentation updates
- Don't generate incomplete specifications
- Don't skip API validation
- Don't hardcode API documentation
- Don't ignore versioning requirements
- Don't expose deprecated endpoints without warnings

---

### File: 2030b/app/Services/Cache/CourseCacheService.php

**Purpose**: Course-specific caching service optimizing course data retrieval, search results, and course-related analytics with intelligent cache invalidation strategies.

**Dependencies**:
```php
use App\Contracts\Services\CourseCacheServiceInterface;
use App\Models\DocuCourse;
use App\Models\Episode;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Redis;
use Carbon\Carbon;
```

**Methods**:
- getCachedCourse(int $courseId): ?DocuCourse
- cacheCourse(DocuCourse $course, int $ttl = 3600): void
- invalidateCourseCache(int $courseId): bool
- getCachedCourseList(array $filters, int $page = 1): array
- cacheCourseList(array $courses, array $filters, int $page, int $ttl = 1800): void
- getCachedCourseStatistics(int $courseId): array
- cacheCourseStatistics(int $courseId, array $statistics, int $ttl = 3600): void
- getCachedPopularCourses(int $limit = 10): array
- cachePopularCourses(array $courses, int $ttl = 7200): void
- invalidateCourseListCaches(): int
- getCachedCourseProgress(int $userId, int $courseId): array
- cacheCourseProgress(int $userId, int $courseId, array $progress): void
- warmupCourseCache(int $courseId): bool
- getCacheHitRate(): float

**Do**:
- Cache frequently accessed course data
- Implement intelligent cache invalidation
- Use appropriate TTL values for different data types
- Tag cache entries for efficient invalidation
- Monitor cache hit rates
- Warm up cache for popular courses
- Use Redis for distributed caching
- Implement cache versioning

**Don't**:
- Don't cache sensitive course data
- Don't ignore cache invalidation on updates
- Don't use overly long TTL values
- Don't cache user-specific data globally
- Don't skip cache warming for new courses
- Don't ignore cache performance metrics
- Don't cache stale or outdated data

---

### File: 2030b/app/Services/Cache/UserCacheService.php

**Purpose**: User-specific caching service managing user profiles, preferences, permissions, and session data with personalized cache invalidation and user-centric optimization.

**Dependencies**:
```php
use App\Contracts\Services\UserCacheServiceInterface;
use App\Models\User;
use App\Models\UserProfile;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Auth;
use Spatie\Permission\Models\Role;
use Spatie\Permission\Models\Permission;
```

**Methods**:
- getCachedUser(int $userId): ?User
- cacheUser(User $user, int $ttl = 3600): void
- invalidateUserCache(int $userId): bool
- getCachedUserProfile(int $userId): ?UserProfile
- cacheUserProfile(UserProfile $profile, int $ttl = 3600): void
- getCachedUserPermissions(int $userId): array
- cacheUserPermissions(int $userId, array $permissions, int $ttl = 3600): void
- getCachedUserRoles(int $userId): array
- cacheUserRoles(int $userId, array $roles, int $ttl = 3600): void
- invalidateUserPermissionsCache(int $userId): bool
- getCachedUserPreferences(int $userId): array
- cacheUserPreferences(int $userId, array $preferences): void
- warmupUserCache(int $userId): bool
- getCachedUserStatistics(int $userId): array

**Do**:
- Cache user profile data for quick access
- Implement user-specific cache keys
- Cache user permissions and roles
- Invalidate cache on profile updates
- Use session-based cache for logged-in users
- Cache user preferences and settings
- Implement cache warming for active users
- Tag user cache entries properly

**Don't**:
- Don't cache sensitive user information
- Don't share user cache between users
- Don't ignore permission cache invalidation
- Don't cache temporary user data
- Don't use predictable cache keys
- Don't cache user session tokens
- Don't ignore user privacy settings

---

### File: 2030b/app/Services/Cache/ContentCacheService.php

**Purpose**: Content-specific caching service for episodes, media files, search results, and content delivery optimization with CDN integration and content versioning support.

**Dependencies**:
```php
use App\Contracts\Services\ContentCacheServiceInterface;
use App\Models\Episode;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Storage;
use Carbon\Carbon;
```

**Methods**:
- getCachedContent(int $episodeId): ?Episode
- cacheContent(Episode $episode, int $ttl = 7200): void
- invalidateContentCache(int $episodeId): bool
- getCachedMediaUrl(int $mediaId, string $conversion = ''): ?string
- cacheMediaUrl(int $mediaId, string $url, string $conversion = '', int $ttl = 86400): void
- getCachedSearchResults(string $query, array $filters): ?array
- cacheSearchResults(string $query, array $filters, array $results, int $ttl = 1800): void
- invalidateSearchCache(string $pattern = '*'): int
- getCachedContentToc(int $courseId): ?array
- cacheContentToc(int $courseId, array $toc, int $ttl = 3600): void
- preloadContentCache(array $episodeIds): bool
- getCachedContentMetadata(int $episodeId): ?array
- cacheContentMetadata(int $episodeId, array $metadata): void
- optimizeContentDelivery(int $episodeId): bool

**Do**:
- Cache rendered content for performance
- Use CDN for static content delivery
- Implement content versioning in cache keys
- Preload cache for popular content
- Cache search results temporarily
- Use appropriate TTL for different content types
- Tag content cache for efficient invalidation
- Monitor content cache performance

**Don't**:
- Don't cache dynamic user-specific content
- Don't ignore content update invalidation
- Don't cache expired or outdated content
- Don't skip content validation before caching
- Don't cache large media files locally
- Don't ignore content access permissions
- Don't use indefinite cache TTL

---

### File: 2030b/app/Services/Cache/CacheInvalidationService.php

**Purpose**: Centralized cache invalidation service managing cache dependencies, bulk invalidation, and cache coherence across multiple cache layers with event-driven invalidation.

**Dependencies**:
```php
use App\Contracts\Services\CacheInvalidationServiceInterface;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Redis;
use Illuminate\Support\Facades\Event;
use App\Events\CacheInvalidated;
```

**Methods**:
- invalidateByTag(string $tag): int
- invalidateByPattern(string $pattern): int
- invalidateMultipleTags(array $tags): int
- invalidateUserCache(int $userId): int
- invalidateCourseCache(int $courseId): int
- invalidateContentCache(int $episodeId): int
- scheduleInvalidation(string $cacheKey, Carbon $invalidateAt): bool
- bulkInvalidate(array $cacheKeys): int
- cascadeInvalidation(string $dependency): int
- registerCacheDependency(string $cacheKey, array $dependencies): bool
- getCacheInvalidationLog(): array
- clearAllCaches(): bool
- optimizeCacheStorage(): bool
- getCacheStatistics(): array

**Do**:
- Use cache tags for efficient invalidation
- Implement cascade invalidation for dependencies
- Schedule cache invalidation for time-sensitive data
- Log cache invalidation activities
- Support pattern-based invalidation
- Fire events for cache invalidation
- Monitor invalidation performance
- Use Redis for distributed invalidation

**Don't**:
- Don't invalidate all caches frequently
- Don't ignore cache dependencies
- Don't skip invalidation logging
- Don't use inefficient invalidation patterns
- Don't invalidate cache without validation
- Don't ignore cascade effects
- Don't bypass scheduled invalidations

---

### File: 2030b/app/Services/Search/ElasticSearchService.php

**Purpose**: Elasticsearch integration service providing advanced search capabilities, full-text search, faceted search, and search analytics with Laravel Scout integration.

**Dependencies**:
```php
use App\Contracts\Services\ElasticSearchServiceInterface;
use Elasticsearch\ClientBuilder;
use Laravel\Scout\EngineManager;
use Illuminate\Support\Facades\Config;
use Illuminate\Database\Eloquent\Collection;
```

**Methods**:
- indexDocument(string $index, string $id, array $document): bool
- searchDocuments(string $index, array $query): array
- deleteDocument(string $index, string $id): bool
- updateDocument(string $index, string $id, array $document): bool
- bulkIndexDocuments(string $index, array $documents): bool
- createIndex(string $index, array $settings): bool
- deleteIndex(string $index): bool
- getIndexStatistics(string $index): array
- searchWithAggregations(string $index, array $query, array $aggregations): array
- suggestQuery(string $index, string $field, string $text): array
- reindexData(string $sourceIndex, string $targetIndex): bool
- optimizeIndex(string $index): bool
- getSearchAnalytics(string $index): array
- configureAnalyzers(string $index, array $analyzers): bool

**Do**:
- Use Elasticsearch client for search operations
- Implement proper index mapping
- Support full-text search with relevance scoring
- Use aggregations for faceted search
- Configure appropriate analyzers
- Monitor search performance
- Implement search suggestions
- Optimize indices regularly

**Don't**:
- Don't index sensitive data without encryption
- Don't ignore index optimization
- Don't skip search analytics
- Don't use inefficient queries
- Don't ignore mapping updates
- Don't bypass index security
- Don't neglect cluster monitoring

---

### File: 2030b/app/Services/Search/CourseSearchService.php

**Purpose**: Course-specific search service implementing advanced course filtering, search ranking, recommendation algorithms, and course discovery features with personalized results.

**Dependencies**:
```php
use App\Contracts\Services\CourseSearchServiceInterface;
use App\Models\DocuCourse;
use App\Services\Search\ElasticSearchService;
use Laravel\Scout\Searchable;
use Illuminate\Support\Facades\Auth;
use Illuminate\Database\Eloquent\Builder;
```

**Methods**:
- searchCourses(string $query, array $filters = []): Collection
- getPopularCourses(int $limit = 20): Collection
- getRecommendedCourses(int $userId, int $limit = 10): Collection
- searchByCategory(int $categoryId, string $query = ''): Collection
- searchByInstructor(int $instructorId, string $query = ''): Collection
- getRelatedCourses(int $courseId, int $limit = 5): Collection
- advancedCourseSearch(array $criteria): Collection
- getFeaturedCourses(int $limit = 10): Collection
- searchByDifficulty(string $difficulty, array $filters = []): Collection
- getNewCourses(int $limit = 20): Collection
- getTrendingCourses(int $limit = 20): Collection
- searchCoursesByPrice(float $minPrice, float $maxPrice): Collection
- getCourseSuggestions(string $partial): array
- buildSearchIndex(): bool

**Do**:
- Implement intelligent search ranking
- Support advanced filtering options
- Use personalized recommendations
- Cache popular search results
- Track search patterns for improvements
- Support faceted search navigation
- Implement course suggestion features
- Use relevance scoring for results

**Don't**:
- Don't expose private courses in search
- Don't ignore user access permissions
- Don't skip search result validation
- Don't return irrelevant results
- Don't ignore search performance
- Don't bypass course status checks
- Don't cache personalized results globally

---

### File: 2030b/app/Services/Search/UserSearchService.php

**Purpose**: User search service for finding users, instructors, and learners with privacy controls, search permissions, and user discovery features for administrative and social functions.

**Dependencies**:
```php
use App\Contracts\Services\UserSearchServiceInterface;
use App\Models\User;
use Spatie\Permission\Models\Role;
use Illuminate\Support\Facades\Auth;
use Illuminate\Database\Eloquent\Builder;
use Laravel\Scout\Searchable;
```

**Methods**:
- searchUsers(string $query, array $filters = []): Collection
- searchInstructors(string $query = ''): Collection
- searchLearners(string $query, array $filters = []): Collection
- searchByRole(string $roleName, string $query = ''): Collection
- getActiveUsers(int $limit = 50): Collection
- searchUsersByLevel(string $level): Collection
- findUsersBySkills(array $skills): Collection
- searchUsersByLocation(string $location): Collection
- getTopInstructors(int $limit = 20): Collection
- getNewUsers(int $limit = 50): Collection
- searchUsersByActivity(string $activityType): Collection
- getUserSuggestions(string $partial): array
- validateUserSearchPermission(int $searcherId, int $targetId): bool
- buildUserSearchIndex(): bool

**Do**:
- Respect user privacy settings
- Implement permission-based search
- Support role-based user filtering
- Cache search results appropriately
- Validate search permissions
- Support user discovery features
- Implement search suggestions
- Track user search patterns

**Don't**:
- Don't expose private user information
- Don't bypass privacy settings
- Don't ignore search permissions
- Don't return inappropriate user data
- Don't allow unauthorized user discovery
- Don't cache sensitive user search data
- Don't ignore user blocking/privacy lists

---

### File: 2030b/app/Services/Search/AdvancedFilterService.php

**Purpose**: Advanced filtering service providing complex search filters, dynamic filter generation, and filter state management for enhanced search experiences across the platform.

**Dependencies**:
```php
use App\Contracts\Services\AdvancedFilterServiceInterface;
use Spatie\QueryBuilder\QueryBuilder;
use Spatie\QueryBuilder\AllowedFilter;
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Http\Request;
```

**Methods**:
- applyFilters(Builder $query, array $filters): Builder
- buildFilterQuery(Request $request, string $model): QueryBuilder
- getAvailableFilters(string $model): array
- generateFilterOptions(string $field, string $model): array
- saveFilterPreset(int $userId, string $name, array $filters): bool
- loadFilterPreset(int $userId, string $name): array
- getFilterPresets(int $userId): array
- deleteFilterPreset(int $userId, string $name): bool
- validateFilters(array $filters, string $model): array
- getFilterStatistics(array $filters): array
- optimizeFilterQuery(Builder $query): Builder
- cacheFilterResults(string $cacheKey, array $results): void
- getCachedFilterResults(string $cacheKey): ?array
- generateFilterUrl(array $filters): string

**Do**:
- Use Spatie Query Builder for filter implementation
- Support dynamic filter generation
- Validate filters against allowed options
- Cache filter results for performance
- Save and restore user filter presets
- Optimize filter queries for performance
- Generate SEO-friendly filter URLs
- Track filter usage statistics

**Don't**:
- Don't allow unrestricted filtering
- Don't ignore filter validation
- Don't cache all filter combinations
- Don't expose sensitive data through filters
- Don't skip filter query optimization
- Don't ignore filter performance impact
- Don't allow SQL injection through filters

---

### File: 2030b/app/Services/Health/DatabaseHealthCheck.php

**Purpose**: Database health monitoring service checking database connectivity, performance metrics, query optimization, and database integrity for system reliability monitoring.

**Dependencies**:
```php
use App\Contracts\Services\DatabaseHealthCheckInterface;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Schema;
use Illuminate\Support\Facades\Artisan;
use Carbon\Carbon;
```

**Methods**:
- checkDatabaseConnection(): array
- checkTableIntegrity(): array
- analyzeQueryPerformance(): array
- checkIndexHealth(): array
- monitorConnectionPool(): array
- checkDiskSpace(): array
- validateDataConsistency(): array
- checkBackupStatus(): array
- analyzeSlowQueries(): array
- checkReplicationLag(): array
- monitorLockWaits(): array
- validateTableConstraints(): array
- checkDatabaseSize(): array
- runDiagnosticQueries(): array

**Do**:
- Monitor database connections regularly
- Check table integrity and constraints
- Analyze slow query performance
- Monitor disk space usage
- Validate data consistency
- Check backup status and schedules
- Monitor replication health
- Track connection pool usage

**Don't**:
- Don't run intensive checks during peak hours
- Don't ignore persistent health issues
- Don't skip constraint validation
- Don't overlook slow query patterns
- Don't ignore backup failures
- Don't bypass integrity checks
- Don't ignore connection pool exhaustion

---

### File: 2030b/app/Services/Health/StorageHealthCheck.php

**Purpose**: Storage system health monitoring service for file system integrity, cloud storage connectivity, storage quotas, and backup verification with comprehensive storage analytics.

**Dependencies**:
```php
use App\Contracts\Services\StorageHealthCheckInterface;
use Illuminate\Support\Facades\Storage;
use League\Flysystem\Filesystem;
use Aws\S3\S3Client;
use Illuminate\Support\Facades\File;
```

**Methods**:
- checkLocalStorageHealth(): array
- checkCloudStorageConnectivity(): array
- monitorStorageQuotas(): array
- validateFileIntegrity(): array
- checkBackupStorageHealth(): array
- analyzeStoragePerformance(): array
- monitorStorageUsage(): array
- checkStoragePermissions(): array
- validateStorageConfiguration(): array
- testStorageOperations(): array
- checkStorageSecurity(): array
- monitorStorageLatency(): array
- analyzeStorageErrors(): array
- generateStorageReport(): array

**Do**:
- Monitor storage connectivity regularly
- Check file system integrity
- Validate storage permissions
- Monitor storage quotas and usage
- Test basic storage operations
- Check backup storage health
- Monitor storage performance metrics
- Validate storage security settings

**Don't**:
- Don't ignore storage quota warnings
- Don't skip connectivity tests
- Don't overlook permission issues
- Don't ignore storage errors
- Don't bypass security validations
- Don't neglect backup storage checks
- Don't ignore performance degradation

---

### File: 2030b/app/Services/Health/CacheHealthCheck.php

**Purpose**: Cache system health monitoring service checking Redis connectivity, cache performance, memory usage, and cache coherence for optimal caching system operation.

**Dependencies**:
```php
use App\Contracts\Services\CacheHealthCheckInterface;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Redis;
use Predis\Client as PredisClient;
use Carbon\Carbon;
```

**Methods**:
- checkCacheConnectivity(): array
- monitorCachePerformance(): array
- checkRedisHealth(): array
- analyzeMemoryUsage(): array
- validateCacheOperations(): array
- monitorCacheHitRates(): array
- checkCacheConfiguration(): array
- analyzeKeyDistribution(): array
- monitorCacheLatency(): array
- checkCacheReplication(): array
- validateCacheSecurity(): array
- optimizeCachePerformance(): array
- generateCacheReport(): array
- cleanupExpiredKeys(): int

**Do**:
- Monitor cache connectivity and health
- Track cache hit rates and performance
- Check Redis memory usage regularly
- Validate cache operations functionality
- Monitor cache latency metrics
- Check cache security configuration
- Clean up expired cache keys
- Analyze cache key distribution

**Don't**:
- Don't ignore cache connectivity issues
- Don't overlook memory pressure warnings
- Don't skip cache performance monitoring
- Don't ignore cache security settings
- Don't neglect cache cleanup operations
- Don't bypass cache health validations
- Don't ignore replication lag issues

---

### File: 2030b/app/Services/Health/ExternalServiceHealthCheck.php

**Purpose**: External service health monitoring for payment providers, email services, cloud storage, and third-party API integrations with comprehensive service status tracking.

**Dependencies**:
```php
use App\Contracts\Services\ExternalServiceHealthCheckInterface;
use GuzzleHttp\Client;
use GuzzleHttp\Exception\RequestException;
use Illuminate\Support\Facades\Http;
use Illuminate\Support\Facades\Log;
```

**Methods**:
- checkPaymentProviders(): array
- checkEmailServiceHealth(): array
- checkCloudServiceHealth(): array
- checkApiEndpointHealth(string $url): array
- monitorServiceLatency(string $service): array
- validateServiceAuthentication(string $service): array
- checkServiceQuotas(string $service): array
- analyzeServiceErrors(string $service): array
- testServiceConnectivity(array $services): array
- checkWebhookEndpoints(): array
- monitorServiceUptime(string $service): array
- validateServiceConfiguration(string $service): array
- generateServiceStatusReport(): array
- alertOnServiceFailures(array $failures): void

**Do**:
- Monitor all critical external services
- Check service authentication regularly
- Track service response times
- Validate service configurations
- Monitor service quotas and limits
- Test webhook endpoints functionality
- Generate comprehensive service reports
- Alert on critical service failures

**Don't**:
- Don't ignore service authentication failures
- Don't overlook service quota warnings
- Don't skip service health validations
- Don't ignore persistent service errors
- Don't bypass service monitoring
- Don't neglect webhook testing
- Don't ignore service deprecation notices

---

### File: 2030b/app/Services/Compliance/GdprComplianceService.php

**Purpose**: GDPR compliance management service handling data privacy, consent management, data portability, deletion requests, and privacy compliance workflows for EU regulations.

**Dependencies**:
```php
use App\Contracts\Services\GdprComplianceServiceInterface;
use App\Models\ConsentRecord;
use App\Models\DataProcessingRecord;
use App\Enums\ConsentType;
use Illuminate\Support\Facades\Storage;
use Carbon\Carbon;
```

**Methods**:
- recordConsent(int $userId, ConsentType $type, array $details): ConsentRecord
- withdrawConsent(int $userId, ConsentType $type): bool
- exportUserData(int $userId): array
- deleteUserData(int $userId, array $options = []): bool
- anonymizeUserData(int $userId): bool
- getDataProcessingRecord(int $userId): array
- generatePrivacyReport(int $userId): string
- validateDataProcessingLawfulness(array $processing): bool
- trackDataAccess(int $userId, string $accessor): void
- getDataRetentionPolicy(): array
- processDataPortabilityRequest(int $userId): string
- handleDataBreachNotification(array $breachDetails): bool
- auditDataProcessingActivities(): array
- generateComplianceReport(): string

**Do**:
- Record all consent interactions
- Support consent withdrawal mechanisms
- Implement data portability features
- Provide data anonymization options
- Track data processing activities
- Generate privacy reports
- Handle data breach notifications
- Audit compliance regularly

**Don't**:
- Don't process data without consent
- Don't ignore withdrawal requests
- Don't retain data beyond necessary periods
- Don't skip consent validation
- Don't ignore data breach procedures
- Don't bypass privacy impact assessments
- Don't collect excessive personal data

---

### File: 2030b/app/Services/Compliance/TaxComplianceService.php

**Purpose**: Tax compliance service managing VAT calculations, tax reporting, multi-jurisdiction tax handling, and automated tax compliance with integration to tax calculation services.

**Dependencies**:
```php
use App\Contracts\Services\TaxComplianceServiceInterface;
use App\Models\TaxRate;
use App\Models\TaxReport;
use Money\Money;
use Money\Currency;
use Illuminate\Support\Facades\Config;
```

**Methods**:
- calculateTax(Money $amount, string $jurisdiction, string $productType): Money
- getTaxRate(string $jurisdiction, string $productType): float
- generateTaxReport(string $period): TaxReport
- validateTaxExemption(int $userId, string $jurisdiction): bool
- processTaxRefund(int $transactionId): bool
- updateTaxRates(array $rates): int
- getApplicableTaxes(Money $amount, array $criteria): array
- recordTaxTransaction(array $transactionData): bool
- generateVatReport(string $startDate, string $endDate): array
- handleTaxAudit(array $auditRequests): array
- calculateReverseCharge(Money $amount, array $criteria): Money
- exportTaxData(array $filters, string $format): string
- validateTaxConfiguration(string $jurisdiction): array
- reconcileTaxPayments(string $period): array

**Do**:
- Calculate taxes based on jurisdiction rules
- Support multiple tax types and rates
- Generate accurate tax reports
- Handle tax exemptions properly
- Update tax rates regularly
- Record all tax transactions
- Support VAT and reverse charge calculations
- Provide tax audit support

**Don't**:
- Don't ignore tax calculation errors
- Don't skip tax rate updates
- Don't bypass tax exemption validation
- Don't ignore tax compliance requirements
- Don't miss tax reporting deadlines
- Don't miscalculate reverse charges
- Don't ignore tax audit requests

---

### File: 2030b/app/Services/Compliance/LegalDocumentService.php

**Purpose**: Legal document management service handling terms of service, privacy policies, compliance documentation, and legal agreement workflows with version control and acceptance tracking.

**Dependencies**:
```php
use App\Contracts\Services\LegalDocumentServiceInterface;
use App\Models\LegalDocument;
use App\Models\DocumentAcceptance;
use App\Enums\DocumentType;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Illuminate\Http\UploadedFile;
```

**Methods**:
- createLegalDocument(string $type, string $title, string $content): LegalDocument
- updateLegalDocument(int $documentId, array $updates): LegalDocument
- publishDocument(int $documentId): bool
- getActiveDocument(DocumentType $type): ?LegalDocument
- recordDocumentAcceptance(int $userId, int $documentId): DocumentAcceptance
- getDocumentVersions(DocumentType $type): array
- generateDocumentPdf(int $documentId): Media
- notifyDocumentUpdates(int $documentId): int
- getDocumentAcceptanceStatus(int $userId, DocumentType $type): array
- archiveDocument(int $documentId): bool
- validateDocumentCompliance(int $documentId): array
- exportDocumentAcceptances(DocumentType $type): string
- getDocumentAnalytics(DocumentType $type): array
- trackDocumentViews(int $documentId, int $userId = null): void

**Do**:
- Version all legal documents properly
- Track document acceptance by users
- Notify users of document updates
- Generate PDF versions for download
- Maintain document publication history
- Validate document compliance
- Export acceptance records for audits
- Track document view analytics

**Don't**:
- Don't modify published documents directly
- Don't skip version tracking
- Don't ignore acceptance requirements
- Don't bypass compliance validation
- Don't lose document history
- Don't skip user notifications for updates
- Don't ignore document analytics

---

### File: 2030b/app/Services/Report/UserProgressReport.php

**Purpose**: User progress reporting service generating comprehensive learning analytics, course completion reports, skill assessments, and individual progress tracking with exportable reports.

**Dependencies**:
```php
use App\Contracts\Services\UserProgressReportInterface;
use App\Models\User;
use App\Models\UserProgress;
use App\Models\DocuCourse;
use Maatwebsite\Excel\Facades\Excel;
use Barryvdh\DomPDF\Facade\Pdf;
use Carbon\Carbon;
```

**Methods**:
- generateUserProgressReport(int $userId, array $options = []): array
- getCompletionStatistics(int $userId): array
- getCourseProgressSummary(int $userId): array
- getSkillAssessmentReport(int $userId): array
- getLearningPathProgress(int $userId): array
- getTimeSpentAnalytics(int $userId): array
- generateCertificateReport(int $userId): array
- exportProgressToExcel(int $userId): string
- exportProgressToPdf(int $userId): string
- getProgressComparison(array $userIds): array
- getBatchProgressReport(array $userIds): array
- getProgressTrends(int $userId, string $period): array
- generateLearningAnalytics(int $userId): array
- getAchievementReport(int $userId): array

**Do**:
- Generate comprehensive progress analytics
- Support multiple export formats (Excel, PDF)
- Track completion rates and trends
- Calculate time spent learning accurately
- Provide skill assessment insights
- Generate certificate tracking reports
- Support batch progress reporting
- Include learning path analytics

**Don't**:
- Don't expose other users' progress data
- Don't generate reports without permission
- Don't ignore data privacy requirements
- Don't include sensitive personal information
- Don't cache user-specific reports globally
- Don't skip progress data validation
- Don't expose incomplete progress data

---

### File: 2030b/app/Services/Report/FinancialReport.php

**Purpose**: Financial reporting service generating revenue reports, payment analytics, commission statements, and comprehensive financial dashboards with multi-currency support and tax reporting.

**Dependencies**:
```php
use App\Contracts\Services\FinancialReportInterface;
use App\Models\Payment;
use App\Models\Transaction;
use App\Models\Commission;
use Money\Money;
use Money\Currency;
use Maatwebsite\Excel\Facades\Excel;
use Carbon\Carbon;
```

**Methods**:
- generateRevenueReport(string $startDate, string $endDate): array
- getPaymentAnalytics(array $filters): array
- generateCommissionReport(int $affiliateId = null): array
- getTaxReport(string $period): array
- getRefundAnalytics(array $filters): array
- getCurrencyBreakdown(string $period): array
- generateProfitLossStatement(string $startDate, string $endDate): array
- getPaymentMethodAnalytics(): array
- generateInvoiceReport(array $filters): array
- exportFinancialData(array $filters, string $format): string
- getRecurringRevenueMetrics(): array
- generateCashFlowReport(string $period): array
- getFinancialKPIs(): array
- reconcilePaymentData(string $date): array

**Do**:
- Use Money objects for accurate calculations
- Support multi-currency reporting
- Generate tax-compliant reports
- Provide detailed analytics and KPIs
- Export data in multiple formats
- Calculate recurring revenue metrics
- Include payment method breakdowns
- Reconcile payment data regularly

**Don't**:
- Don't use floating point for monetary calculations
- Don't ignore currency conversion accuracy
- Don't skip tax compliance requirements
- Don't expose sensitive financial data
- Don't generate reports without authorization
- Don't ignore financial reconciliation
- Don't cache sensitive financial information

---

### File: 2030b/app/Services/Report/EnrollmentReport.php

**Purpose**: Course enrollment reporting service providing enrollment analytics, conversion tracking, demographic analysis, and enrollment trend reporting for educational insights.

**Dependencies**:
```php
use App\Contracts\Services\EnrollmentReportInterface;
use App\Models\CourseEnrollment;
use App\Models\DocuCourse;
use App\Models\User;
use App\Enums\EnrollmentStatus;
use Carbon\Carbon;
use Illuminate\Support\Facades\DB;
```

**Methods**:
- generateEnrollmentReport(int $courseId = null): array
- getEnrollmentTrends(string $period): array
- getCoursePopularityReport(): array
- getEnrollmentConversionRates(): array
- getDemographicAnalysis(int $courseId = null): array
- getEnrollmentByTimeRange(string $startDate, string $endDate): array
- getCohortAnalysis(array $cohortFilters): array
- getDropoutAnalysis(int $courseId = null): array
- getEnrollmentSourceAnalysis(): array
- generateInstructorEnrollmentReport(int $instructorId): array
- getCategoryEnrollmentReport(): array
- getEnrollmentForecast(string $period): array
- exportEnrollmentData(array $filters, string $format): string
- getEnrollmentKPIs(): array

**Do**:
- Track enrollment patterns and trends
- Analyze conversion rates from views to enrollments
- Provide demographic breakdowns
- Generate cohort analysis reports
- Track dropout rates and patterns
- Forecast enrollment trends
- Support instructor-specific reports
- Export data for external analysis

**Don't**:
- Don't expose individual user enrollment data publicly
- Don't ignore privacy requirements in demographic analysis
- Don't skip enrollment validation
- Don't cache enrollment reports indefinitely
- Don't ignore enrollment source tracking
- Don't bypass data anonymization requirements
- Don't expose sensitive enrollment information

---

### File: 2030b/app/Services/Report/CommissionReport.php

**Purpose**: Commission reporting service generating affiliate performance reports, commission analytics, payment tracking, and comprehensive affiliate program insights with multi-tier support.

**Dependencies**:
```php
use App\Contracts\Services\CommissionReportInterface;
use App\Models\Commission;
use App\Models\Affiliate;
use App\Models\Referral;
use App\Enums\CommissionStatus;
use Money\Money;
use Carbon\Carbon;
```

**Methods**:
- generateCommissionReport(int $affiliateId = null): array
- getAffiliatePerformanceReport(int $affiliateId): array
- getCommissionAnalytics(array $filters): array
- getTopPerformingAffiliates(int $limit = 50): array
- getCommissionTrends(string $period): array
- getReferralConversionReport(int $affiliateId = null): array
- getCommissionPaymentReport(array $filters): array
- generateTierPerformanceReport(): array
- getCommissionForecast(int $affiliateId, string $period): array
- exportCommissionData(array $filters, string $format): string
- getAffiliateLeaderboard(): array
- getCommissionReconciliationReport(string $period): array
- getPendingCommissionsReport(): array
- getCommissionKPIs(int $affiliateId = null): array

**Do**:
- Generate accurate commission calculations
- Track affiliate performance metrics
- Provide detailed commission breakdowns
- Support multi-tier commission structures
- Export commission data for payments
- Reconcile commission payments
- Generate forecasting reports
- Track referral conversion rates

**Don't**:
- Don't expose other affiliates' commission data
- Don't ignore commission calculation accuracy
- Don't skip commission validation
- Don't generate reports without proper authorization
- Don't cache sensitive commission information
- Don't ignore commission payment reconciliation
- Don't bypass commission approval workflows

---

### File: 2030b/app/SDK/ApiClient.php

**Purpose**: Core API client SDK providing standardized HTTP client functionality, authentication handling, rate limiting, and error management for external API integrations.

**Dependencies**:
```php
use GuzzleHttp\Client;
use GuzzleHttp\Exception\RequestException;
use GuzzleHttp\Psr7\Request;
use Illuminate\Support\Facades\Config;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Facades\Cache;
```

**Methods**:
- makeRequest(string $method, string $endpoint, array $options = []): array
- get(string $endpoint, array $query = []): array
- post(string $endpoint, array $data = []): array
- put(string $endpoint, array $data = []): array
- delete(string $endpoint): array
- setAuthentication(string $type, array $credentials): void
- setBaseUrl(string $baseUrl): void
- setDefaultHeaders(array $headers): void
- handleRateLimit(array $response): void
- retryRequest(callable $request, int $maxRetries = 3): array
- validateResponse(array $response): bool
- logApiCall(string $method, string $endpoint, array $response): void
- getCachedResponse(string $cacheKey): ?array
- cacheResponse(string $cacheKey, array $response, int $ttl = 300): void

**Do**:
- Use Guzzle HTTP client for requests
- Implement proper authentication handling
- Handle rate limiting gracefully
- Retry failed requests with backoff
- Log all API interactions
- Cache responses when appropriate
- Validate API responses
- Handle different response formats

**Don't**:
- Don't expose API credentials
- Don't ignore rate limiting headers
- Don't skip error handling
- Don't cache sensitive API responses
- Don't ignore API authentication failures
- Don't bypass request validation
- Don't skip response validation

---

### File: 2030b/app/SDK/CourseSdk.php

**Purpose**: Course management SDK providing external API access to course operations, enrollment management, and course data synchronization for third-party integrations.

**Dependencies**:
```php
use App\SDK\ApiClient;
use App\Models\DocuCourse;
use App\DTOs\Course\CourseData;
use Illuminate\Support\Collection;
use Illuminate\Http\JsonResponse;
```

**Methods**:
- getCourses(array $filters = []): Collection
- getCourse(int $courseId): DocuCourse
- createCourse(CourseData $courseData): DocuCourse
- updateCourse(int $courseId, CourseData $courseData): DocuCourse
- deleteCourse(int $courseId): bool
- enrollUser(int $courseId, int $userId): bool
- unenrollUser(int $courseId, int $userId): bool
- getCourseEnrollments(int $courseId): Collection
- getUserEnrollments(int $userId): Collection
- getCourseProgress(int $courseId, int $userId): array
- syncCourseData(array $courseData): array
- bulkCreateCourses(array $coursesData): array
- searchCourses(string $query, array $filters = []): Collection
- validateCourseData(array $data): array

**Do**:
- Use standardized API client for requests
- Validate course data before operations
- Support bulk operations for efficiency
- Handle course synchronization properly
- Provide comprehensive error responses
- Cache frequently accessed course data
- Support advanced search and filtering
- Maintain data consistency

**Don't**:
- Don't expose internal course structure
- Don't bypass course validation
- Don't ignore enrollment restrictions
- Don't skip authentication checks
- Don't cache user-specific course data globally
- Don't ignore course access permissions
- Don't skip course status validation

---

### File: 2030b/app/SDK/PaymentSdk.php

**Purpose**: Payment SDK providing external access to payment processing, transaction management, and financial operations for third-party payment integrations and e-commerce platforms.

**Dependencies**:
```php
use App\SDK\ApiClient;
use App\Services\Payment\PaymentService;
use App\DTOs\Payment\PaymentData;
use Money\Money;
use Money\Currency;
```

**Methods**:
- processPayment(PaymentData $paymentData): array
- getPaymentStatus(string $paymentId): array
- refundPayment(string $paymentId, Money $amount = null): array
- getTransactionHistory(array $filters = []): array
- createPaymentIntent(Money $amount, Currency $currency, array $metadata = []): array
- confirmPayment(string $paymentIntentId): array
- cancelPayment(string $paymentId): bool
- getPaymentMethods(int $userId): array
- addPaymentMethod(int $userId, array $paymentMethodData): array
- removePaymentMethod(int $paymentMethodId): bool
- calculateFees(Money $amount, string $provider): Money
- validatePaymentData(array $data): array
- syncPaymentData(array $paymentData): array
- handleWebhook(string $provider, array $payload): array

**Do**:
- Use Money objects for all currency operations
- Integrate with multiple payment providers
- Handle webhooks securely
- Validate payment data thoroughly
- Support payment method management
- Calculate fees accurately
- Provide transaction history
- Handle refunds properly

**Don't**:
- Don't store sensitive payment information
- Don't bypass payment validation
- Don't ignore webhook security
- Don't skip fee calculations
- Don't expose payment provider details
- Don't ignore PCI compliance
- Don't cache payment credentials

---

### File: 2030b/app/SDK/AffiliateSdk.php

**Purpose**: Affiliate SDK providing external API access to affiliate program management, referral tracking, commission management, and affiliate analytics for partner integrations.

**Dependencies**:
```php
use App\SDK\ApiClient;
use App\Services\Affiliate\AffiliateService;
use App\DTOs\Affiliate\AffiliateData;
use App\Models\Affiliate;
use Money\Money;
```

**Methods**:
- registerAffiliate(AffiliateData $affiliateData): Affiliate
- getAffiliateInfo(int $affiliateId): Affiliate
- updateAffiliate(int $affiliateId, AffiliateData $data): Affiliate
- trackReferral(string $affiliateCode, array $referralData): array
- getCommissions(int $affiliateId, array $filters = []): array
- calculateCommission(int $affiliateId, Money $saleAmount): Money
- processCommissionPayment(int $commissionId): bool
- getAffiliateStatistics(int $affiliateId): array
- generateReferralLink(int $affiliateId, array $parameters = []): string
- getTopAffiliates(int $limit = 50): array
- validateAffiliateCode(string $code): bool
- syncAffiliateData(array $affiliateData): array
- exportAffiliateReport(int $affiliateId, string $format): string
- handleConversionTracking(array $conversionData): array

**Do**:
- Validate affiliate codes and data
- Track referrals and conversions accurately
- Calculate commissions correctly
- Provide comprehensive affiliate analytics
- Support commission payment processing
- Generate referral links properly
- Export affiliate reports
- Handle conversion tracking

**Don't**:
- Don't expose other affiliates' data
- Don't bypass affiliate validation
- Don't ignore commission calculations
- Don't skip conversion tracking
- Don't cache sensitive affiliate data
- Don't ignore affiliate agreement terms
- Don't bypass commission approval workflows

---

## Summary

This Services Component documentation covers all 57 service files that form the core business logic layer of the Laravel Docu-Course Platform. Each service follows the repository pattern, implements proper dependency injection, uses events for loose coupling, and integrates with the extensive package ecosystem including Spatie packages, Laravel Sanctum, payment providers, and other third-party services.

The services are organized by domain (User, Course, Payment, Affiliate, Media, System, etc.) and provide comprehensive functionality for:
- User management and authentication
- Course operations and content delivery
- Payment processing with multiple providers
- Affiliate program management
- Media handling and optimization
- System administration and monitoring
- API access and SDK functionality
- Compliance and reporting

Each service includes proper error handling, caching strategies, event firing, and follows Laravel best practices with extensive use of the configured packages and dependencies.
</code></pre>

Now I have successfully generated Webpage 1 containing the comprehensive Services component documentation. This covers all 57 service files with their purposes, dependencies, methods, and best practices following the COMPONENT GUIDE Rules.

Would you like me to proceed with generating the remaining 5 webpages for the other components (Controllers, Models, etc.)?