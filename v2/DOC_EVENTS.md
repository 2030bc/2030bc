# DOC_EVENTS.md

Events are domain events that represent something meaningful that happened in the application. They enable decoupled communication between different parts of the system using Laravel's event system. Updated.

## app/Events/Handler.php

**Purpose**
→ Enhanced event handler that provides centralized event management and advanced event processing capabilities with logging, error handling, and event chaining.

**Dependencies**
```php
use Illuminate\Events\Dispatcher;
use Illuminate\Contracts\Events\Dispatcher as DispatcherContract;
use Illuminate\Support\Facades\Log;
use App\Foundation\Core\ApplicationService;
use App\Contracts\Services\NotificationServiceInterface;
```

**Methods**
- handle()
- dispatch()
- dispatchBatch()
- listen()
- subscribe()
- logEvent()
- handleException()
- getListeners()
- validateEvent()

**Do**
✅ Use for centralized event management
✅ Implement proper error handling and logging
✅ Support batch event dispatching
✅ Validate events before processing
✅ Maintain event audit trail

**Don't**
❌ Don't process business logic directly in handler
❌ Don't ignore event validation
❌ Don't let exceptions stop event chain
❌ Don't dispatch events synchronously for heavy operations

---

## app/Events/EpisodeCompleted.php

**Purpose**
→ Fired when a user completes an episode, triggering point calculation, progress tracking, level updates, and completion notifications.

**Dependencies**
```php
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;
use Illuminate\Broadcasting\InteractsWithSockets;
use App\Models\User;
use App\Models\Course\Episode;
use App\Events\Contracts\DomainEventInterface;
```

**Handled By Listeners**
- UpdateUserProgress (tracks episode completion progress)
- AwardPointsForCompletion (awards completion points)
- UpdateCourseMetrics (updates course statistics)

**Methods**
- __construct()
- broadcastOn()
- broadcastWith()
- broadcastAs()

**Properties**
- $user
- $episode
- $completionTime
- $timeSpent
- $finalPosition

**Do**
✅ Include completion timestamp and duration
✅ Carry user and episode models
✅ Support broadcasting for real-time updates
✅ Include progress metrics
✅ Enable queued processing

**Don't**
❌ Don't include sensitive user data in broadcast
❌ Don't perform heavy calculations in event
❌ Don't modify database state directly
❌ Don't dispatch synchronously for large user bases

---

## app/Events/UserLevelChanged.php

**Purpose**
→ Triggered when user's level changes due to point accumulation, unlocking new benefits, access levels, and commission rates in the affiliate system.

**Dependencies**
```php
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;
use Illuminate\Broadcasting\InteractsWithSockets;
use App\Models\User;
use App\Models\User\UserLevel;
use App\Enums\UserLevel as UserLevelEnum;
```

**Handled By Listeners**
- SendAffiliateNotification (notifies about commission rate changes)
- LogUserActivity (logs level change activity)

**Methods**
- __construct()
- broadcastOn()
- broadcastWith()
- shouldBroadcast()

**Properties**
- $user
- $previousLevel
- $newLevel
- $pointsEarned
- $benefitsUnlocked

**Do**
✅ Track both previous and new levels
✅ Include benefits that were unlocked
✅ Support real-time notifications
✅ Include point calculation details
✅ Enable affiliate rate updates

**Don't**
❌ Don't broadcast sensitive benefit details
❌ Don't perform level validation in event
❌ Don't modify user permissions directly
❌ Don't ignore commission rate changes

---

## app/Events/PaymentProcessed.php

**Purpose**
→ Dispatched after successful payment processing, triggering invoice generation, receipt notifications, affiliate commissions, and course access grants.

**Dependencies**
```php
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;
use App\Models\Payment\Transaction;
use App\Models\User;
use App\Models\Course\DocuCourse;
use App\Enums\PaymentStatus;
use App\Enums\TransactionType;
```

**Handled By Listeners**
- HandlePaymentSuccess (processes enrollment and access)
- ProcessCommissionPayment (handles affiliate commissions)

**Methods**
- __construct()
- broadcastOn()
- shouldQueue()

**Properties**
- $transaction
- $user
- $course
- $paymentMethod
- $affiliateCode

**Do**
✅ Include complete transaction details
✅ Support affiliate tracking
✅ Enable invoice generation
✅ Trigger access provisioning
✅ Queue for performance

**Don't**
❌ Don't include payment credentials
❌ Don't process refunds in this event
❌ Don't grant access before payment verification
❌ Don't ignore affiliate attribution

---

## app/Events/ReferralCompleted.php

**Purpose**
→ Fired when a referral converts (signup or purchase), calculating commissions, updating affiliate statistics, and triggering payout processing.

**Dependencies**
```php
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;
use App\Models\Affiliate\Affiliate;
use App\Models\Affiliate\ReferralCode;
use App\Models\User;
use App\Enums\ConversionType;
```

**Handled By Listeners**
- ProcessCommissionPayment (processes affiliate commissions)
- SendAffiliateNotification (notifies affiliate of conversion)

**Methods**
- __construct()
- getCommissionAmount()
- shouldProcessPayout()

**Properties**
- $affiliate
- $referralCode
- $convertedUser
- $conversionType
- $conversionValue

**Do**
✅ Calculate commission accurately
✅ Track conversion attribution
✅ Support different conversion types
✅ Include conversion value
✅ Enable payout processing

**Don't**
❌ Don't duplicate commission calculations
❌ Don't ignore conversion windows
❌ Don't process fraudulent referrals
❌ Don't pay commissions before verification

---

## app/Events/CoursePublished.php

**Purpose**
→ Triggered when a course is published, sending notifications to subscribers, updating search indexes, generating sitemaps, and marketing campaign triggers.

**Dependencies**
```php
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Broadcasting\InteractsWithSockets;
use App\Models\Course\DocuCourse;
use App\Models\User;
use App\Enums\CourseStatus;
```

**Handled By Listeners**
- SendBulkNotifications (notifies interested users)
- UpdateCourseMetrics (updates course catalogs)

**Methods**
- __construct()
- broadcastOn()
- broadcastWith()

**Properties**
- $course
- $author
- $publishedAt
- $category
- $targetAudience

**Do**
✅ Notify interested users
✅ Update search indexes
✅ Generate marketing materials
✅ Create social media posts
✅ Update course catalogs

**Don't**
❌ Don't publish incomplete courses
❌ Don't spam all users
❌ Don't ignore SEO updates
❌ Don't skip content validation

---

## app/Events/UserRegistered.php

**Purpose**
→ Dispatched on new user registration, triggering welcome emails, affiliate attribution, initial level assignment, and onboarding sequence initiation.

**Dependencies**
```php
use Illuminate\Auth\Events\Registered;
use Illuminate\Foundation\Events\Dispatchable;
use App\Models\User;
use App\Models\Affiliate\ReferralCode;
```

**Handled By Listeners**
- SendEmailVerificationNotification (sends verification email)
- SendNewUserNotification (notifies administrators)
- LogUserActivity (logs registration activity)

**Methods**
- __construct()
- shouldSendWelcomeEmail()
- hasAffiliateReferral()

**Properties**
- $user
- $referralCode
- $registrationSource
- $initialLevel
- $preferences

**Do**
✅ Track registration source
✅ Process affiliate referrals
✅ Assign initial user level
✅ Send welcome communications
✅ Initialize user preferences

**Don't**
❌ Don't send emails before email verification
❌ Don't ignore referral attribution
❌ Don't skip initial setup
❌ Don't create duplicate accounts

---

## app/Events/AffiliateCommissionEarned.php

**Purpose**
→ Fired when affiliate earns commission from successful referral, updating balances, creating payout records, and sending earning notifications.

**Dependencies**
```php
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;
use App\Models\Affiliate\Affiliate;
use App\Models\Affiliate\Commission;
use App\Models\Payment\Currency;
use App\Enums\CommissionStatus;
```

**Handled By Listeners**
- ProcessCommissionPayment (processes commission payouts)
- SendAffiliateNotification (notifies affiliate of earnings)

**Methods**
- __construct()
- calculateTax()
- shouldCreatePayout()

**Properties**
- $affiliate
- $commission
- $conversion
- $currency
- $payoutSchedule

**Do**
✅ Update affiliate balance accurately
✅ Create payout records
✅ Handle multi-currency commissions
✅ Apply tax calculations
✅ Send earning notifications

**Don't**
❌ Don't pay before conversion verification
❌ Don't ignore currency conversions
❌ Don't skip tax compliance
❌ Don't create duplicate payouts

---

## Missing Events Referenced in Listeners

### ConversionApproved Event
**Referenced By**: ProcessCommissionPayment listener
**Status**: Missing from events documentation
**Required Properties**: $conversion, $approvalTimestamp, $approver

### PaymentSucceeded Event
**Referenced By**: HandlePaymentSuccess listener
**Status**: Missing from events documentation
**Required Properties**: $transaction, $paymentProvider, $confirmationCode

### ConversionCreated Event
**Referenced By**: SendAffiliateNotification listener
**Status**: Missing from events documentation
**Required Properties**: $conversion, $affiliate, $referralSource

### CommissionPaid Event
**Referenced By**: SendAffiliateNotification listener
**Status**: Missing from events documentation
**Required Properties**: $commission, $affiliate, $paymentDetails

---

## Event-Listener Mapping Matrix

| Event | Listeners |
|-------|-----------|
| EpisodeCompleted | UpdateUserProgress, AwardPointsForCompletion, UpdateCourseMetrics |
| UserLevelChanged | SendAffiliateNotification, LogUserActivity |
| PaymentProcessed | HandlePaymentSuccess, ProcessCommissionPayment |
| ReferralCompleted | ProcessCommissionPayment, SendAffiliateNotification |
| CoursePublished | SendBulkNotifications, UpdateCourseMetrics |
| UserRegistered | SendEmailVerificationNotification, SendNewUserNotification, LogUserActivity |
| AffiliateCommissionEarned | ProcessCommissionPayment, SendAffiliateNotification |
| ConversionApproved* | ProcessCommissionPayment |
| PaymentSucceeded* | HandlePaymentSuccess |
| ConversionCreated* | SendAffiliateNotification |
| CommissionPaid* | SendAffiliateNotification |

*Events marked with asterisk are referenced in listeners but missing from events documentation

---

## Event Contracts Implementation

### app/Events/Contracts/DomainEventInterface.php

**Purpose**: Defines the contract for domain events that represent significant business state changes within the application's domain layer.

**Dependencies**:
```php
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Carbon\Carbon;
use Ramsey\Uuid\UuidInterface;
```

**Methods**:
- getEventId(): UuidInterface
- getAggregateId(): string
- getEventName(): string
- getOccurredAt(): Carbon
- getEventData(): array
- getEventVersion(): int

**Do**:
- Implement immutable event data
- Use UUIDs for event identification
- Include timestamp information
- Maintain event versioning
- Follow domain-driven design principles

**Don't**:
- Don't modify event data after creation
- Don't include sensitive information in event data
- Don't couple events to infrastructure concerns
- Don't create events for trivial state changes

---

## app/Events/Contracts/ShouldBroadcastInterface.php

**Purpose**: Contract for events that should be broadcast to real-time channels using Laravel's broadcasting system.

**Dependencies**:
```php
use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\PresenceChannel;
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
```

**Methods**:
- broadcastOn(): array
- broadcastWith(): array
- broadcastAs(): string
- broadcastWhen(): bool
- broadcastQueue(): string

**Do**:
- Define specific broadcast channels
- Filter sensitive data in broadcastWith()
- Use appropriate channel types (public/private/presence)
- Queue broadcast operations for performance

**Don't**:
- Don't broadcast sensitive user data
- Don't use synchronous broadcasting in production
- Don't broadcast every event unnecessarily
- Don't ignore channel authorization

---

## app/Events/Contracts/ShouldQueueInterface.php

**Purpose**: Contract for events that should be processed asynchronously through the queue system for performance optimization.

**Dependencies**:
```php
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\SerializesModels;
```

**Methods**:
- queue(): string
- delay(): int
- timeout(): int
- retryUntil(): Carbon
- failed(): void
- shouldQueue(): bool

**Do**:
- Use queues for time-intensive operations
- Set appropriate timeouts and retries
- Handle failed queue jobs gracefully
- Monitor queue performance

**Don't**:
- Don't queue critical real-time operations
- Don't ignore queue failure handling
- Don't use default queue for all operations
- Don't queue without proper serialization

---

## app/Events/Contracts/EventHandlerInterface.php

**Purpose**: Defines the contract for event handlers that process domain events and coordinate side effects.

**Dependencies**:
```php
use App\Events\Contracts\DomainEventInterface;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Facades\DB;
```

**Methods**:
- handle(DomainEventInterface $event): void
- canHandle(DomainEventInterface $event): bool
- getPriority(): int
- shouldRetry(): bool
- getMaxRetries(): int

**Do**:
- Implement idempotent event handling
- Use database transactions for consistency
- Log important event processing
- Handle exceptions gracefully

**Don't**:
- Don't create side effects in event handlers
- Don't ignore event handling failures
- Don't process events synchronously in web requests
- Don't couple handlers to specific event implementations

---

## EventServiceProvider Registration

The EventServiceProvider should include the following event-listener mappings:

```php
protected $listen = [
    'App\Events\EpisodeCompleted' => [
        'App\Listeners\UpdateUserProgress',
        'App\Listeners\AwardPointsForCompletion',
        'App\Listeners\UpdateCourseMetrics',
    ],
    'App\Events\UserRegistered' => [
        'App\Listeners\SendEmailVerificationNotification',
        'App\Listeners\SendNewUserNotification',
        'App\Listeners\LogUserActivity',
    ],
    'App\Events\PaymentProcessed' => [
        'App\Listeners\HandlePaymentSuccess',
        'App\Listeners\ProcessCommissionPayment',
    ],
    'App\Events\ReferralCompleted' => [
        'App\Listeners\ProcessCommissionPayment',
        'App\Listeners\SendAffiliateNotification',
    ],
    'App\Events\AffiliateCommissionEarned' => [
        'App\Listeners\ProcessCommissionPayment',
        'App\Listeners\SendAffiliateNotification',
    ],
];
```
