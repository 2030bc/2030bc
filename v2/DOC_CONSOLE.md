# DOC_CONSOLE.md

## 2030b/app/Console/Kernel.php

**Purpose**: Console kernel managing artisan commands registration, task scheduling, and command execution pipeline.

**Dependencies**:
```php
use Illuminate\Console\Scheduling\Schedule;
use Illuminate\Foundation\Console\Kernel as ConsoleKernel;
use App\Console\Commands\ProcessCourseUploads;
use App\Console\Commands\CalculateUserLevels;
use App\Console\Commands\ProcessCommissions;
use App\Console\Commands\BackupSystem;
use App\Console\Commands\CleanupOldData;
```

**Methods**:
- commands(): void
- schedule(Schedule $schedule): void
- bootstrappers(): array
- registerCommands(): void
- scheduleBackups(): void
- scheduleCleanup(): void
- scheduleProcessing(): void

**Do**:
- Register all custom artisan commands
- Configure command scheduling with proper frequencies
- Set up command overlapping prevention
- Configure command output and logging
- Handle command failure notifications

**Don't**:
- Don't schedule resource-intensive commands during peak hours
- Don't ignore command execution failures
- Don't schedule commands without proper error handling
- Don't forget to configure command timeouts

---

## 2030b/app/Console/Commands/ProcessCourseUploads.php

**Purpose**: Background command for processing course file uploads, video conversions, and media optimization.

**Dependencies**:
```php
use Illuminate\Console\Command;
use App\Services\Course\CourseUploadService;
use App\Services\Media\VideoProcessingService;
use App\Models\CourseUpload;
use Illuminate\Support\Facades\Storage;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
```

**Methods**:
- handle(): int
- processUploadQueue(): void
- processVideoFile(CourseUpload $upload): void
- generateThumbnails(Media $media): void
- optimizeVideo(string $path): void
- updateUploadStatus(CourseUpload $upload, string $status): void

**Do**:
- Process uploads in background queues
- Generate video thumbnails and previews
- Validate file types and sizes
- Update upload progress status
- Handle processing failures gracefully

**Don't**:
- Don't process large files without chunking
- Don't ignore file validation errors
- Don't skip virus scanning for uploads
- Don't forget to clean up temporary files

---

## 2030b/app/Console/Commands/CalculateUserLevels.php

**Purpose**: Command for calculating and updating user levels based on points, achievements, and activity metrics.

**Dependencies**:
```php
use Illuminate\Console\Command;
use App\Services\User\UserLevelService;
use App\Models\User;
use App\Models\UserProgress;
use App\Enums\UserLevel;
use App\Events\UserLevelChanged;
```

**Methods**:
- handle(): int
- calculateUserLevels(): void
- updateUserLevel(User $user): void
- calculateUserPoints(User $user): int
- awardLevelBenefits(User $user, UserLevel $newLevel): void
- notifyLevelChange(User $user, UserLevel $oldLevel, UserLevel $newLevel): void

**Do**:
- Calculate levels based on standardized point system
- Fire events for level changes
- Award level-specific benefits and badges
- Log level calculation history
- Handle bulk level updates efficiently

**Don't**:
- Don't modify levels without proper validation
- Don't ignore point calculation errors
- Don't skip level change notifications
- Don't process all users in single batch

---

## 2030b/app/Console/Commands/ProcessCommissions.php

**Purpose**: Command for processing affiliate commissions, calculating payouts, and managing commission payments.

**Dependencies**:
```php
use Illuminate\Console\Command;
use App\Services\Affiliate\CommissionService;
use App\Models\Commission;
use App\Models\Transaction;
use App\Enums\CommissionStatus;
use App\Events\CommissionProcessed;
```

**Methods**:
- handle(): int
- processPendingCommissions(): void
- calculateCommissionAmount(Transaction $transaction): float
- validateCommissionEligibility(Commission $commission): bool
- processCommissionPayment(Commission $commission): void
- updateCommissionStatus(Commission $commission, CommissionStatus $status): void

**Do**:
- Process commissions based on conversion events
- Validate commission eligibility rules
- Calculate tiered commission rates
- Handle commission payment processing
- Track commission payment history

**Don't**:
- Don't process duplicate commissions
- Don't ignore commission validation rules
- Don't process commissions without proper verification
- Don't skip commission fraud detection

---

## 2030b/app/Console/Commands/BackupSystem.php

**Purpose**: System backup command managing database backups, file system backups, and backup rotation.

**Dependencies**:
```php
use Illuminate\Console\Command;
use Spatie\Backup\BackupDestination\BackupDestination;
use Spatie\Backup\Tasks\Backup\BackupJobFactory;
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Facades\DB;
```

**Methods**:
- handle(): int
- performDatabaseBackup(): void
- performFileSystemBackup(): void
- cleanupOldBackups(): void
- verifyBackupIntegrity(): bool
- sendBackupNotifications(): void

**Do**:
- Create encrypted database backups
- Include essential file system data
- Rotate backups based on retention policy
- Verify backup integrity after creation
- Send backup status notifications

**Don't**:
- Don't store backups only locally
- Don't include temporary or cache files
- Don't ignore backup verification failures
- Don't forget to encrypt sensitive backups

---

## 2030b/app/Console/Commands/CleanupOldData.php

**Purpose**: Data cleanup command for removing expired data, temporary files, and maintaining database performance.

**Dependencies**:
```php
use Illuminate\Console\Command;
use App\Models\UserSession;
use App\Models\ActivityLog;
use App\Models\Media;
use Carbon\Carbon;
use Illuminate\Support\Facades\Storage;
```

**Methods**:
- handle(): int
- cleanupExpiredSessions(): void
- cleanupOldLogs(): void
- cleanupOrphanedMedia(): void
- cleanupTempFiles(): void
- optimizeDatabaseTables(): void

**Do**:
- Remove expired user sessions and tokens
- Clean up old activity logs based on retention
- Remove orphaned media files
- Clear temporary file storage
- Optimize database tables after cleanup

**Don't**:
- Don't delete data without proper retention policies
- Don't skip data integrity checks
- Don't clean up referenced data
- Don't ignore cleanup failure notifications

---

## 2030b/app/Console/Commands/GenerateCourseCertificates.php

**Purpose**: Certificate generation command for creating course completion certificates and managing certificate distribution.

**Dependencies**:
```php
use Illuminate\Console\Command;
use App\Services\Course\CertificateService;
use App\Models\UserProgress;
use App\Models\DocuCourse;
use Barryvdh\DomPDF\Facade\Pdf;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
```

**Methods**:
- handle(): int
- generatePendingCertificates(): void
- createCertificatePDF(UserProgress $progress): Media
- validateCertificateEligibility(UserProgress $progress): bool
- sendCertificateNotification(UserProgress $progress): void
- updateCertificateStatus(UserProgress $progress): void

**Do**:
- Generate certificates for completed courses
- Validate course completion requirements
- Create PDF certificates with proper templates
- Send certificate notifications to users
- Track certificate generation status

**Don't**:
- Don't generate certificates without validation
- Don't skip certificate template validation
- Don't ignore certificate generation failures
- Don't send duplicate certificates

---

## 2030b/app/Console/Commands/ProcessVideoConversions.php

**Purpose**: Video processing command for converting uploaded videos, generating multiple formats, and creating thumbnails.

**Dependencies**:
```php
use Illuminate\Console\Command;
use App\Services\Media\VideoConversionService;
use App\Models\CourseUpload;
use FFMpeg\FFMpeg;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
```

**Methods**:
- handle(): int
- processConversionQueue(): void
- convertVideo(CourseUpload $upload): void
- generateVideoFormats(Media $media): void
- createVideoThumbnails(Media $media): void
- updateConversionProgress(CourseUpload $upload, int $progress): void

**Do**:
- Process video conversions in background
- Generate multiple video quality formats
- Create video thumbnails and previews
- Update conversion progress status
- Handle conversion failures with retries

**Don't**:
- Don't process videos without size validation
- Don't ignore video format compatibility
- Don't skip video quality optimization
- Don't forget to clean up conversion temp files

---

## 2030b/app/Console/Commands/SendBulkNotifications.php

**Purpose**: Bulk notification command for sending scheduled notifications, newsletters, and system announcements.

**Dependencies**:
```php
use Illuminate\Console\Command;
use App\Services\Notification\BulkNotificationService;
use App\Models\User;
use Illuminate\Support\Facades\Mail;
use Illuminate\Support\Facades\Queue;
```

**Methods**:
- handle(): int
- processScheduledNotifications(): void
- sendUserNotifications(User $user, array $notifications): void
- validateNotificationPreferences(User $user): array
- queueNotificationBatch(array $users, string $template): void
- trackNotificationDelivery(string $notificationId): void

**Do**:
- Respect user notification preferences
- Queue notifications for efficient processing
- Track notification delivery status
- Handle notification failures gracefully
- Implement rate limiting for bulk sends

**Don't**:
- Don't send notifications without user consent
- Don't ignore unsubscribe requests
- Don't send to invalid email addresses
- Don't skip notification delivery tracking

---

## 2030b/app/Console/Commands/BackupDatabase.php

**Purpose**: Database backup command for creating scheduled database exports and managing backup storage.

**Dependencies**:
```php
use Illuminate\Console\Command;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Storage;
use Spatie\DbDumper\Databases\MySql;
use Spatie\DbDumper\Databases\PostgreSql;
```

**Methods**:
- handle(): int
- createDatabaseDump(): void
- compressBackupFile(string $filename): void
- uploadBackupToCloud(string $filename): void
- verifyBackupIntegrity(string $filename): bool
- cleanupLocalBackups(): void

**Do**:
- Create compressed database dumps
- Upload backups to cloud storage
- Verify backup file integrity
- Implement backup rotation policy
- Encrypt sensitive database backups

**Don't**:
- Don't store backups only locally
- Don't include unnecessary data in backups
- Don't skip backup verification
- Don't ignore backup upload failures

---

## 2030b/app/Console/Commands/OptimizeImages.php

**Purpose**: Image optimization command for compressing uploaded images, generating thumbnails, and managing image assets.

**Dependencies**:
```php
use Illuminate\Console\Command;
use Spatie\ImageOptimizer\OptimizerChainFactory;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Intervention\Image\ImageManager;
use Illuminate\Support\Facades\Storage;
```

**Methods**:
- handle(): int
- optimizeUnprocessedImages(): void
- optimizeImage(Media $media): void
- generateImageThumbnails(Media $media): void
- updateImageMetadata(Media $media): void
- validateImageProcessing(Media $media): bool

**Do**:
- Optimize images for web performance
- Generate multiple thumbnail sizes
- Update image metadata after optimization
- Validate image processing results
- Handle optimization failures gracefully

**Don't**:
- Don't over-compress images losing quality
- Don't skip image format validation
- Don't ignore optimization failures
- Don't forget to update media metadata

---

## 2030b/app/Console/Commands/ProcessLargeCourseUpload.php

**Purpose**: Large file upload processing command for handling course files that require chunked processing and special handling.

**Dependencies**:
```php
use Illuminate\Console\Command;
use App\Services\Course\LargeUploadService;
use App\Models\CourseUpload;
use Illuminate\Support\Facades\Storage;
use App\Enums\UploadStatus;
```

**Methods**:
- handle(): int
- processLargeUploads(): void
- assembleChunkedUpload(CourseUpload $upload): void
- validateUploadIntegrity(CourseUpload $upload): bool
- moveToFinalLocation(CourseUpload $upload): void
- updateUploadCompletion(CourseUpload $upload): void

**Do**:
- Process chunked file uploads
- Validate file integrity after assembly
- Move files to permanent storage
- Update upload completion status
- Handle large file processing errors

**Don't**:
- Don't process files without integrity checks
- Don't ignore chunk assembly failures
- Don't skip virus scanning for large files
- Don't forget to clean up temporary chunks

---

## 2030b/app/Console/Commands/GenerateCourseCertificate.php

**Purpose**: Individual certificate generation command for creating single course certificates on demand.

**Dependencies**:
```php
use Illuminate\Console\Command;
use App\Services\Course\CertificateService;
use App\Models\User;
use App\Models\DocuCourse;
use App\Models\UserProgress;
use Barryvdh\DomPDF\Facade\Pdf;
```

**Methods**:
- handle(): int
- generateCertificate(int $userId, int $courseId): void
- validateCertificateRequest(User $user, DocuCourse $course): bool
- createCertificateData(User $user, DocuCourse $course): array
- generateCertificatePDF(array $data): string
- saveCertificateRecord(User $user, DocuCourse $course, string $path): void

**Do**:
- Validate certificate generation requests
- Create personalized certificate data
- Generate PDF with proper formatting
- Save certificate records for tracking
- Send certificate to user upon generation

**Don't**:
- Don't generate certificates without validation
- Don't skip course completion verification
- Don't ignore certificate template errors
- Don't forget to track certificate generation
