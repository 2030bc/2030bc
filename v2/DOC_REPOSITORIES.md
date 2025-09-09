# DOC_REPOSITORIES.md

## Repository Pattern Architecture

The Repository component provides data access abstraction layer between the business logic and data mapping layers. All repositories implement contracts and use DTOs for data transfer.

---

## File: 2030b/app/Repositories/Eloquent/BaseRepository.php

**Purpose**: Abstract base repository providing common CRUD operations and query building methods for all domain repositories.

**Dependencies**:
```php
use App\Contracts\Repositories\BaseRepositoryInterface;
use App\Foundation\Core\BaseDto;
use App\Enums\UserLevel;
use App\Enums\UserStatus;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Collection;
use Illuminate\Pagination\LengthAwarePaginator;
use Illuminate\Support\Collection as SupportCollection;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\DB;
```

**Methods**:
- find(int $id): ?Model
- findByUuid(string $uuid): ?Model  
- create(BaseDto $dto): Model
- update(int $id, BaseDto $dto): Model
- delete(int $id): bool
- softDelete(int $id): bool
- restore(int $id): bool
- paginate(int $perPage = 15): LengthAwarePaginator
- findWhere(array $criteria): Collection
- findWhereIn(string $field, array $values): Collection
- count(array $criteria = []): int
- exists(array $criteria): bool
- firstOrCreate(array $attributes, array $values = []): Model
- updateOrCreate(array $attributes, array $values = []): Model
- buildQuery(array $filters = []): Builder
- applyFilters(Builder $query, array $filters): Builder
- applySorting(Builder $query, array $sorts): Builder
- cacheQuery(string $key, callable $callback, int $ttl = 3600): mixed
- clearCache(array $tags = []): bool

**Do**:
- Always use DTOs for data transfer between layers
- Implement caching for frequently accessed data
- Use UUID for external references and ID for internal joins
- Apply proper query filters and pagination
- Use database transactions for multiple operations
- Implement soft deletes where appropriate
- Use eager loading to prevent N+1 queries
- Apply consistent naming conventions

**Don't**:
- Don't expose Eloquent models directly to services
- Don't implement business logic in repositories
- Don't use raw SQL without proper binding
- Don't forget to clear relevant caches on updates
- Don't ignore database indexes for performance
- Don't use select(*) for large datasets
- Don't bypass validation when creating/updating

---

## File: 2030b/app/Repositories/Eloquent/UserRepository.php

**Purpose**: User data access layer handling user CRUD operations, authentication queries, and user progression tracking.

**Dependencies**:
```php
use App\Repositories\Eloquent\BaseRepository;
use App\Contracts\Repositories\UserRepositoryInterface;
use App\Models\User;
use App\Models\User\UserProfile;
use App\Models\User\UserLevel;
use App\Models\User\UserProgress;
use App\DTOs\User\ProfileData;
use App\DTOs\User\ProgressData;
use App\Enums\UserStatus;
use App\Enums\UserLevel as UserLevelEnum;
use Illuminate\Support\Facades\Hash;
use Spatie\Permission\Models\Role;
```

**Methods**:
- findByEmail(string $email): ?User
- findByUsername(string $username): ?User
- findByReferralCode(string $code): ?User
- createUser(ProfileData $profileData): User
- updateProfile(int $userId, ProfileData $profileData): User
- updateUserLevel(int $userId, int $level): User
- addPoints(int $userId, int $points): User
- getByLevel(int $level): Collection
- getActiveUsers(): Collection
- getUsersByStatus(UserStatus $status): Collection
- getUserProgress(int $userId): Collection
- updateProgress(int $userId, ProgressData $progressData): UserProgress
- getUserAchievements(int $userId): Collection
- searchUsers(string $query): Collection
- getUserStats(int $userId): array
- getReferrals(int $userId): Collection
- banUser(int $userId, string $reason): User
- activateUser(int $userId): User
- verifyEmail(int $userId): User
- updateLastActive(int $userId): User

**Do**:
- Hash passwords using Laravel's Hash facade
- Validate email uniqueness before creation
- Use soft deletes for user accounts
- Cache user permissions and roles
- Track user activity and progress
- Implement proper user level progression
- Use UUID for public user references
- Apply privacy settings in queries

**Don't**:
- Don't store plain text passwords
- Don't expose sensitive user data
- Don't hard delete user accounts
- Don't bypass email verification
- Don't ignore user privacy settings
- Don't cache sensitive information
- Don't allow duplicate usernames/emails

---

## File: 2030b/app/Repositories/Eloquent/CourseRepository.php

**Purpose**: Course data access layer managing docu-courses, episodes, categories, and enrollment operations.

**Dependencies**:
```php
use App\Repositories\Eloquent\BaseRepository;
use App\Contracts\Repositories\CourseRepositoryInterface;
use App\Models\Course\DocuCourse;
use App\Models\Course\Episode;
use App\Models\Course\EpisodePart;
use App\Models\Course\Category;
use App\Models\Course\CourseUpload;
use App\DTOs\Course\CourseData;
use App\DTOs\Course\EpisodeData;
use App\DTOs\Course\CategoryData;
use App\Enums\CourseStatus;
use App\Enums\ContentAccessLevel;
use App\Enums\DifficultyLevel;
use Illuminate\Support\Facades\Storage;
```

**Methods**:
- findByAlias(string $alias): ?DocuCourse
- getPublishedCourses(): Collection
- getCoursesByCategory(int $categoryId): Collection
- getCoursesByAuthor(int $authorId): Collection
- getCoursesByDifficulty(DifficultyLevel $level): Collection
- createCourse(CourseData $courseData): DocuCourse
- updateCourse(int $courseId, CourseData $courseData): DocuCourse
- publishCourse(int $courseId): DocuCourse
- archiveCourse(int $courseId): DocuCourse
- getEpisodes(int $courseId): Collection
- createEpisode(int $courseId, EpisodeData $episodeData): Episode
- updateEpisode(int $episodeId, EpisodeData $episodeData): Episode
- getEpisodeParts(int $episodeId): Collection
- searchCourses(string $query, array $filters = []): Collection
- getFeaturedCourses(int $limit = 10): Collection
- getPopularCourses(int $limit = 10): Collection
- getRecentCourses(int $limit = 10): Collection
- getCourseStats(int $courseId): array
- enrollUser(int $userId, int $courseId): bool
- isUserEnrolled(int $userId, int $courseId): bool
- getUserCourses(int $userId): Collection
- rateCourse(int $userId, int $courseId, int $rating): bool

**Do**:
- Use slugs/aliases for SEO-friendly URLs
- Implement proper access level checking
- Cache course metadata and statistics
- Use file storage for course content
- Track course views and enrollments
- Implement course versioning
- Use eager loading for course relationships
- Apply content filtering by user level

**Don't**:
- Don't expose draft courses publicly
- Don't allow unauthorized access to premium content
- Don't ignore file size limits
- Don't bypass content validation
- Don't cache large course files in memory
- Don't allow duplicate course aliases
- Don't delete courses with enrollments

---

## File: 2030b/app/Repositories/Eloquent/PaymentRepository.php

**Purpose**: Payment data access layer handling transactions, invoices, payment methods, and currency operations.

**Dependencies**:
```php
use App\Repositories\Eloquent\BaseRepository;
use App\Contracts\Repositories\PaymentRepositoryInterface;
use App\Models\Payment\Transaction;
use App\Models\Payment\PaymentMethod;
use App\Models\Payment\Invoice;
use App\Models\Payment\Currency;
use App\DTOs\Payment\PaymentData;
use App\DTOs\Payment\TransactionData;
use App\DTOs\Payment\InvoiceData;
use App\Enums\PaymentStatus;
use App\Enums\PaymentProvider;
use App\Enums\TransactionType;
use App\Enums\CurrencyType;
use Money\Money;
use Money\Currency as MoneyCurrency;
```

**Methods**:
- createTransaction(TransactionData $transactionData): Transaction
- updateTransactionStatus(int $transactionId, PaymentStatus $status): Transaction
- findByProviderTransactionId(string $providerId): ?Transaction
- getUserTransactions(int $userId): Collection
- getTransactionsByStatus(PaymentStatus $status): Collection
- getTransactionsByProvider(PaymentProvider $provider): Collection
- createInvoice(InvoiceData $invoiceData): Invoice
- updateInvoice(int $invoiceId, InvoiceData $invoiceData): Invoice
- markInvoicePaid(int $invoiceId): Invoice
- getUserInvoices(int $userId): Collection
- getPendingInvoices(): Collection
- getPaymentMethods(int $userId): Collection
- addPaymentMethod(int $userId, array $methodData): PaymentMethod
- setDefaultPaymentMethod(int $userId, int $methodId): bool
- removePaymentMethod(int $methodId): bool
- getCurrency(string $code): ?Currency
- getActiveCurrencies(): Collection
- updateExchangeRates(): bool
- convertAmount(Money $amount, string $toCurrency): Money
- getPaymentStats(array $filters = []): array
- getRevenueStats(array $filters = []): array
- processRefund(int $transactionId, Money $amount): Transaction

**Do**:
- Use Money objects for precise calculations
- Store amounts in smallest currency units
- Validate currency codes against supported list
- Implement idempotency for payment operations
- Log all payment activities
- Use database transactions for payment flows
- Encrypt sensitive payment data
- Handle currency conversions properly

**Don't**:
- Don't use floats for monetary calculations
- Don't store payment credentials in database
- Don't bypass payment validation
- Don't ignore webhook signatures
- Don't process duplicate payments
- Don't expose payment method details
- Don't ignore PCI compliance requirements

---

## File: 2030b/app/Repositories/Eloquent/AffiliateRepository.php

**Purpose**: Affiliate program data access layer managing affiliates, referrals, conversions, and commission tracking.

**Dependencies**:
```php
use App\Repositories\Eloquent\BaseRepository;
use App\Contracts\Repositories\AffiliateRepositoryInterface;
use App\Models\Affiliate\Affiliate;
use App\Models\Affiliate\ReferralCode;
use App\Models\Affiliate\AffiliateClick;
use App\Models\Affiliate\Conversion;
use App\Models\Affiliate\Commission;
use App\Models\Affiliate\SocialShare;
use App\DTOs\Affiliate\AffiliateData;
use App\DTOs\Affiliate\CommissionData;
use App\DTOs\Affiliate\ConversionData;
use App\Enums\AffiliateStatus;
use App\Enums\CommissionStatus;
use App\Enums\ConversionType;
use App\Enums\SocialPlatform;
```

**Methods**:
- createAffiliate(int $userId, AffiliateData $affiliateData): Affiliate
- updateAffiliate(int $affiliateId, AffiliateData $affiliateData): Affiliate
- approveAffiliate(int $affiliateId): Affiliate
- suspendAffiliate(int $affiliateId, string $reason): Affiliate
- findByReferralCode(string $code): ?Affiliate
- generateReferralCode(int $affiliateId): ReferralCode
- trackClick(int $affiliateId, array $clickData): AffiliateClick
- recordConversion(ConversionData $conversionData): Conversion
- calculateCommission(int $conversionId): Commission
- getAffiliateStats(int $affiliateId): array
- getCommissions(int $affiliateId, array $filters = []): Collection
- getPendingCommissions(): Collection
- approveCommission(int $commissionId): Commission
- payCommission(int $commissionId): Commission
- getTopAffiliates(int $limit = 10): Collection
- getClicksReport(int $affiliateId, array $dateRange): array
- getConversionsReport(int $affiliateId, array $dateRange): array
- trackSocialShare(int $userId, SocialPlatform $platform, array $shareData): SocialShare
- getReferralStats(string $referralCode): array
- getAffiliatePerformance(int $affiliateId, string $period): array

**Do**:
- Track all affiliate interactions
- Validate referral code authenticity
- Implement proper commission calculations
- Use fraud detection mechanisms
- Cache affiliate statistics
- Track conversion attribution properly
- Implement payout schedules
- Monitor affiliate performance

**Don't**:
- Don't allow self-referrals
- Don't pay commissions without validation
- Don't expose affiliate earnings publicly
- Don't ignore click fraud detection
- Don't bypass commission approval process
- Don't allow duplicate referral codes
- Don't ignore payout thresholds

---

## File: 2030b/app/Repositories/RepositoryServiceProvider.php

**Purpose**: Service provider for binding repository interfaces to their concrete implementations in the IoC container.

**Dependencies**:
```php
use Illuminate\Support\ServiceProvider;
use App\Contracts\Repositories\BaseRepositoryInterface;
use App\Contracts\Repositories\UserRepositoryInterface;
use App\Contracts\Repositories\CourseRepositoryInterface;
use App\Contracts\Repositories\PaymentRepositoryInterface;
use App\Contracts\Repositories\AffiliateRepositoryInterface;
use App\Repositories\Eloquent\BaseRepository;
use App\Repositories\Eloquent\UserRepository;
use App\Repositories\Eloquent\CourseRepository;
use App\Repositories\Eloquent\PaymentRepository;
use App\Repositories\Eloquent\AffiliateRepository;
```

**Methods**:
- register(): void
- boot(): void
- bindRepositories(): void
- bindBaseRepository(): void
- bindDomainRepositories(): void

**Do**:
- Bind all repository interfaces consistently
- Use singleton bindings for performance
- Register repositories before services
- Follow naming conventions
- Document all bindings
- Use conditional binding when needed

**Don't**:
- Don't bind concrete classes directly
- Don't ignore interface contracts
- Don't create circular dependencies
- Don't register repositories multiple times