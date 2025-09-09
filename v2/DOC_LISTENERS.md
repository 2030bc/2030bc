# DOC_LISTENERS.md

## File: 2030b/app/Listeners/SendEmailVerificationNotification.php

**Purpose**: Handles sending email verification notifications when users register or request email verification.

**Dependencies**:
```php
use App\Events\Registered;
use App\Notifications\EmailVerificationNotification;
use Illuminate\Auth\Events\Registered as LaravelRegistered;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Support\Facades\Mail;
use Illuminate\Support\Facades\Log;
```

**Methods**:
- handle(Registered $event): void
- shouldSend(User $user): bool
- getNotificationData(User $user): array
- failed(Registered $event, Throwable $exception): void

**Do**:
- Queue email sending for performance
- Validate email addresses before sending
- Log email sending attempts
- Handle email sending failures gracefully

**Don't**:
- Don't send emails synchronously
- Don't expose sensitive user data in logs
- Don't ignore email delivery failures
- Don't send duplicate verification emails

## File: 2030b/app/Listeners/UpdateUserProgress.php

**Purpose**: Updates user progress tracking when course episodes are completed or accessed.

**Dependencies**:
```php
use App\Events\EpisodeCompleted;
use App\Events\EpisodeAccessed;
use App\Models\UserProgress;
use App\Services\User\UserProgressService;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Support\Facades\Cache;
```

**Methods**:
- handle(EpisodeCompleted $event): void
- updateProgress(User $user, Episode $episode, int $percentage): void
- calculateTotalProgress(User $user): float
- awardCompletionBadge(User $user): void
- clearProgressCache(User $user): void

**Do**:
- Update progress atomically
- Calculate accurate completion percentages
- Cache progress calculations
- Award achievements for milestones

**Don't**:
- Don't allow progress manipulation
- Don't ignore progress calculation errors
- Don't update progress without validation
- Don't create duplicate progress records

## File: 2030b/app/Listeners/AwardPointsForCompletion.php

**Purpose**: Awards points to users when they complete course episodes or achieve learning milestones.

**Dependencies**:
```php
use App\Events\EpisodeCompleted;
use App\Events\CourseCompleted;
use App\Services\User\UserPointsService;
use App\Models\User;
use App\Enums\PointsSource;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Support\Facades\DB;
```

**Methods**:
- handle(EpisodeCompleted $event): void
- calculatePoints(Episode $episode): int
- awardBonusPoints(User $user, string $achievement): void
- updateUserLevel(User $user): bool
- logPointsAwarded(User $user, int $points, string $source): void

**Do**:
- Use database transactions for points awarding
- Calculate points based on episode difficulty
- Check for level upgrades after awarding points
- Log all points transactions

**Don't**:
- Don't award points multiple times for same completion
- Don't allow negative points manipulation
- Don't bypass points validation rules
- Don't ignore points calculation errors

## File: 2030b/app/Listeners/ProcessCommissionPayment.php

**Purpose**: Processes affiliate commission payments when conversions are approved and payable.

**Dependencies**:
```php
use App\Events\ConversionApproved;
use App\Services\Affiliate\CommissionService;
use App\Services\Payment\PaymentService;
use App\Models\Commission;
use App\Enums\CommissionStatus;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Support\Facades\DB;
```

**Methods**:
- handle(ConversionApproved $event): void
- calculateCommission(Conversion $conversion): float
- processPayment(Commission $commission): bool
- updateCommissionStatus(Commission $commission, string $status): void
- handlePaymentFailure(Commission $commission, string $reason): void

**Do**:
- Use database transactions for commission processing
- Validate commission calculations
- Handle payment failures gracefully
- Log all commission transactions

**Don't**:
- Don't process duplicate commission payments
- Don't ignore payment validation rules
- Don't bypass commission approval workflow
- Don't expose payment details in logs

## File: 2030b/app/Listeners/LogUserActivity.php

**Purpose**: Logs user activities and interactions for audit trails and analytics purposes.

**Dependencies**:
```php
use App\Events\UserLoggedIn;
use App\Events\UserLoggedOut;
use App\Events\EpisodeAccessed;
use App\Models\ActivityLog;
use App\Services\Analytics\ActivityService;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Http\Request;
```

**Methods**:
- handle($event): void
- logActivity(User $user, string $action, array $properties): void
- getClientInfo(Request $request): array
- shouldLogActivity(User $user, string $action): bool
- cleanOldLogs(): void

**Do**:
- Log important user actions
- Include relevant context data
- Respect user privacy settings
- Clean old logs regularly

**Don't**:
- Don't log sensitive information
- Don't log every minor interaction
- Don't ignore privacy regulations
- Don't create excessive log entries

## File: 2030b/app/Listeners/SendNewUserNotification.php

**Purpose**: Sends notifications to administrators when new users register on the platform.

**Dependencies**:
```php
use App\Events\Registered;
use App\Notifications\NewUserRegisteredNotification;
use App\Models\User;
use App\Services\User\UserService;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Support\Facades\Notification;
```

**Methods**:
- handle(Registered $event): void
- getAdminUsers(): Collection
- shouldNotifyAdmins(User $user): bool
- getNotificationData(User $user): array
- failed(Registered $event, Throwable $exception): void

**Do**:
- Queue notifications for performance
- Filter admin notification recipients
- Include relevant user information
- Handle notification failures

**Don't**:
- Don't send notifications synchronously
- Don't include sensitive user data
- Don't spam administrators with notifications
- Don't ignore notification preferences

## File: 2030b/app/Listeners/HandlePaymentSuccess.php

**Purpose**: Handles successful payment confirmations and updates related entities and user access.

**Dependencies**:
```php
use App\Events\PaymentSucceeded;
use App\Services\Payment\PaymentService;
use App\Services\Course\EnrollmentService;
use App\Models\Transaction;
use App\Enums\TransactionType;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Support\Facades\DB;
```

**Methods**:
- handle(PaymentSucceeded $event): void
- processEnrollment(Transaction $transaction): void
- updateUserAccess(User $user, Course $course): void
- sendPaymentConfirmation(Transaction $transaction): void
- handleRefundRequest(Transaction $transaction): void

**Do**:
- Process payments atomically
- Update user access immediately
- Send payment confirmations
- Log payment processing

**Don't**:
- Don't ignore payment validation
- Don't grant access without payment confirmation
- Don't process duplicate payments
- Don't expose payment details

## File: 2030b/app/Listeners/CleanupTemporaryFiles.php

**Purpose**: Cleans up temporary files and uploads that are no longer needed to maintain system storage.

**Dependencies**:
```php
use App\Events\FileProcessingCompleted;
use App\Events\UploadCancelled;
use App\Services\Media\FileCleanupService;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Facades\Log;
```

**Methods**:
- handle($event): void
- cleanupTemporaryFiles(): void
- removeOrphanedFiles(): void
- scheduleCleanup(string $filePath, Carbon $deleteAt): void
- isFileInUse(string $filePath): bool

**Do**:
- Schedule cleanup operations
- Verify files are not in use before deletion
- Log cleanup operations
- Handle cleanup failures gracefully

**Don't**:
- Don't delete files immediately
- Don't ignore file usage verification
- Don't cleanup without proper scheduling
- Don't delete permanent files

## File: 2030b/app/Listeners/UpdateCourseMetrics.php

**Purpose**: Updates course metrics and statistics when course-related events occur.

**Dependencies**:
```php
use App\Events\EpisodeCompleted;
use App\Events\CourseEnrolled;
use App\Services\Analytics\CourseMetricsService;
use App\Models\DocuCourse;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\DB;
```

**Methods**:
- handle($event): void
- updateEnrollmentCount(DocuCourse $course): void
- updateCompletionRate(DocuCourse $course): void
- updateAverageRating(DocuCourse $course): void
- recalculateMetrics(DocuCourse $course): void
- clearMetricsCache(DocuCourse $course): void

**Do**:
- Update metrics asynchronously
- Cache calculated metrics
- Recalculate periodically
- Handle metric calculation errors

**Don't**:
- Don't calculate metrics synchronously
- Don't ignore cache invalidation
- Don't update metrics without validation
- Don't create inconsistent metrics

## File: 2030b/app/Listeners/SendAffiliateNotification.php

**Purpose**: Sends notifications to affiliates about their referrals, conversions, and commission updates.

**Dependencies**:
```php
use App\Events\ConversionCreated;
use App\Events\CommissionPaid;
use App\Notifications\AffiliateNotification;
use App\Models\Affiliate;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Support\Facades\Notification;
```

**Methods**:
- handle($event): void
- notifyAffiliate(Affiliate $affiliate, string $type, array $data): void
- shouldNotify(Affiliate $affiliate, string $type): bool
- getNotificationPreferences(Affiliate $affiliate): array
- failed($event, Throwable $exception): void

**Do**:
- Queue affiliate notifications
- Respect notification preferences
- Include relevant commission data
- Handle notification failures

**Don't**:
- Don't send unwanted notifications
- Don't include sensitive commission details
- Don't ignore affiliate preferences
- Don't send duplicate notifications
