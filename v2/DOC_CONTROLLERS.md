# DOC_CONTROLLERS.md

## Laravel Docu-Course Platform - Controllers Component Documentation

**Project:** Laravel Docu-Course Platform with Modules Marketplace  
**Laravel Version:** 12.24.0 (EXACT VERSION REQUIRED)  
**PHP Version:** 8.2+ (minimum 8.2.0)  

---

### File: 2030b/app/Http/Controllers/Auth/AuthenticatedSessionController.php

**Purpose**: Manages user authentication sessions, handling login, logout, and session state validation for web and API authentication flows.

**Dependencies**:
```php
use App\Http\Requests\Auth\LoginRequest;
use App\Providers\RouteServiceProvider;
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;
use Illuminate\View\View;
use App\Services\User\UserService;
use App\Enums\UserStatus;
use Illuminate\Support\Facades\RateLimiter;
use Illuminate\Validation\ValidationException;
```

**Methods**:
- create(): View
- store(LoginRequest $request): RedirectResponse
- destroy(Request $request): RedirectResponse
- validateLoginAttempt(Request $request): bool
- logAuthenticationAttempt(string $email, bool $successful): void
- checkUserStatus(User $user): bool
- handleFailedLogin(Request $request): ValidationException

**Do**:
- Rate limit login attempts per IP and email
- Log all authentication attempts for security
- Validate user status before allowing login
- Clear session data completely on logout
- Use secure session configuration
- Implement CSRF protection
- Redirect to intended URL after login

**Don't**:
- Don't expose sensitive authentication errors
- Don't allow login without email verification
- Don't ignore rate limiting
- Don't store credentials in plain text
- Don't bypass user status checks

---

### File: 2030b/app/Http/Controllers/Auth/RegisteredUserController.php

**Purpose**: Handles new user registration with email verification, user level assignment, and welcome notification dispatch.

**Dependencies**:
```php
use App\Http\Requests\Auth\RegisterRequest;
use App\Models\User;
use App\Providers\RouteServiceProvider;
use Illuminate\Auth\Events\Registered;
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Hash;
use Illuminate\View\View;
use App\Services\User\UserService;
use App\DTOs\User\ProfileData;
use App\Enums\UserLevel;
use App\Events\UserRegistered;
use App\Notifications\WelcomeNotification;
use Illuminate\Support\Str;
```

**Methods**:
- create(): View
- store(RegisterRequest $request): RedirectResponse
- assignDefaultLevel(User $user): void
- sendWelcomeNotification(User $user): void
- generateReferralCode(User $user): string
- validateInvitationCode(string $code): bool
- processReferralRegistration(User $user, string $referralCode): void

**Do**:
- Validate all registration data thoroughly
- Send email verification immediately
- Assign default user level and permissions
- Generate unique referral codes
- Process referral rewards properly
- Send welcome notifications
- Log registration events
- Hash passwords securely

**Don't**:
- Don't auto-login without email verification
- Don't skip password strength validation
- Don't allow duplicate email addresses
- Don't ignore referral code processing
- Don't expose internal user IDs

---

### File: 2030b/app/Http/Controllers/Auth/PasswordResetLinkController.php

**Purpose**: Manages password reset link generation and validation with rate limiting and secure token generation.

**Dependencies**:
```php
use App\Http\Requests\Auth\PasswordResetLinkRequest;
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Password;
use Illuminate\View\View;
use Illuminate\Support\Facades\RateLimiter;
use App\Services\User\UserService;
use App\Notifications\PasswordResetNotification;
use Illuminate\Support\Facades\Log;
```

**Methods**:
- create(): View
- store(PasswordResetLinkRequest $request): RedirectResponse
- validateResetAttempt(string $email): bool
- sendResetLink(string $email): string
- logResetAttempt(string $email, bool $successful): void
- checkRateLimit(string $email): bool
- generateSecureToken(): string

**Do**:
- Rate limit reset requests per email and IP
- Use secure token generation
- Set reasonable token expiration times
- Log all reset attempts
- Validate email exists before sending
- Use encrypted email in reset links
- Provide consistent response messages

**Don't**:
- Don't reveal if email exists in system
- Don't allow unlimited reset attempts
- Don't use predictable token patterns
- Don't send reset links to unverified emails
- Don't expose reset token in logs

---

### File: 2030b/app/Http/Controllers/Auth/NewPasswordController.php

**Purpose**: Processes password reset completion with token validation, password updates, and security event logging.

**Dependencies**:
```php
use App\Http\Requests\Auth\NewPasswordRequest;
use Illuminate\Auth\Events\PasswordReset;
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\Password;
use Illuminate\Support\Str;
use Illuminate\View\View;
use Illuminate\Validation\ValidationException;
use App\Services\User\UserService;
use App\Events\PasswordChanged;
use Illuminate\Support\Facades\Log;
```

**Methods**:
- create(Request $request): View
- store(NewPasswordRequest $request): RedirectResponse
- validateResetToken(string $token, string $email): bool
- updatePassword(User $user, string $password): bool
- invalidateExistingSessions(User $user): void
- logPasswordChange(User $user): void
- sendPasswordChangeNotification(User $user): void

**Do**:
- Validate reset tokens thoroughly
- Hash new passwords securely
- Invalidate all existing sessions
- Log password change events
- Send confirmation notifications
- Update password change timestamp
- Fire password reset events

**Don't**:
- Don't reuse password reset tokens
- Don't allow weak passwords
- Don't skip token validation
- Don't forget session invalidation
- Don't expose token details in responses

---

### File: 2030b/app/Http/Controllers/Auth/TwoFactorController.php

**Purpose**: Manages two-factor authentication setup, validation, and backup code generation for enhanced account security.

**Dependencies**:
```php
use App\Http\Requests\Auth\TwoFactorSetupRequest;
use App\Http\Requests\Auth\TwoFactorVerifyRequest;
use Illuminate\Http\Request;
use Illuminate\Http\RedirectResponse;
use Illuminate\View\View;
use PragmaRX\Google2FA\Google2FA;
use Illuminate\Support\Facades\Crypt;
use App\Services\User\UserService;
use App\Events\TwoFactorEnabled;
use App\Events\TwoFactorDisabled;
use App\Notifications\TwoFactorEnabledNotification;
```

**Methods**:
- setup(): View
- enable(TwoFactorSetupRequest $request): RedirectResponse
- disable(Request $request): RedirectResponse
- verify(TwoFactorVerifyRequest $request): RedirectResponse
- generateSecretKey(): string
- generateQrCode(User $user, string $secret): string
- generateBackupCodes(): array
- validateTwoFactorCode(User $user, string $code): bool
- useBackupCode(User $user, string $code): bool

**Do**:
- Generate cryptographically secure secret keys
- Store encrypted 2FA secrets
- Provide QR codes for easy setup
- Generate secure backup codes
- Validate codes with time window tolerance
- Invalidate used backup codes
- Send setup confirmation notifications
- Log 2FA events for security

**Don't**:
- Don't store 2FA secrets in plain text
- Don't allow reuse of backup codes
- Don't skip code validation
- Don't expose secret keys in responses
- Don't allow setup without password confirmation

---

### File: 2030b/app/Http/Controllers/Auth/SocialAuthController.php

**Purpose**: Handles OAuth authentication with third-party providers (Google, Facebook, GitHub) and account linking functionality.

**Dependencies**:
```php
use Laravel\Socialite\Facades\Socialite;
use App\Models\User;
use App\Services\User\UserService;
use Illuminate\Http\RedirectResponse;
use Illuminate\Support\Facades\Auth;
use App\DTOs\User\ProfileData;
use App\Events\SocialAccountLinked;
use App\Enums\SocialProvider;
use Illuminate\Support\Str;
use Exception;
```

**Methods**:
- redirect(string $provider): RedirectResponse
- callback(string $provider): RedirectResponse
- handleProviderCallback(string $provider): User
- findOrCreateUser(array $socialUser, string $provider): User
- linkExistingAccount(User $user, array $socialUser, string $provider): void
- unlinkSocialAccount(User $user, string $provider): RedirectResponse
- validateProvider(string $provider): bool
- extractUserData(object $socialUser): ProfileData

**Do**:
- Validate supported OAuth providers
- Handle OAuth callback errors gracefully
- Link social accounts to existing users
- Store provider-specific user data
- Validate email addresses from providers
- Fire social account events
- Log OAuth authentication attempts
- Implement provider-specific scopes

**Don't**:
- Don't trust all provider data blindly
- Don't allow account takeover via OAuth
- Don't skip email verification for social logins
- Don't expose OAuth tokens in responses
- Don't ignore provider-specific errors

---

### File: 2030b/app/Http/Controllers/Auth/EmailVerificationController.php

**Purpose**: Manages email verification process, resend verification emails, and email change confirmation workflows.

**Dependencies**:
```php
use App\Http\Requests\Auth\EmailVerificationRequest;
use App\Providers\RouteServiceProvider;
use Illuminate\Auth\Events\Verified;
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
use Illuminate\View\View;
use App\Services\User\UserService;
use App\Notifications\EmailVerificationNotification;
use Illuminate\Support\Facades\URL;
use Illuminate\Support\Facades\RateLimiter;
```

**Methods**:
- notice(): View
- verify(EmailVerificationRequest $request): RedirectResponse
- resend(Request $request): RedirectResponse
- changeEmail(Request $request): RedirectResponse
- generateVerificationUrl(User $user): string
- validateVerificationLink(Request $request): bool
- markEmailAsVerified(User $user): void
- checkResendRateLimit(User $user): bool

**Do**:
- Rate limit verification resend requests
- Use signed URLs for verification links
- Set reasonable link expiration times
- Fire verification events
- Update user email verification status
- Log verification attempts
- Redirect to intended destination after verification

**Don't**:
- Don't allow unlimited resend requests
- Don't use predictable verification tokens
- Don't skip signature validation
- Don't verify already verified emails
- Don't expose verification URLs in logs

---

### File: 2030b/app/Http/Controllers/User/ProfileController.php

**Purpose**: Manages user profile information, avatar uploads, privacy settings, and profile data updates with validation.

**Dependencies**:
```php
use App\Http\Requests\User\ProfileUpdateRequest;
use App\Http\Requests\User\AvatarUploadRequest;
use App\Services\User\UserService;
use App\Services\Media\MediaService;
use App\DTOs\User\ProfileData;
use Illuminate\Http\Request;
use Illuminate\Http\RedirectResponse;
use Illuminate\View\View;
use App\Events\ProfileUpdated;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
```

**Methods**:
- show(Request $request): View
- edit(Request $request): View
- update(ProfileUpdateRequest $request): RedirectResponse
- uploadAvatar(AvatarUploadRequest $request): RedirectResponse
- removeAvatar(Request $request): RedirectResponse
- updatePrivacySettings(Request $request): RedirectResponse
- exportProfileData(Request $request): RedirectResponse
- deactivateAccount(Request $request): RedirectResponse

**Do**:
- Validate all profile data thoroughly
- Optimize and resize uploaded avatars
- Implement privacy controls
- Fire profile update events
- Log sensitive profile changes
- Provide data export functionality
- Handle account deactivation properly

**Don't**:
- Don't allow unauthorized profile access
- Don't skip image validation for avatars
- Don't expose sensitive profile data
- Don't allow profile updates without authentication
- Don't ignore privacy settings

---

### File: 2030b/app/Http/Controllers/User/DashboardController.php

**Purpose**: Displays user dashboard with personalized statistics, recent activities, course progress, and achievement notifications.

**Dependencies**:
```php
use App\Services\User\UserService;
use App\Services\Course\CourseService;
use App\Services\Analytics\AnalyticsService;
use Illuminate\Http\Request;
use Illuminate\View\View;
use App\DTOs\User\DashboardData;
use Illuminate\Support\Facades\Cache;
use App\Enums\UserLevel;
```

**Methods**:
- index(Request $request): View
- getDashboardData(User $user): DashboardData
- getRecentActivities(User $user): array
- getCourseProgress(User $user): array
- getAchievements(User $user): array
- getRecommendedCourses(User $user): array
- getUpcomingDeadlines(User $user): array
- refreshDashboardCache(User $user): void

**Do**:
- Cache dashboard data for performance
- Personalize content based on user preferences
- Show relevant course recommendations
- Display progress indicators clearly
- Load data efficiently with eager loading
- Implement real-time notifications
- Provide quick action shortcuts

**Don't**:
- Don't load unnecessary data
- Don't expose other users' private information
- Don't ignore user privacy settings
- Don't cache sensitive information
- Don't overwhelm with too much information

---

### File: 2030b/app/Http/Controllers/User/ProgressController.php

**Purpose**: Tracks and manages user learning progress, episode completion, point calculations, and level progression analytics.

**Dependencies**:
```php
use App\Services\User\UserService;
use App\Services\Course\ProgressService;
use App\Http\Requests\User\ProgressUpdateRequest;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use Illuminate\View\View;
use App\DTOs\User\ProgressData;
use App\Events\EpisodeCompleted;
use App\Events\UserLevelChanged;
```

**Methods**:
- index(Request $request): View
- show(int $courseId): View
- update(ProgressUpdateRequest $request): JsonResponse
- completeEpisode(Request $request): JsonResponse
- markEpisodeWatched(int $episodeId): JsonResponse
- calculateProgressPercentage(User $user, int $courseId): float
- awardPoints(User $user, int $points, string $source): void
- checkLevelProgression(User $user): bool
- getProgressStatistics(User $user): array

**Do**:
- Update progress in real-time
- Calculate points accurately
- Fire progression events
- Cache progress data for performance
- Validate progress updates
- Track detailed learning analytics
- Handle level changes properly

**Don't**:
- Don't allow progress manipulation
- Don't skip validation of completion data
- Don't ignore point calculation rules
- Don't cache real-time progress data
- Don't expose progress calculation logic

---

### File: 2030b/app/Http/Controllers/User/NotesController.php

**Purpose**: Manages user course notes, annotations, timestamps, and note sharing functionality with rich text support.

**Dependencies**:
```php
use App\Http\Requests\User\NoteRequest;
use App\Services\User\NoteService;
use App\Models\User\Note;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use Illuminate\View\View;
use App\DTOs\User\NoteData;
use Illuminate\Support\Facades\Gate;
```

**Methods**:
- index(Request $request): View
- store(NoteRequest $request): JsonResponse
- show(Note $note): JsonResponse
- update(NoteRequest $request, Note $note): JsonResponse
- destroy(Note $note): JsonResponse
- search(Request $request): JsonResponse
- export(Request $request): JsonResponse
- shareNote(Note $note, Request $request): JsonResponse

**Do**:
- Authorize note access properly
- Support rich text formatting
- Implement note search functionality
- Allow note export in multiple formats
- Timestamp notes with video positions
- Validate note content and length
- Cache frequently accessed notes

**Don't**:
- Don't allow access to other users' notes
- Don't skip input sanitization
- Don't allow unlimited note storage
- Don't expose sensitive note content
- Don't ignore sharing permissions

---

### File: 2030b/app/Http/Controllers/User/ReactionController.php

**Purpose**: Handles user reactions (likes, dislikes, bookmarks) for courses and episodes with real-time updates and analytics.

**Dependencies**:
```php
use App\Http\Requests\User\ReactionRequest;
use App\Services\User\ReactionService;
use App\Models\User\Reaction;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use App\DTOs\User\ReactionData;
use App\Events\ReactionAdded;
use App\Events\ReactionRemoved;
```

**Methods**:
- store(ReactionRequest $request): JsonResponse
- destroy(Reaction $reaction): JsonResponse
- toggle(ReactionRequest $request): JsonResponse
- getReactions(Request $request): JsonResponse
- getUserReactions(Request $request): JsonResponse
- getReactionStats(int $contentId, string $contentType): array
- bulkUpdateReactions(Request $request): JsonResponse

**Do**:
- Prevent duplicate reactions per user
- Update reaction counts in real-time
- Fire reaction events for notifications
- Cache reaction statistics
- Authorize reaction access
- Track reaction analytics
- Support multiple reaction types

**Don't**:
- Don't allow reaction spamming
- Don't expose user reaction patterns
- Don't skip rate limiting
- Don't ignore content type validation
- Don't cache user-specific reaction data

---

### File: 2030b/app/Http/Controllers/User/PreferencesController.php

**Purpose**: Manages user application preferences, notification settings, theme choices, and personalization options with validation.

**Dependencies**:
```php
use App\Http\Requests\User\PreferencesRequest;
use App\Services\User\PreferencesService;
use Illuminate\Http\Request;
use Illuminate\Http\RedirectResponse;
use Illuminate\View\View;
use App\DTOs\User\PreferencesData;
use App\Enums\Theme;
use App\Enums\Language;
use App\Events\PreferencesUpdated;
```

**Methods**:
- show(): View
- update(PreferencesRequest $request): RedirectResponse
- updateNotificationSettings(Request $request): RedirectResponse
- updateTheme(Request $request): RedirectResponse
- updateLanguage(Request $request): RedirectResponse
- resetToDefaults(Request $request): RedirectResponse
- exportPreferences(Request $request): JsonResponse
- importPreferences(Request $request): RedirectResponse

**Do**:
- Validate preference values against enums
- Cache user preferences for performance
- Fire preference change events
- Provide preference export/import
- Implement theme switching
- Support multiple languages
- Validate notification preferences

**Don't**:
- Don't ignore preference validation
- Don't cache all preference data
- Don't allow invalid enum values
- Don't skip user authorization
- Don't expose system-level preferences

---

### File: 2030b/app/Http/Controllers/Course/DocuCourseController.php

**Purpose**: Core course management including creation, updates, publishing, enrollment handling, and course analytics with authorization.

**Dependencies**:
```php
use App\Http\Requests\Course\CourseRequest;
use App\Services\Course\CourseService;
use App\Models\Course\DocuCourse;
use Illuminate\Http\Request;
use Illuminate\View\View;
use Illuminate\Http\RedirectResponse;
use App\DTOs\Course\CourseData;
use App\Events\CoursePublished;
use App\Events\CourseEnrollment;
use Illuminate\Support\Facades\Gate;
```

**Methods**:
- index(Request $request): View
- show(DocuCourse $course): View
- create(): View
- store(CourseRequest $request): RedirectResponse
- edit(DocuCourse $course): View
- update(CourseRequest $request, DocuCourse $course): RedirectResponse
- destroy(DocuCourse $course): RedirectResponse
- publish(DocuCourse $course): RedirectResponse
- unpublish(DocuCourse $course): RedirectResponse
- clone(DocuCourse $course): RedirectResponse

**Do**:
- Authorize all course operations
- Validate course data thoroughly
- Fire course lifecycle events
- Implement course versioning
- Cache course metadata
- Track course analytics
- Handle course cloning properly
- Support course templates

**Don't**:
- Don't allow unauthorized course access
- Don't skip content validation
- Don't ignore course status changes
- Don't expose draft courses publicly
- Don't allow invalid course structures

---

### File: 2030b/app/Http/Controllers/Course/EpisodeController.php

**Purpose**: Manages course episodes including video uploads, transcripts, progress tracking, and interactive content with media processing.

**Dependencies**:
```php
use App\Http\Requests\Course\EpisodeRequest;
use App\Services\Course\EpisodeService;
use App\Services\Media\VideoService;
use App\Models\Course\Episode;
use Illuminate\Http\Request;
use Illuminate\View\View;
use Illuminate\Http\RedirectResponse;
use App\DTOs\Course\EpisodeData;
use Illuminate\Support\Facades\Gate;
```

**Methods**:
- index(Request $request): View
- show(Episode $episode): View
- create(): View
- store(EpisodeRequest $request): RedirectResponse
- edit(Episode $episode): View
- update(EpisodeRequest $request, Episode $episode): RedirectResponse
- destroy(Episode $episode): RedirectResponse
- uploadVideo(Request $request, Episode $episode): RedirectResponse
- generateTranscript(Episode $episode): RedirectResponse
- reorderEpisodes(Request $request): RedirectResponse

**Do**:
- Process video uploads asynchronously
- Generate automatic transcripts
- Support episode reordering
- Track episode completion
- Validate video formats and sizes
- Implement progressive streaming
- Cache episode metadata
- Handle episode dependencies

**Don't**:
- Don't allow unauthorized episode access
- Don't skip video processing validation
- Don't ignore episode order constraints
- Don't expose processing status details
- Don't allow invalid video formats

---

### File: 2030b/app/Http/Controllers/Course/CategoryController.php

**Purpose**: Manages course categories, hierarchical structures, category assignments, and category-based filtering with SEO optimization.

**Dependencies**:
```php
use App\Http\Requests\Course\CategoryRequest;
use App\Services\Course\CategoryService;
use App\Models\Course\Category;
use Illuminate\Http\Request;
use Illuminate\View\View;
use Illuminate\Http\RedirectResponse;
use App\DTOs\Course\CategoryData;
use Illuminate\Support\Str;
```

**Methods**:
- index(Request $request): View
- show(Category $category): View
- create(): View
- store(CategoryRequest $request): RedirectResponse
- edit(Category $category): View
- update(CategoryRequest $request, Category $category): RedirectResponse
- destroy(Category $category): RedirectResponse
- reorder(Request $request): RedirectResponse
- getCoursesByCategory(Category $category): View

**Do**:
- Support nested category hierarchies
- Generate SEO-friendly slugs
- Validate category relationships
- Implement category reordering
- Cache category trees
- Track category usage statistics
- Support category icons and descriptions

**Don't**:
- Don't create circular category relationships
- Don't allow deletion of categories with courses
- Don't skip slug validation
- Don't ignore parent-child constraints
- Don't expose category management to regular users

---

### File: 2030b/app/Http/Controllers/Course/EnrollmentController.php

**Purpose**: Handles course enrollment processes, payment integration, enrollment status management, and access control validation.

**Dependencies**:
```php
use App\Http\Requests\Course\EnrollmentRequest;
use App\Services\Course\EnrollmentService;
use App\Services\Payment\PaymentService;
use App\Models\Course\Enrollment;
use Illuminate\Http\Request;
use Illuminate\Http\RedirectResponse;
use App\Events\CourseEnrolled;
use App\Events\EnrollmentCompleted;
use Illuminate\Support\Facades\Gate;
```

**Methods**:
- store(EnrollmentRequest $request): RedirectResponse
- show(Enrollment $enrollment): View
- cancel(Enrollment $enrollment): RedirectResponse
- renew(Enrollment $enrollment): RedirectResponse
- transfer(Enrollment $enrollment, Request $request): RedirectResponse
- getEnrollmentStatus(Request $request): JsonResponse
- validateEnrollmentAccess(User $user, DocuCourse $course): bool
- processRefund(Enrollment $enrollment): RedirectResponse

**Do**:
- Validate enrollment eligibility
- Process payments before enrollment
- Fire enrollment events
- Handle enrollment transfers
- Implement refund processing
- Track enrollment analytics
- Support bulk enrollments
- Validate access permissions

**Don't**:
- Don't allow duplicate enrollments
- Don't skip payment validation
- Don't ignore course prerequisites
- Don't expose enrollment details to unauthorized users
- Don't allow enrollments without valid payment

---

### File: 2030b/app/Http/Controllers/Course/CourseUploadController.php

**Purpose**: Manages bulk course content uploads, file processing, validation, and import workflows with progress tracking and error handling.

**Dependencies**:
```php
use App\Http\Requests\Course\CourseUploadRequest;
use App\Services\Course\CourseUploadService;
use App\Services\Media\FileService;
use App\Jobs\ProcessCourseUpload;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use Illuminate\View\View;
use Illuminate\Support\Facades\Storage;
```

**Methods**:
- create(): View
- store(CourseUploadRequest $request): JsonResponse
- getUploadProgress(string $uploadId): JsonResponse
- validateUpload(Request $request): JsonResponse
- processChunkedUpload(Request $request): JsonResponse
- cancelUpload(string $uploadId): JsonResponse
- getUploadHistory(Request $request): View
- retryFailedUpload(string $uploadId): JsonResponse

**Do**:
- Support chunked file uploads
- Validate file types and sizes
- Process uploads asynchronously
- Provide upload progress tracking
- Implement upload resumption
- Validate course structure
- Handle upload errors gracefully
- Support multiple file formats

**Don't**:
- Don't allow unlimited file sizes
- Don't process uploads synchronously
- Don't skip file validation
- Don't ignore upload quotas
- Don't expose temporary file paths

---

### File: 2030b/app/Http/Controllers/Course/SearchController.php

**Purpose**: Provides advanced course search functionality with filters, sorting, faceted search, and search analytics with performance optimization.

**Dependencies**:
```php
use App\Http\Requests\Course\SearchRequest;
use App\Services\Course\SearchService;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use Illuminate\View\View;
use App\DTOs\Course\SearchFilters;
use Laravel\Scout\Searchable;
use Illuminate\Support\Facades\Cache;
```

**Methods**:
- index(SearchRequest $request): View
- search(SearchRequest $request): JsonResponse
- getSearchSuggestions(Request $request): JsonResponse
- getPopularSearches(): JsonResponse
- saveSearch(Request $request): JsonResponse
- getSearchHistory(Request $request): JsonResponse
- exportSearchResults(SearchRequest $request): JsonResponse
- getSearchAnalytics(): JsonResponse

**Do**:
- Implement full-text search
- Support advanced filtering
- Cache popular search results
- Track search analytics
- Provide search suggestions
- Support faceted search
- Implement search result ranking
- Handle typo tolerance

**Don't**:
- Don't expose search algorithms
- Don't ignore search performance
- Don't cache user-specific searches
- Don't allow unrestricted search queries
- Don't skip search result validation

---

### File: 2030b/app/Http/Controllers/Payment/PaymentController.php

**Purpose**: Central payment processing hub managing multiple payment methods, transaction handling, invoice generation, and payment status tracking.

**Dependencies**:
```php
use App\Http\Requests\Payment\PaymentRequest;
use App\Services\Payment\PaymentService;
use App\Services\Payment\StripeService;
use App\Services\Payment\BinanceService;
use App\Models\Payment\Transaction;
use Illuminate\Http\Request;
use Illuminate\Http\RedirectResponse;
use Illuminate\View\View;
use App\DTOs\Payment\PaymentData;
use App\Events\PaymentProcessed;
use App\Events\PaymentFailed;
```

**Methods**:
- index(Request $request): View
- create(): View
- store(PaymentRequest $request): RedirectResponse
- show(Transaction $transaction): View
- processPayment(PaymentData $paymentData): array
- handlePaymentCallback(Request $request): RedirectResponse
- refundPayment(Transaction $transaction): RedirectResponse
- getPaymentMethods(Request $request): JsonResponse
- validatePaymentData(PaymentData $data): bool

**Do**:
- Support multiple payment gateways
- Validate payment data thoroughly
- Fire payment events
- Handle payment failures gracefully
- Implement payment retry logic
- Log all payment attempts
- Support partial refunds
- Validate payment amounts

**Don't**:
- Don't store sensitive payment data
- Don't skip payment validation
- Don't ignore PCI compliance
- Don't expose payment processing details
- Don't allow payment manipulation

---

### File: 2030b/app/Http/Controllers/Payment/StripeController.php

**Purpose**: Handles Stripe-specific payment processing, webhook management, subscription billing, and Stripe Connect integration with security validation.

**Dependencies**:
```php
use Stripe\Stripe;
use Stripe\PaymentIntent;
use Stripe\Webhook;
use App\Services\Payment\StripeService;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use App\DTOs\Payment\StripePaymentData;
use App\Events\StripePaymentCompleted;
use Illuminate\Support\Facades\Log;
```

**Methods**:
- createPaymentIntent(Request $request): JsonResponse
- confirmPayment(Request $request): JsonResponse
- handleWebhook(Request $request): JsonResponse
- createCustomer(Request $request): JsonResponse
- createSubscription(Request $request): JsonResponse
- cancelSubscription(Request $request): JsonResponse
- processRefund(Request $request): JsonResponse
- validateWebhookSignature(Request $request): bool

**Do**:
- Validate webhook signatures
- Handle idempotent operations
- Implement proper error handling
- Use Stripe's latest API version
- Store Stripe customer IDs securely
- Handle subscription lifecycle events
- Implement Connect account management
- Log Stripe interactions

**Don't**:
- Don't skip webhook validation
- Don't store Stripe secrets in code
- Don't ignore API version compatibility
- Don't expose Stripe internal errors
- Don't process duplicate webhooks

---

### File: 2030b/app/Http/Controllers/Payment/BinanceController.php

**Purpose**: Manages cryptocurrency payments through Binance Pay, handles crypto transaction validation, and processes blockchain confirmations with security measures.

**Dependencies**:
```php
use Binance\Connector\Spot;
use App\Services\Payment\BinanceService;
use App\Services\Payment\CryptoService;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use App\DTOs\Payment\CryptoPaymentData;
use App\Events\CryptoPaymentReceived;
use Illuminate\Support\Facades\Log;
```

**Methods**:
- createPayment(Request $request): JsonResponse
- checkPaymentStatus(Request $request): JsonResponse
- handleWebhook(Request $request): JsonResponse
- validateCryptoAddress(string $address, string $currency): bool
- getExchangeRates(Request $request): JsonResponse
- processCryptoRefund(Request $request): JsonResponse
- getCryptoTransactionFee(Request $request): JsonResponse
- validateWebhookSignature(Request $request): bool

**Do**:
- Validate cryptocurrency addresses
- Monitor blockchain confirmations
- Handle exchange rate fluctuations
- Implement proper crypto validation
- Log crypto transactions securely
- Handle network confirmation delays
- Validate webhook authenticity
- Support multiple cryptocurrencies

**Don't**:
- Don't store private keys
- Don't ignore blockchain confirmations
- Don't skip address validation
- Don't expose wallet information
- Don't process unconfirmed transactions

---

### File: 2030b/app/Http/Controllers/Payment/TransactionController.php

**Purpose**: Comprehensive transaction management including history tracking, status updates, transaction analytics, and audit trail maintenance.

**Dependencies**:
```php
use App\Services\Payment\TransactionService;
use App\Models\Payment\Transaction;
use Illuminate\Http\Request;
use Illuminate\View\View;
use Illuminate\Http\JsonResponse;
use App\DTOs\Payment\TransactionFilters;
use Illuminate\Support\Facades\Gate;
use App\Exports\TransactionExport;
```

**Methods**:
- index(Request $request): View
- show(Transaction $transaction): View
- updateStatus(Transaction $transaction, Request $request): JsonResponse
- getTransactionHistory(Request $request): JsonResponse
- exportTransactions(Request $request): JsonResponse
- getTransactionAnalytics(Request $request): JsonResponse
- reconcileTransactions(Request $request): JsonResponse
- flagTransaction(Transaction $transaction, Request $request): JsonResponse

**Do**:
- Authorize transaction access
- Implement comprehensive filtering
- Track transaction state changes
- Provide detailed analytics
- Support transaction export
- Implement transaction reconciliation
- Log all transaction updates
- Validate transaction modifications

**Don't**:
- Don't allow unauthorized transaction access
- Don't expose sensitive transaction data
- Don't skip audit trail logging
- Don't allow direct transaction manipulation
- Don't ignore transaction integrity checks

---

### File: 2030b/app/Http/Controllers/Payment/InvoiceController.php

**Purpose**: Invoice generation, PDF creation, payment tracking, invoice templates, and automated billing workflows with tax calculations.

**Dependencies**:
```php
use App\Services\Payment\InvoiceService;
use App\Services\Payment\TaxService;
use App\Models\Payment\Invoice;
use Barryvdh\DomPDF\Facade\Pdf;
use Illuminate\Http\Request;
use Illuminate\View\View;
use Illuminate\Http\Response;
use App\DTOs\Payment\InvoiceData;
use Illuminate\Support\Facades\Gate;
```

**Methods**:
- index(Request $request): View
- show(Invoice $invoice): View
- create(): View
- store(Request $request): RedirectResponse
- generatePdf(Invoice $invoice): Response
- sendInvoice(Invoice $invoice): JsonResponse
- markAsPaid(Invoice $invoice): JsonResponse
- voidInvoice(Invoice $invoice): JsonResponse
- duplicateInvoice(Invoice $invoice): RedirectResponse
- calculateTaxes(InvoiceData $data): array

**Do**:
- Generate professional PDF invoices
- Calculate taxes accurately
- Track invoice payment status
- Send invoices via email
- Support invoice templates
- Implement invoice numbering
- Handle currency conversions
- Validate invoice data

**Don't**:
- Don't allow unauthorized invoice access
- Don't skip tax calculations
- Don't ignore invoice sequencing
- Don't expose invoice generation logic
- Don't allow invoice tampering

---

### File: 2030b/app/Http/Controllers/Payment/WebhookController.php

**Purpose**: Centralized webhook handler for all payment providers, signature validation, event processing, and webhook security management.

**Dependencies**:
```php
use App\Services\Payment\WebhookService;
use App\Services\Payment\StripeService;
use App\Services\Payment\BinanceService;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use App\Events\WebhookReceived;
use Illuminate\Support\Facades\Log;
use Exception;
```

**Methods**:
- handle(Request $request, string $provider): JsonResponse
- validateSignature(Request $request, string $provider): bool
- processWebhookEvent(array $event, string $provider): void
- handleStripeWebhook(array $event): void
- handleBinanceWebhook(array $event): void
- logWebhookEvent(array $event, string $provider): void
- retryFailedWebhook(Request $request): JsonResponse
- getWebhookHistory(Request $request): JsonResponse

**Do**:
- Validate all webhook signatures
- Process webhooks idempotently
- Log webhook events for debugging
- Handle webhook failures gracefully
- Implement webhook replay functionality
- Support multiple webhook providers
- Rate limit webhook endpoints
- Fire webhook received events

**Don't**:
- Don't skip signature validation
- Don't process duplicate webhooks
- Don't expose webhook processing details
- Don't ignore webhook security
- Don't allow unauthorized webhook calls

---

### File: 2030b/app/Http/Controllers/Affiliate/AffiliateController.php

**Purpose**: Core affiliate program management including registration, dashboard analytics, commission tracking, and affiliate performance monitoring.

**Dependencies**:
```php
use App\Http\Requests\Affiliate\AffiliateRequest;
use App\Services\Affiliate\AffiliateService;
use App\Models\Affiliate\Affiliate;
use Illuminate\Http\Request;
use Illuminate\View\View;
use Illuminate\Http\RedirectResponse;
use App\DTOs\Affiliate\AffiliateData;
use App\Events\AffiliateRegistered;
use Illuminate\Support\Facades\Gate;
```

**Methods**:
- index(Request $request): View
- dashboard(): View
- store(AffiliateRequest $request): RedirectResponse
- show(Affiliate $affiliate): View
- update(AffiliateRequest $request, Affiliate $affiliate): RedirectResponse
- getAffiliateStats(Affiliate $affiliate): array
- generateAffiliateLink(Request $request): JsonResponse
- getCommissionHistory(Request $request): JsonResponse
- requestPayout(Request $request): RedirectResponse

**Do**:
- Validate affiliate eligibility
- Generate unique affiliate links
- Track affiliate performance
- Calculate commissions accurately
- Provide detailed analytics
- Handle payout requests
- Fire affiliate events
- Implement affiliate tiers

**Don't**:
- Don't allow self-referrals
- Don't skip commission validation
- Don't expose other affiliates' data
- Don't ignore payout thresholds
- Don't allow commission manipulation

---

### File: 2030b/app/Http/Controllers/Affiliate/ReferralController.php

**Purpose**: Manages referral tracking, conversion attribution, referral code generation, and referral reward distribution with fraud prevention.

**Dependencies**:
```php
use App\Services\Affiliate\ReferralService;
use App\Models\Affiliate\Referral;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use App\DTOs\Affiliate\ReferralData;
use App\Events\ReferralCompleted;
use Illuminate\Support\Str;
```

**Methods**:
- track(Request $request): JsonResponse
- convertReferral(Request $request): JsonResponse
- getReferralStats(Request $request): JsonResponse
- generateReferralCode(Request $request): JsonResponse
- validateReferralCode(string $code): bool
- processReferralReward(Referral $referral): void
- detectFraudulentReferrals(Request $request): bool
- getReferralHistory(Request $request): JsonResponse

**Do**:
- Track referral sources accurately
- Detect and prevent referral fraud
- Generate unique referral codes
- Process rewards automatically
- Validate referral conversions
- Track referral attribution
- Implement cooling-off periods
- Monitor referral patterns

**Don't**:
- Don't allow referral manipulation
- Don't skip fraud detection
- Don't ignore referral attribution
- Don't process duplicate referrals
- Don't expose referral algorithms

---

### File: 2030b/app/Http/Controllers/Affiliate/CommissionController.php

**Purpose**: Commission calculation, tier management, payment processing, commission adjustments, and affiliate compensation analytics with reporting.

**Dependencies**:
```php
use App\Services\Affiliate\CommissionService;
use App\Models\Affiliate\Commission;
use Illuminate\Http\Request;
use Illuminate\View\View;
use Illuminate\Http\JsonResponse;
use App\DTOs\Affiliate\CommissionData;
use App\Events\CommissionEarned;
use Illuminate\Support\Facades\Gate;
```

**Methods**:
- index(Request $request): View
- show(Commission $commission): View
- calculate(Request $request): JsonResponse
- adjustCommission(Commission $commission, Request $request): JsonResponse
- processPayouts(Request $request): JsonResponse
- getCommissionReport(Request $request): JsonResponse
- reverseCommission(Commission $commission): JsonResponse
- updateCommissionTiers(Request $request): JsonResponse

**Do**:
- Calculate commissions accurately
- Support tiered commission structures
- Process commission payments
- Handle commission adjustments
- Generate commission reports
- Track commission analytics
- Validate commission rules
- Fire commission events

**Don't**:
- Don't allow commission tampering
- Don't skip commission validation
- Don't ignore payout thresholds
- Don't process duplicate commissions
- Don't expose commission calculation logic

---

### File: 2030b/app/Http/Controllers/Affiliate/TrackingController.php

**Purpose**: Advanced affiliate tracking with click attribution, conversion funnel analysis, traffic source analytics, and performance optimization insights.

**Dependencies**:
```php
use App\Services\Affiliate\TrackingService;
use App\Services\Analytics\AnalyticsService;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use App\DTOs\Analytics\TrackingData;
use Illuminate\Support\Facades\Cache;
use Jenssegers\Agent\Agent;
```

**Methods**:
- trackClick(Request $request): JsonResponse
- trackConversion(Request $request): JsonResponse
- getTrackingPixel(Request $request): Response
- getClickAnalytics(Request $request): JsonResponse
- getConversionFunnel(Request $request): JsonResponse
- getTrafficSources(Request $request): JsonResponse
- getDeviceAnalytics(Request $request): JsonResponse
- exportTrackingData(Request $request): JsonResponse

**Do**:
- Track clicks and conversions accurately
- Implement proper attribution models
- Cache tracking analytics
- Support cross-device tracking
- Provide detailed funnel analysis
- Track traffic sources
- Implement pixel tracking
- Generate tracking reports

**Don't**:
- Don't violate user privacy
- Don't skip consent validation
- Don't ignore GDPR compliance
- Don't cache sensitive tracking data
- Don't expose tracking mechanisms

---

### File: 2030b/app/Http/Controllers/Affiliate/SocialShareController.php

**Purpose**: Social media sharing functionality, viral tracking, social platform integration, and social sharing analytics with engagement monitoring.

**Dependencies**:
```php
use App\Services\Affiliate\SocialShareService;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use App\DTOs\Social\ShareData;
use Illuminate\Support\Facades\Cache;
use App\Events\ContentShared;
```

**Methods**:
- generateShareUrl(Request $request): JsonResponse
- trackShare(Request $request): JsonResponse
- getSocialShareStats(Request $request): JsonResponse
- getViralCoefficient(Request $request): JsonResponse
- getPopularShares(Request $request): JsonResponse
- generateSocialCards(Request $request): JsonResponse
- trackSocialEngagement(Request $request): JsonResponse
- getSocialPlatformPerformance(Request $request): JsonResponse

**Do**:
- Generate trackable share URLs
- Support multiple social platforms
- Track viral spreading
- Generate social media cards
- Monitor engagement metrics
- Cache social statistics
- Fire sharing events
- Optimize for social SEO

**Don't**:
- Don't ignore platform-specific requirements
- Don't skip share attribution
- Don't expose tracking parameters
- Don't violate platform terms of service
- Don't cache real-time engagement data

---

### File: 2030b/app/Http/Controllers/Media/MediaController.php

**Purpose**: Comprehensive media management including uploads, organization, metadata extraction, media processing, and digital asset management with CDN integration.

**Dependencies**:
```php
use App\Services\Media\MediaService;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Illuminate\Http\Request;
use Illuminate\View\View;
use Illuminate\Http\JsonResponse;
use App\DTOs\Media\MediaData;
use Illuminate\Support\Facades\Gate;
use Illuminate\Support\Facades\Storage;
```

**Methods**:
- index(Request $request): View
- show(Media $media): View
- store(Request $request): JsonResponse
- update(Media $media, Request $request): JsonResponse
- destroy(Media $media): JsonResponse
- download(Media $media): Response
- getMediaMetadata(Media $media): JsonResponse
- organizeMedia(Request $request): JsonResponse
- bulkDelete(Request $request): JsonResponse

**Do**:
- Validate file types and sizes
- Extract media metadata automatically
- Implement media organization
- Support bulk operations
- Generate thumbnails and previews
- Integrate with CDN services
- Track media usage
- Authorize media access

**Don't**:
- Don't allow unauthorized media access
- Don't skip file validation
- Don't ignore storage quotas
- Don't expose media processing details
- Don't allow unlimited uploads

---

### File: 2030b/app/Http/Controllers/Media/FileUploadController.php

**Purpose**: Advanced file upload handling with chunked uploads, progress tracking, file validation, virus scanning, and upload optimization.

**Dependencies**:
```php
use App\Http\Requests\Media\FileUploadRequest;
use App\Services\Media\FileUploadService;
use App\Jobs\ProcessFileUpload;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use Illuminate\Support\Facades\Storage;
use App\Rules\ValidFileType;
```

**Methods**:
- upload(FileUploadRequest $request): JsonResponse
- uploadChunk(Request $request): JsonResponse
- completeChunkedUpload(Request $request): JsonResponse
- getUploadProgress(string $uploadId): JsonResponse
- cancelUpload(string $uploadId): JsonResponse
- resumeUpload(Request $request): JsonResponse
- validateFile(Request $request): JsonResponse
- getUploadUrl(Request $request): JsonResponse

**Do**:
- Support chunked file uploads
- Validate files before processing
- Scan uploads for viruses
- Track upload progress
- Implement upload resumption
- Handle upload failures gracefully
- Generate secure upload URLs
- Process uploads asynchronously

**Don't**:
- Don't allow unlimited file sizes
- Don't skip virus scanning
- Don't process uploads synchronously
- Don't expose temporary file paths
- Don't ignore upload quotas

---

### File: 2030b/app/Http/Controllers/Media/ImageOptimizationController.php

**Purpose**: Image processing and optimization including resizing, compression, format conversion, thumbnail generation, and responsive image creation.

**Dependencies**:
```php
use App\Services\Media\ImageOptimizationService;
use Intervention\Image\ImageManagerStatic as Image;
use Spatie\ImageOptimizer\OptimizerChainFactory;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use App\Jobs\OptimizeImage;
```

**Methods**:
- optimize(Request $request): JsonResponse
- resize(Request $request): JsonResponse
- compress(Request $request): JsonResponse
- convertFormat(Request $request): JsonResponse
- generateThumbnails(Request $request): JsonResponse
- createResponsiveImages(Request $request): JsonResponse
- watermarkImage(Request $request): JsonResponse
- getBulkOptimizationStatus(Request $request): JsonResponse

**Do**:
- Optimize images for web delivery
- Generate multiple image sizes
- Support various image formats
- Implement progressive JPEG
- Add watermarks when needed
- Process images asynchronously
- Maintain aspect ratios
- Cache optimized images

**Don't**:
- Don't process images synchronously
- Don't ignore image quality settings
- Don't skip format validation
- Don't allow unlimited processing
- Don't expose processing algorithms

---

### File: 2030b/app/Http/Controllers/Admin/DashboardController.php

**Purpose**: Administrative dashboard providing system overview, key metrics, user analytics, financial summaries, and operational insights with data visualization.

**Dependencies**:
```php
use App\Services\Admin\DashboardService;
use App\Services\Analytics\AnalyticsService;
use App\Services\System\SystemService;
use Illuminate\Http\Request;
use Illuminate\View\View;
use App\DTOs\Admin\DashboardData;
use Illuminate\Support\Facades\Cache;
```

**Methods**:
- index(): View
- getSystemMetrics(): array
- getUserAnalytics(): array
- getFinancialSummary(): array
- getCourseStatistics(): array
- getRevenueChart(): JsonResponse
- getPerformanceMetrics(): JsonResponse
- getSystemHealth(): JsonResponse
- refreshDashboardData(): JsonResponse

**Do**:
- Cache dashboard data for performance
- Provide real-time system metrics
- Display key performance indicators
- Show financial summaries
- Implement data visualization
- Monitor system health
- Track user engagement
- Provide actionable insights

**Don't**:
- Don't expose sensitive system data
- Don't ignore performance impact
- Don't cache real-time metrics
- Don't allow unauthorized admin access
- Don't overwhelm with too much data

---

### File: 2030b/app/Http/Controllers/Admin/UserManagementController.php

**Purpose**: Comprehensive user administration including user CRUD operations, role management, account suspension, user analytics, and bulk user operations.

**Dependencies**:
```php
use App\Http\Requests\Admin\UserManagementRequest;
use App\Services\Admin\UserManagementService;
use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\View\View;
use Illuminate\Http\RedirectResponse;
use Spatie\Permission\Models\Role;
use App\Events\UserSuspended;
```

**Methods**:
- index(Request $request): View
- show(User $user): View
- edit(User $user): View
- update(UserManagementRequest $request, User $user): RedirectResponse
- suspend(User $user, Request $request): RedirectResponse
- activate(User $user): RedirectResponse
- assignRole(User $user, Request $request): RedirectResponse
- bulkUpdate(Request $request): RedirectResponse
- exportUsers(Request $request): Response

**Do**:
- Authorize all admin operations
- Implement comprehensive user search
- Support bulk user operations
- Track user management activities
- Validate role assignments
- Fire user management events
- Provide detailed user profiles
- Export user data securely

**Don't**:
- Don't allow unauthorized user access
- Don't skip audit trail logging
- Don't expose sensitive user data
- Don't ignore role hierarchy
- Don't allow self-suspension

---

### File: 2030b/app/Http/Controllers/Admin/SystemConfigController.php

**Purpose**: System configuration management including application settings, feature toggles, environment variables, and system maintenance operations.

**Dependencies**:
```php
use App\Http\Requests\Admin\SystemConfigRequest;
use App\Services\System\ConfigService;
use App\Models\System\Configuration;
use Illuminate\Http\Request;
use Illuminate\View\View;
use Illuminate\Http\RedirectResponse;
use Illuminate\Support\Facades\Artisan;
use Illuminate\Support\Facades\Cache;
```

**Methods**:
- index(): View
- show(Configuration $config): View
- update(SystemConfigRequest $request): RedirectResponse
- clearCache(): RedirectResponse
- runMaintenance(): RedirectResponse
- updateEnvironment(Request $request): RedirectResponse
- getConfigValue(string $key): JsonResponse
- backupConfiguration(): RedirectResponse
- restoreConfiguration(Request $request): RedirectResponse

**Do**:
- Validate configuration changes
- Clear caches after updates
- Backup configurations before changes
- Log configuration modifications
- Implement configuration versioning
- Validate environment settings
- Support configuration export/import
- Authorize configuration access

**Don't**:
- Don't expose sensitive configuration
- Don't skip validation of config values
- Don't ignore cache clearing
- Don't allow unauthorized config access
- Don't modify production config carelessly

---

### File: 2030b/app/Http/Controllers/Admin/AnalyticsController.php

**Purpose**: Advanced analytics and reporting including user behavior analysis, performance metrics, business intelligence, and custom report generation.

**Dependencies**:
```php
use App\Services\Analytics\AnalyticsService;
use App\Services\Analytics\ReportingService;
use Illuminate\Http\Request;
use Illuminate\View\View;
use Illuminate\Http\JsonResponse;
use App\DTOs\Analytics\ReportFilters;
use App\Exports\AnalyticsExport;
```

**Methods**:
- index(): View
- getUserAnalytics(Request $request): JsonResponse
- getCourseAnalytics(Request $request): JsonResponse
- getRevenueAnalytics(Request $request): JsonResponse
- getTrafficAnalytics(Request $request): JsonResponse
- generateCustomReport(Request $request): JsonResponse
- exportReport(Request $request): Response
- getAnalyticsDashboard(): View
- scheduleReport(Request $request): RedirectResponse

**Do**:
- Implement comprehensive analytics
- Support custom date ranges
- Provide data visualization
- Cache analytics results
- Generate exportable reports
- Support scheduled reports
- Track key performance indicators
- Implement funnel analysis

**Don't**:
- Don't expose raw analytics data
- Don't ignore data privacy
- Don't cache real-time analytics
- Don't allow unauthorized analytics access
- Don't overwhelm with excessive metrics

---

### File: 2030b/app/Http/Controllers/Admin/BackupController.php

**Purpose**: System backup management including database backups, file backups, backup scheduling, restoration, and backup integrity verification.

**Dependencies**:
```php
use App\Services\System\BackupService;
use Spatie\Backup\BackupDestination\BackupDestination;
use Illuminate\Http\Request;
use Illuminate\View\View;
use Illuminate\Http\RedirectResponse;
use App\Jobs\CreateBackup;
use App\Jobs\RestoreBackup;
```

**Methods**:
- index(): View
- create(): RedirectResponse
- restore(Request $request): RedirectResponse
- download(Request $request): Response
- delete(Request $request): RedirectResponse
- schedule(Request $request): RedirectResponse
- getBackupStatus(): JsonResponse
- verifyBackupIntegrity(Request $request): JsonResponse
- cleanupOldBackups(): RedirectResponse

**Do**:
- Schedule regular backups
- Verify backup integrity
- Support multiple backup destinations
- Encrypt sensitive backup data
- Monitor backup success/failure
- Implement backup rotation
- Test backup restoration
- Log backup activities

**Don't**:
- Don't store backups insecurely
- Don't skip backup verification
- Don't ignore backup failures
- Don't allow unauthorized backup access
- Don't backup to single location only

---

### File: 2030b/app/Http/Controllers/Api/V1/AuthController.php

**Purpose**: API authentication endpoints including token-based authentication, API key management, refresh tokens, and API access control with rate limiting.

**Dependencies**:
```php
use App\Http\Requests\Api\AuthRequest;
use App\Services\Api\AuthService;
use Laravel\Sanctum\HasApiTokens;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use App\DTOs\Api\AuthData;
use Illuminate\Support\Facades\RateLimiter;
```

**Methods**:
- login(AuthRequest $request): JsonResponse
- register(AuthRequest $request): JsonResponse
- logout(Request $request): JsonResponse
- refresh(Request $request): JsonResponse
- me(Request $request): JsonResponse
- generateApiKey(Request $request): JsonResponse
- revokeApiKey(Request $request): JsonResponse
- validateToken(Request $request): JsonResponse

**Do**:
- Implement token-based authentication
- Rate limit API authentication
- Support token refresh
- Validate API requests
- Log API authentication attempts
- Generate secure API keys
- Implement token expiration
- Support token revocation

**Don't**:
- Don't expose sensitive token data
- Don't ignore rate limiting
- Don't allow unlimited API access
- Don't skip token validation
- Don't store tokens insecurely

---

### File: 2030b/app/Http/Controllers/Api/V1/UserApiController.php

**Purpose**: RESTful API endpoints for user management including profile operations, user data retrieval, preferences, and user-related API functionality.

**Dependencies**:
```php
use App\Http\Resources\User\UserResource;
use App\Http\Resources\User\UserCollection;
use App\Services\User\UserService;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use App\DTOs\User\UserData;
use Illuminate\Support\Facades\Gate;
```

**Methods**:
- index(Request $request): JsonResponse
- show(User $user): JsonResponse
- store(Request $request): JsonResponse
- update(Request $request, User $user): JsonResponse
- destroy(User $user): JsonResponse
- profile(Request $request): JsonResponse
- updateProfile(Request $request): JsonResponse
- getPreferences(Request $request): JsonResponse

**Do**:
- Use API resources for consistent output
- Implement proper API pagination
- Validate API request data
- Authorize API operations
- Return appropriate HTTP status codes
- Support API versioning
- Implement API documentation
- Handle API errors gracefully

**Don't**:
- Don't expose sensitive user data
- Don't skip API authorization
- Don't ignore API rate limits
- Don't return inconsistent responses
- Don't allow unauthorized API access

---

### File: 2030b/app/Http/Controllers/Api/V1/CourseApiController.php

**Purpose**: Course-related API endpoints including course CRUD operations, enrollment management, progress tracking, and course data API with filtering and search.

**Dependencies**:
```php
use App\Http\Resources\Course\DocuCourseResource;
use App\Http\Resources\Course\CourseCollection;
use App\Services\Course\CourseService;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use App\DTOs\Course\CourseFilters;
use Illuminate\Support\Facades\Gate;
```

**Methods**:
- index(Request $request): JsonResponse
- show(DocuCourse $course): JsonResponse
- store(Request $request): JsonResponse
- update(Request $request, DocuCourse $course): JsonResponse
- destroy(DocuCourse $course): JsonResponse
- enroll(Request $request): JsonResponse
- getProgress(Request $request): JsonResponse
- search(Request $request): JsonResponse

**Do**:
- Implement API resource transformations
- Support advanced filtering
- Provide course search functionality
- Handle enrollment via API
- Track API usage analytics
- Implement course recommendations
- Support bulk operations
- Validate course data

**Don't**:
- Don't expose draft courses via API
- Don't skip course authorization
- Don't ignore API versioning
- Don't return excessive course data
- Don't allow unauthorized course modifications

---

### File: 2030b/app/Http/Controllers/Api/V1/PaymentApiController.php

**Purpose**: Payment processing API including payment initiation, transaction status, webhook handling, and payment-related API operations with security validation.

**Dependencies**:
```php
use App\Http\Resources\Payment\TransactionResource;
use App\Services\Payment\PaymentService;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use App\DTOs\Payment\ApiPaymentData;
use Illuminate\Support\Facades\Gate;
```

**Methods**:
- createPayment(Request $request): JsonResponse
- getPaymentStatus(Request $request): JsonResponse
- getTransactions(Request $request): JsonResponse
- refundPayment(Request $request): JsonResponse
- getPaymentMethods(Request $request): JsonResponse
- validatePayment(Request $request): JsonResponse
- getInvoice(Request $request): JsonResponse
- processWebhook(Request $request): JsonResponse

**Do**:
- Validate all payment API requests
- Implement secure payment processing
- Handle payment webhooks properly
- Return appropriate payment statuses
- Log payment API interactions
- Support multiple payment methods
- Implement payment retry logic
- Authorize payment operations

**Don't**:
- Don't expose payment processing details
- Don't skip payment validation
- Don't ignore PCI compliance
- Don't allow unauthorized payments
- Don't return sensitive payment data

---

### File: 2030b/app/Http/Controllers/Api/V1/AffiliateApiController.php

**Purpose**: Affiliate program API including affiliate registration, commission tracking, referral management, and affiliate analytics via API endpoints.

**Dependencies**:
```php
use App\Http\Resources\Affiliate\AffiliateResource;
use App\Services\Affiliate\AffiliateService;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use App\DTOs\Affiliate\AffiliateApiData;
use Illuminate\Support\Facades\Gate;
```

**Methods**:
- register(Request $request): JsonResponse
- getAffiliateData(Request $request): JsonResponse
- getCommissions(Request $request): JsonResponse
- getReferrals(Request $request): JsonResponse
- generateReferralCode(Request $request): JsonResponse
- getStatistics(Request $request): JsonResponse
- requestPayout(Request $request): JsonResponse
- trackReferral(Request $request): JsonResponse

**Do**:
- Validate affiliate API requests
- Implement affiliate authorization
- Track API-based referrals
- Provide affiliate analytics
- Support affiliate registration
- Generate referral codes via API
- Handle payout requests
- Log affiliate API activities

**Don't**:
- Don't expose other affiliates' data
- Don't skip affiliate validation
- Don't allow referral manipulation
- Don't ignore commission rules
- Don't bypass fraud detection

---

### File: 2030b/app/Http/Controllers/System/LanguageController.php

**Purpose**: Multi-language support including language switching, translation management, locale settings, and internationalization features with RTL support.

**Dependencies**:
```php
use App\Services\System\LanguageService;
use App\Models\System\Language;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
use Illuminate\Http\Request;
use Illuminate\Http\RedirectResponse;
use Illuminate\View\View;
use App\DTOs\System\LanguageData;
```

**Methods**:
- index(): View
- switch(Request $request): RedirectResponse
- getAvailableLanguages(): JsonResponse
- getCurrentLanguage(): JsonResponse
- getTranslations(Request $request): JsonResponse
- updateTranslation(Request $request): JsonResponse
- exportTranslations(Request $request): Response
- importTranslations(Request $request): RedirectResponse

**Do**:
- Support multiple languages
- Handle RTL language layouts
- Cache translation data
- Validate language codes
- Support translation export/import
- Implement fallback languages
- Track language usage
- Handle missing translations gracefully

**Don't**:
- Don't ignore RTL requirements
- Don't cache user-specific languages
- Don't expose translation keys
- Don't allow invalid language codes
- Don't skip translation validation

---

### File: 2030b/app/Http/Controllers/System/ConfigurationController.php

**Purpose**: System configuration API including runtime settings, feature flags, application parameters, and dynamic configuration management.

**Dependencies**:
```php
use App\Services\System\ConfigurationService;
use App\Models\System\Configuration;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use App\DTOs\System\ConfigData;
use Illuminate\Support\Facades\Cache;
```

**Methods**:
- getConfig(Request $request): JsonResponse
- updateConfig(Request $request): JsonResponse
- getFeatureFlags(): JsonResponse
- toggleFeature(Request $request): JsonResponse
- getSystemSettings(): JsonResponse
- validateConfiguration(Request $request): JsonResponse
- resetToDefaults(Request $request): JsonResponse
- getConfigHistory(Request $request): JsonResponse

**Do**:
- Cache configuration data
- Validate configuration updates
- Support feature toggles
- Track configuration changes
- Implement configuration versioning
- Provide configuration validation
- Support configuration rollback
- Authorize configuration access

**Don't**:
- Don't expose sensitive configuration
- Don't skip configuration validation
- Don't ignore cache invalidation
- Don't allow unauthorized config changes
- Don't modify critical settings carelessly

---

### File: 2030b/app/Http/Controllers/System/HealthCheckController.php

**Purpose**: System health monitoring including database connectivity, external service status, performance metrics, and system diagnostics with alerting.

**Dependencies**:
```php
use App\Services\System\HealthService;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use App\DTOs\System\HealthStatus;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Cache;
```

**Methods**:
- check(): JsonResponse
- detailed(): JsonResponse
- database(): JsonResponse
- cache(): JsonResponse
- storage(): JsonResponse
- services(): JsonResponse
- performance(): JsonResponse
- getSystemInfo(): JsonResponse

**Do**:
- Check database connectivity
- Monitor external services
- Track performance metrics
- Provide detailed health status
- Implement health alerts
- Cache health check results briefly
- Support multiple health endpoints
- Log health check failures

**Don't**:
- Don't expose sensitive system info
- Don't ignore health check failures
- Don't cache health results too long
- Don't allow unauthorized health access
- Don't perform expensive health checks

---

### File: 2030b/app/Http/Controllers/Controller.php

**Purpose**: Base controller class providing common functionality, middleware registration, response helpers, and shared controller logic for inheritance.

**Dependencies**:
```php
use Illuminate\Foundation\Auth\Access\AuthorizesRequests;
use Illuminate\Foundation\Validation\ValidatesRequests;
use Illuminate\Routing\Controller as BaseController;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\RedirectResponse;
use Illuminate\Support\Facades\Response;
```

**Methods**:
- successResponse(mixed $data, string $message, int $status): JsonResponse
- errorResponse(string $message, int $status, array $errors): JsonResponse
- redirectWithSuccess(string $route, string $message): RedirectResponse
- redirectWithError(string $route, string $message): RedirectResponse
- validateRequest(array $rules, array $data): array
- authorizeAction(string $ability, mixed $resource): bool

**Do**:
- Provide consistent response formats
- Implement common validation logic
- Handle authorization consistently
- Use standard HTTP status codes
- Implement common middleware
- Provide response helpers
- Handle common exceptions
- Support API and web responses

**Don't**:
- Don't implement business logic
- Don't skip authorization checks
- Don't ignore response standards
- Don't handle specific domain logic
- Don't bypass common validations

---

### File: 2030b/app/Http/Controllers/BaseApiController.php

**Purpose**: Base API controller extending common controller functionality with API-specific features, response formatting, and API middleware management.

**Dependencies**:
```php
use App\Http\Controllers\Controller;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Request;
use App\Traits\ApiResponses;
use Illuminate\Support\Facades\Validator;
use Illuminate\Validation\ValidationException;
```

**Methods**:
- respondWithSuccess(mixed $data, string $message, int $status): JsonResponse
- respondWithError(string $message, int $status, array $errors): JsonResponse
- respondWithValidationError(ValidationException $e): JsonResponse
- validateApiRequest(Request $request, array $rules): array
- paginateApiResponse(mixed $data): JsonResponse
- handleApiException(\Exception $e): JsonResponse

**Do**:
- Standardize API response formats
- Handle API-specific validation
- Implement consistent error responses
- Support API pagination
- Handle API exceptions gracefully
- Use appropriate HTTP status codes
- Implement API rate limiting
- Support API versioning

**Don't**:
- Don't return inconsistent API responses
- Don't ignore API standards
- Don't skip API validation
- Don't expose internal errors
- Don't bypass API middleware

---

### File: 2030b/app/Http/Controllers/BaseWebController.php

**Purpose**: Base web controller for web-specific functionality including flash messages, view data helpers, form handling, and web middleware management.

**Dependencies**:
```php
use App\Http\Controllers\Controller;
use Illuminate\Http\RedirectResponse;
use Illuminate\View\View;
use Illuminate\Http\Request;
use App\Traits\WebResponses;
use Illuminate\Support\Facades\Session;
```

**Methods**:
- redirectWithFlash(string $route, string $message, string $type): RedirectResponse
- viewWithData(string $view, array $data): View
- handleFormSubmission(Request $request, callable $action): RedirectResponse
- validateWebRequest(Request $request, array $rules): array
- setFlashMessage(string $message, string $type): void
- handleWebException(\Exception $e): RedirectResponse

**Do**:
- Standardize web response patterns
- Handle flash messages consistently
- Implement form submission helpers
- Provide view data helpers
- Handle web-specific validation
- Support CSRF protection
- Implement proper redirects
- Handle web exceptions gracefully

**Don't**:
- Don't ignore CSRF validation
- Don't skip flash message handling
- Don't return API responses in web controllers
- Don't bypass web middleware
- Don't ignore session management
