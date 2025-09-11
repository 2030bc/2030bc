# DOC_JOBS.md

Jobs handle time-consuming operations asynchronously using Laravel's queue system. They ensure application responsiveness while processing heavy tasks in the background and integrate seamlessly with the event-driven architecture and service layer. Updated.

## app/Jobs/ProcessCourseUpload.php

**Purpose**
→ Processes uploaded course files, extracts content, generates metadata, creates episodes and parts, and updates course structure with progress tracking.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;
use App\Services\Course\ContentService;
use App\Services\Media\FileStorageService;
use App\Models\Course\CourseUpload;
```

**Service Integration**
- **ContentService**: Delegates content extraction and processing
- **FileStorageService**: Manages file operations and storage
- **MediaService**: Handles media optimization and metadata

**Event Triggers**
- **Triggered By**: Course file upload completion event
- **Fires Events**: CourseProcessed, EpisodeCreated, ContentExtracted

**Console Command Relationship**
- **Related Command**: ProcessCourseUploads (console command for batch processing)
- **Difference**: Job processes individual uploads; command processes queued uploads in batches
- **Coordination**: Command dispatches multiple instances of this job

**Methods**
- handle(): Main processing logic delegated to ContentService
- extractContent(): Uses ContentService::extractContent()
- generateMetadata(): Uses ContentService::generateMetadata()
- createEpisodes(): Uses ContentService::createEpisodes()
- updateProgress(): Tracks processing progress
- failed(): Handles job failure with ContentService cleanup

**Do**
✅ Delegate business logic to ContentService
✅ Track processing progress for user feedback
✅ Handle large file uploads in chunks via ContentService
✅ Generate thumbnails through MediaService
✅ Fire events for completion notifications

**Don't**
❌ Don't implement content processing logic directly
❌ Don't ignore file size limits from ContentService
❌ Don't skip virus scanning via FileStorageService
❌ Don't create episodes without ContentService validation

---

## app/Jobs/CalculateUserPoints.php

**Purpose**
→ Recalculates user points based on activities, applies retroactive changes, handles point adjustments, and triggers level evaluations with audit trails.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use App\Services\User\LevelService;
use App\Services\User\UserPointsService;
use App\Models\User;
use App\Models\User\UserProgress;
use App\Enums\ActivityType;
```

**Service Integration**
- **UserPointsService**: Handles point calculations and adjustments
- **LevelService**: Manages level upgrades and evaluations
- **UserService**: Coordinates user state updates

**Event Triggers**
- **Triggered By**: EpisodeCompleted, CourseCompleted, AchievementUnlocked events
- **Fires Events**: UserLevelChanged, PointsAwarded, AchievementEarned

**Console Command Relationship**
- **Related Command**: CalculateUserLevels (recalculates levels system-wide)
- **Difference**: Job processes individual user point changes; command recalculates all user levels
- **Coordination**: Command may dispatch multiple instances for batch processing

**Methods**
- handle(): Coordinates with UserPointsService and LevelService
- calculatePoints(): Uses UserPointsService::calculatePoints()
- applyAdjustments(): Uses UserPointsService::applyAdjustments()
- checkLevelChanges(): Uses LevelService::evaluateLevel()
- auditChanges(): Uses UserPointsService::createAuditTrail()
- retryUntil(): Implements retry logic for point consistency

**Do**
✅ Use UserPointsService for atomic point calculations
✅ Create audit trails via UserPointsService
✅ Handle retroactive changes through LevelService
✅ Trigger level updates via LevelService
✅ Fire UserLevelChanged events for commission updates

**Don't**
❌ Don't implement point calculation logic directly
❌ Don't bypass LevelService for level changes
❌ Don't skip audit logging via UserPointsService
❌ Don't process without UserPointsService validation

---

## app/Jobs/SendWelcomeEmail.php

**Purpose**
→ Sends personalized welcome emails to new users with onboarding information, course recommendations, and affiliate program details using email templates.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Mail\Mailable;
use App\Models\User;
use App\Services\System\LocalizationService;
use App\Services\Course\SearchService;
use App\Services\System\NotificationService;
```

**Service Integration**
- **NotificationService**: Handles email delivery and tracking
- **LocalizationService**: Provides multi-language support
- **SearchService**: Generates course recommendations

**Event Triggers**
- **Triggered By**: UserRegistered event (fired from registration process)
- **Fires Events**: WelcomeEmailSent, UserOnboardingStarted

**Job Queue Integration**
- **Queue**: 'emails' queue for dedicated email processing
- **Delay**: Respects user timezone via LocalizationService
- **Retry**: Implements email delivery retry logic

**Methods**
- handle(): Coordinates with NotificationService
- personalizeContent(): Uses LocalizationService for personalization
- recommendCourses(): Uses SearchService::getRecommendations()
- getEmailTemplate(): Uses LocalizationService for templates
- trackDelivery(): Uses NotificationService::trackDelivery()

**Do**
✅ Use NotificationService for email delivery
✅ Personalize content via LocalizationService
✅ Include course recommendations from SearchService
✅ Support multiple languages via LocalizationService
✅ Track delivery status via NotificationService

**Don't**
❌ Don't send emails without NotificationService
❌ Don't ignore language preferences from LocalizationService
❌ Don't spam users without NotificationService validation
❌ Don't skip unsubscribe links via NotificationService

---

## app/Jobs/ProcessPayment.php

**Purpose**
→ Processes payment transactions asynchronously, handles payment provider communication, manages retries for failed payments, and updates order status.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use App\Services\Payment\PaymentService;
use App\Services\Payment\StripeService;
use App\Services\Payment\BinanceService;
use App\Models\Payment\Transaction;
use App\Enums\PaymentStatus;
use App\Enums\PaymentProvider;
```

**Service Integration**
- **PaymentService**: Central payment processing coordination
- **StripeService**: Stripe payment provider integration
- **BinanceService**: Cryptocurrency payment processing

**Event Triggers**
- **Triggered By**: PaymentInitiated event from payment controllers
- **Fires Events**: PaymentProcessed, PaymentSucceeded, PaymentFailed

**Webhook Integration**
- **Coordinates With**: StripeWebhookHandler, BinanceWebhookHandler
- **Idempotency**: Prevents duplicate processing with webhook events
- **Status Sync**: Synchronizes status between job processing and webhook updates

**Methods**
- handle(): Uses PaymentService::processPayment()
- processTransaction(): Delegates to appropriate service (Stripe/Binance)
- handleRetries(): Uses PaymentService retry logic
- updateStatus(): Uses PaymentService::updateTransactionStatus()
- notifyUser(): Uses NotificationService via PaymentService
- failed(): Uses PaymentService::handleFailedPayment()

**Do**
✅ Use PaymentService for all payment operations
✅ Implement retry logic via PaymentService
✅ Update transaction status through PaymentService
✅ Send confirmation emails via PaymentService integration
✅ Coordinate with webhook handlers for idempotency

**Don't**
❌ Don't bypass PaymentService for provider calls
❌ Don't ignore payment failures without PaymentService handling
❌ Don't process payments without proper service validation
❌ Don't duplicate webhook processing logic

---

## app/Jobs/GenerateInvoice.php

**Purpose**
→ Generates PDF invoices for completed transactions, includes tax calculations, supports multiple currencies, and sends invoices via email with proper formatting.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use App\Services\Payment\InvoiceService;
use App\Services\System\LocalizationService;
use App\Models\Payment\Invoice;
use Barryvdh\DomPDF\Facade\Pdf;
```

**Service Integration**
- **InvoiceService**: Handles invoice generation and management
- **LocalizationService**: Provides currency and language formatting
- **NotificationService**: Manages invoice email delivery

**Event Triggers**
- **Triggered By**: PaymentSucceeded event (after successful payment processing)
- **Fires Events**: InvoiceGenerated, InvoiceEmailed

**Methods**
- handle(): Uses InvoiceService::generateInvoice()
- generatePDF(): Uses InvoiceService::createPDF()
- calculateTaxes(): Uses InvoiceService::calculateTaxes()
- formatCurrency(): Uses LocalizationService::formatCurrency()
- sendInvoice(): Uses NotificationService via InvoiceService
- archiveInvoice(): Uses InvoiceService::archiveInvoice()

**Do**
✅ Use InvoiceService for all invoice operations
✅ Generate professional PDFs via InvoiceService
✅ Include tax information through InvoiceService
✅ Support multiple currencies via LocalizationService
✅ Archive invoices via InvoiceService for compliance

**Don't**
❌ Don't generate invoices without InvoiceService validation
❌ Don't ignore tax requirements from InvoiceService
❌ Don't skip currency conversion via LocalizationService
❌ Don't delete invoice records managed by InvoiceService

---

## app/Jobs/TrackAffiliateConversion.php

**Purpose**
→ Tracks affiliate conversions, calculates commissions, updates affiliate statistics, creates payout records, and handles multi-tier commission structures.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use App\Services\Affiliate\TrackingService;
use App\Services\Affiliate\CommissionService;
use App\Models\Affiliate\Conversion;
use App\Models\Affiliate\Commission;
use App\Enums\ConversionType;
```

**Service Integration**
- **TrackingService**: Handles conversion tracking and validation
- **CommissionService**: Manages commission calculations and payouts
- **AffiliateService**: Coordinates affiliate operations

**Event Triggers**
- **Triggered By**: PaymentSucceeded, UserRegistered, ReferralCompleted events
- **Fires Events**: ConversionTracked, AffiliateCommissionEarned, PayoutCreated

**Console Command Relationship**
- **Related Command**: ProcessCommissions (batch processes pending commissions)
- **Difference**: Job processes individual conversions; command handles bulk commission calculations
- **Coordination**: Command may dispatch jobs for complex commission scenarios

**Methods**
- handle(): Coordinates TrackingService and CommissionService
- trackConversion(): Uses TrackingService::trackConversion()
- calculateCommissions(): Uses CommissionService::calculateCommission()
- createPayouts(): Uses CommissionService::createPayout()
- updateStats(): Uses AffiliateService::updateStatistics()
- detectFraud(): Uses TrackingService::validateConversion()

**Do**
✅ Use TrackingService for accurate conversion tracking
✅ Calculate commissions via CommissionService
✅ Detect fraudulent activity through TrackingService
✅ Support multi-tier structures via CommissionService
✅ Update affiliate dashboards via AffiliateService

**Don't**
❌ Don't track conversions without TrackingService validation
❌ Don't bypass fraud detection in TrackingService
❌ Don't calculate commissions without CommissionService
❌ Don't skip validation via affiliate services

---

## app/Jobs/BackupDatabase.php

**Purpose**
→ Creates automated database backups using Spatie Backup package, compresses backup files, uploads to cloud storage, and maintains backup retention policies.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Spatie\Backup\BackupDestination\BackupDestination;
use App\Services\File\BackupService;
use Illuminate\Support\Facades\Storage;
```

**Service Integration**
- **BackupService**: Manages backup operations and retention
- **FileStorageService**: Handles file compression and storage

**Console Command Relationship**
- **Related Command**: BackupSystem, BackupDatabase (console commands)
- **Difference**: Job handles individual backup operations; commands coordinate system-wide backups
- **Coordination**: Commands dispatch this job for actual backup execution

**Event Triggers**
- **Triggered By**: Scheduled backup events, system maintenance events
- **Fires Events**: BackupCompleted, BackupFailed, BackupUploaded

**Methods**
- handle(): Uses BackupService::createBackup()
- createBackup(): Uses BackupService with Spatie integration
- compressFiles(): Uses FileStorageService::compressFiles()
- uploadToCloud(): Uses BackupService::uploadToCloud()
- cleanOldBackups(): Uses BackupService::maintainRetention()
- verifyBackup(): Uses BackupService::verifyIntegrity()

**Do**
✅ Use BackupService for all backup operations
✅ Compress backup files via FileStorageService
✅ Store in multiple locations via BackupService
✅ Verify backup integrity through BackupService
✅ Maintain retention policies via BackupService

**Don't**
❌ Don't backup without BackupService encryption
❌ Don't ignore backup failures from BackupService
❌ Don't keep unlimited backups without BackupService management
❌ Don't skip verification via BackupService

---

## app/Jobs/OptimizeImages.php

**Purpose**
→ Optimizes uploaded images using Spatie Image Optimizer, generates multiple sizes, creates WebP versions, and updates media library with optimized versions.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Spatie\ImageOptimizer\OptimizerChain;
use App\Services\Media\ImageOptimizationService;
```

**Service Integration**
- **ImageOptimizationService**: Handles image processing and optimization
- **MediaService**: Manages media library operations

**Console Command Relationship**
- **Related Command**: OptimizeImages (batch optimizes existing images)
- **Difference**: Job processes individual image uploads; command processes existing media
- **Coordination**: Command dispatches jobs for batch image optimization

**Event Triggers**
- **Triggered By**: MediaUploaded, ImageUploaded events
- **Fires Events**: ImageOptimized, MediaProcessed

**Methods**
- handle(): Uses ImageOptimizationService::optimize()
- optimizeImage(): Uses ImageOptimizationService with Spatie integration
- generateSizes(): Uses ImageOptimizationService::generateSizes()
- createWebP(): Uses ImageOptimizationService::createWebP()
- updateMetadata(): Uses MediaService::updateMetadata()
- cleanTempFiles(): Uses ImageOptimizationService::cleanup()

**Do**
✅ Use ImageOptimizationService for all optimizations
✅ Generate multiple sizes via ImageOptimizationService
✅ Create modern formats through ImageOptimizationService
✅ Update metadata via MediaService
✅ Clean temporary files via ImageOptimizationService

**Don't**
❌ Don't optimize without ImageOptimizationService validation
❌ Don't ignore original quality settings in ImageOptimizationService
❌ Don't skip format conversion via ImageOptimizationService
❌ Don't leave temp files without ImageOptimizationService cleanup

---

## app/Jobs/ProcessLargeCourseUpload.php

**Purpose**
→ Handles large course file uploads using chunked processing, progress tracking, temporary file management, and atomic course creation with rollback support.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use App\Services\Course\ContentService;
use App\Services\File\FileConverterService;
use App\Models\Course\CourseUpload;
```

**Service Integration**
- **ContentService**: Manages course content processing
- **FileConverterService**: Handles large file operations
- **MediaService**: Processes course media assets

**Console Command Relationship**
- **Related Command**: ProcessLargeCourseUpload (handles stuck uploads)
- **Difference**: Job processes individual large uploads; command recovers failed operations
- **Coordination**: Command dispatches jobs for retry scenarios

**Event Triggers**
- **Triggered By**: LargeCourseUploadStarted event
- **Fires Events**: CourseUploadProgress, CourseProcessed, UploadCompleted

**Methods**
- handle(): Coordinates ContentService and FileConverterService
- processChunks(): Uses FileConverterService::processChunks()
- trackProgress(): Uses ContentService::updateProgress()
- mergeFiles(): Uses FileConverterService::mergeFiles()
- createCourse(): Uses ContentService::createCourse()
- rollbackOnFailure(): Uses ContentService::rollbackUpload()

**Do**
✅ Use FileConverterService for chunked processing
✅ Track progress via ContentService
✅ Handle interruptions through ContentService
✅ Provide rollback via ContentService
✅ Clean temporary files via FileConverterService

**Don't**
❌ Don't process entire files without FileConverterService chunking
❌ Don't ignore progress tracking in ContentService
❌ Don't skip error handling via ContentService
❌ Don't leave partial uploads without ContentService cleanup

---

## app/Jobs/GenerateCourseCertificate.php

**Purpose**
→ Generates completion certificates for users who finish courses, creates personalized PDF certificates, stores them securely, and sends notification emails.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use App\Services\Course\CertificationService;
use App\Models\User;
use App\Models\Course\DocuCourse;
use Barryvdh\DomPDF\Facade\Pdf;
```

**Service Integration**
- **CertificationService**: Manages certificate generation and validation
- **LocalizationService**: Provides multi-language certificate templates
- **NotificationService**: Handles certificate delivery emails

**Console Command Relationship**
- **Related Command**: GenerateCourseCertificates (bulk certificate generation)
- **Difference**: Job generates individual certificates; command processes bulk requests
- **Coordination**: Command dispatches jobs for efficient certificate creation

**Event Triggers**
- **Triggered By**: CourseCompleted, EpisodeCompleted events (course completion)
- **Fires Events**: CertificateGenerated, CertificateDelivered

**Methods**
- handle(): Uses CertificationService::generateCertificate()
- generateCertificate(): Uses CertificationService with PDF generation
- personalizeContent(): Uses LocalizationService for personalization
- storeCertificate(): Uses CertificationService::storeCertificate()
- sendNotification(): Uses NotificationService via CertificationService
- validateCompletion(): Uses CertificationService::validateCompletion()

**Do**
✅ Use CertificationService for certificate validation and generation
✅ Generate professional certificates via CertificationService
✅ Personalize content through LocalizationService
✅ Store securely via CertificationService
✅ Send notifications via CertificationService integration

**Don't**
❌ Don't generate without CertificationService validation
❌ Don't create duplicate certificates via CertificationService
❌ Don't ignore personalization from LocalizationService
❌ Don't skip security measures in CertificationService

---

## app/Jobs/SendBulkNotifications.php

**Purpose**
→ Sends bulk notifications to multiple users efficiently, supports different notification channels, handles rate limiting, and provides delivery tracking.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use App\Services\System\NotificationService;
use Illuminate\Support\Collection;
use App\Models\User;
```

**Methods**
- handle()
- batchUsers()
- sendNotifications()
- handleRateLimit()
- trackDelivery()
- processFailures()

**Do**
✅ Process users in batches
✅ Respect rate limits
✅ Track delivery status
✅ Handle failures gracefully
✅ Support multiple channels

**Don't**
❌ Don't spam all users at once
❌ Don't ignore rate limits
❌ Don't skip delivery tracking
❌ Don't send to unsubscribed users

---

## app/Jobs/ProcessVideoConversion.php

**Purpose**
→ Converts uploaded video files to web-compatible formats, generates thumbnails, creates multiple quality versions, and updates media metadata.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use App\Services\File\FileConverterService;
use App\Services\Media\MediaService;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
```

**Methods**
- handle()
- convertVideo()
- generateThumbnails()
- createQualities()
- updateMetadata()
- cleanSourceFiles()

**Do**
✅ Convert to web formats
✅ Generate quality versions
✅ Create video thumbnails
✅ Update file metadata
✅ Clean up source files

**Don't**
❌ Don't ignore format compatibility
❌ Don't skip quality optimization
❌ Don't leave source files
❌ Don't ignore metadata

---

## app/Jobs/BackupToCloudStorage.php

**Purpose**
→ Uploads backup files to cloud storage providers (AWS S3, Google Cloud), manages backup retention, encrypts sensitive backups, and verifies upload integrity.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Support\Facades\Storage;
use App\Services\File\BackupService;
use League\Flysystem\AwsS3V3\AwsS3V3Adapter;
```

**Methods**
- handle()
- uploadBackup()
- encryptFile()
- verifyUpload()
- cleanLocalBackup()
- updateBackupLog()

**Do**
✅ Encrypt sensitive backups
✅ Verify upload integrity
✅ Maintain backup logs
✅ Clean local files after upload
✅ Handle upload failures

**Don't**
❌ Don't upload unencrypted data
❌ Don't ignore upload failures
❌ Don't skip integrity checks
❌ Don't keep unlimited 

---

## Queue Configuration and Failed Job Handling

### Queue Configuration
Jobs are organized into specialized queues for optimal performance:

**Queue Assignments**:
- `default`: Standard processing jobs
- `payments`: Payment processing jobs (ProcessPayment, GenerateInvoice)
- `media`: Media processing jobs (OptimizeImages, ProcessVideoConversion)
- `emails`: Email jobs (SendWelcomeEmail, SendBulkNotifications)
- `backups`: System maintenance jobs (BackupDatabase, BackupToCloudStorage)
- `affiliates`: Affiliate processing jobs (TrackAffiliateConversion)

### Failed Job Handling
Per TESTING_GUIDE.md requirements:

**Retry Policies**:
- Payment jobs: 3 retries with exponential backoff
- Media processing: 2 retries with linear backoff
- Email jobs: 5 retries with rate limiting
- Critical jobs: Immediate admin notification on failure

**Failed Job Recovery**:
- Automatic retry for transient failures
- Manual intervention for data consistency issues
- Rollback mechanisms via service layer
- Audit trail maintenance for all failures

### Service Layer Integration
All jobs delegate business logic to appropriate service classes:

**Service Benefits**:
- Consistent business logic across jobs and controllers
- Proper transaction handling and error management
- Testable business logic independent of queue infrastructure
- Reusable logic for both synchronous and asynchronous operations

**Anti-Pattern Prevention**:
- Jobs never implement business logic directly
- Database operations always go through service layer
- Event firing coordinated through service classes
- External API calls managed by service classes
