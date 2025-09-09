# DOC_JOBS.md

Jobs handle time-consuming operations asynchronously using Laravel's queue system. They ensure application responsiveness while processing heavy tasks in the background.

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

**Methods**
- handle()
- extractContent()
- generateMetadata()
- createEpisodes()
- updateProgress()
- failed()

**Do**
✅ Process files in chunks
✅ Track processing progress
✅ Handle large file uploads
✅ Generate thumbnails
✅ Update course metadata

**Don't**
❌ Don't process files without validation
❌ Don't ignore file size limits
❌ Don't skip virus scanning
❌ Don't create invalid episodes

---

## app/Jobs/CalculateUserPoints.php

**Purpose**
→ Recalculates user points based on activities, applies retroactive changes, handles point adjustments, and triggers level evaluations with audit trails.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use App\Services\User\LevelService;
use App\Models\User;
use App\Models\User\UserProgress;
use App\Enums\ActivityType;
```

**Methods**
- handle()
- calculatePoints()
- applyAdjustments()
- checkLevelChanges()
- auditChanges()
- retryUntil()

**Do**
✅ Use atomic point calculations
✅ Create audit trails
✅ Handle retroactive changes
✅ Trigger level updates
✅ Support batch processing

**Don't**
❌ Don't create point inconsistencies
❌ Don't ignore level thresholds
❌ Don't skip audit logging
❌ Don't process invalid activities

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
```

**Methods**
- handle()
- personalizeContent()
- recommendCourses()
- getEmailTemplate()
- trackDelivery()

**Do**
✅ Personalize email content
✅ Include course recommendations
✅ Support multiple languages
✅ Track email delivery
✅ Handle unsubscribe requests

**Don't**
❌ Don't send to unverified emails
❌ Don't ignore language preferences
❌ Don't spam new users
❌ Don't skip unsubscribe links

---

## app/Jobs/ProcessPayment.php

**Purpose**
→ Processes payment transactions asynchronously, handles payment provider communication, manages retries for failed payments, and updates order status.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use App\Services\Payment\PaymentService;
use App\Models\Payment\Transaction;
use App\Enums\PaymentStatus;
use App\Enums\PaymentProvider;
```

**Methods**
- handle()
- processTransaction()
- handleRetries()
- updateStatus()
- notifyUser()
- failed()

**Do**
✅ Handle payment provider APIs
✅ Implement retry logic
✅ Update transaction status
✅ Send confirmation emails
✅ Log payment attempts

**Don't**
❌ Don't store payment credentials
❌ Don't ignore payment failures
❌ Don't skip PCI compliance
❌ Don't process duplicate payments

---

## app/Jobs/GenerateInvoice.php

**Purpose**
→ Generates PDF invoices for completed transactions, includes tax calculations, supports multiple currencies, and sends invoices via email with proper formatting.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use App\Services\Payment\InvoiceService;
use App\Models\Payment\Invoice;
use Barryvdh\DomPDF\Facade\Pdf;
use App\Services\System\LocalizationService;
```

**Methods**
- handle()
- generatePDF()
- calculateTaxes()
- formatCurrency()
- sendInvoice()
- archiveInvoice()

**Do**
✅ Generate professional PDFs
✅ Include all tax information
✅ Support multiple currencies
✅ Email invoices automatically
✅ Archive for compliance

**Don't**
❌ Don't generate invalid invoices
❌ Don't ignore tax requirements
❌ Don't skip currency conversion
❌ Don't delete invoice records

---

## app/Jobs/TrackAffiliateConversion.php

**Purpose**
→ Tracks affiliate conversions, calculates commissions, updates affiliate statistics, creates payout records, and handles multi-tier commission structures.

**Dependencies**
```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use App\Services\Affiliate\TrackingService;
use App\Models\Affiliate\Conversion;
use App\Models\Affiliate\Commission;
use App\Enums\ConversionType;
```

**Methods**
- handle()
- trackConversion()
- calculateCommissions()
- createPayouts()
- updateStats()
- detectFraud()

**Do**
✅ Track accurate conversions
✅ Calculate fair commissions
✅ Detect fraudulent activity
✅ Support multi-tier structures
✅ Update affiliate dashboards

**Don't**
❌ Don't track invalid conversions
❌ Don't ignore fraud detection
❌ Don't pay duplicate commissions
❌ Don't skip validation

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

**Methods**
- handle()
- createBackup()
- compressFiles()
- uploadToCloud()
- cleanOldBackups()
- verifyBackup()

**Do**
✅ Create regular automated backups
✅ Compress backup files
✅ Store in multiple locations
✅ Verify backup integrity
✅ Maintain retention policies

**Don't**
❌ Don't backup sensitive data unencrypted
❌ Don't ignore backup failures
❌ Don't keep unlimited backups
❌ Don't skip verification

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

**Methods**
- handle()
- optimizeImage()
- generateSizes()
- createWebP()
- updateMetadata()
- cleanTempFiles()

**Do**
✅ Optimize images automatically
✅ Generate multiple sizes
✅ Create modern formats
✅ Update file metadata
✅ Clean temporary files

**Don't**
❌ Don't over-compress images
❌ Don't ignore original quality
❌ Don't skip format conversion
❌ Don't leave temp files

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

**Methods**
- handle()
- processChunks()
- trackProgress()
- mergeFiles()
- createCourse()
- rollbackOnFailure()

**Do**
✅ Process files in chunks
✅ Track upload progress
✅ Handle interruptions gracefully
✅ Provide rollback capability
✅ Clean up temporary files

**Don't**
❌ Don't process entire file at once
❌ Don't ignore progress tracking
❌ Don't skip error handling
❌ Don't leave partial uploads

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

**Methods**
- handle()
- generateCertificate()
- personalizeContent()
- storeCertificate()
- sendNotification()
- validateCompletion()

**Do**
✅ Validate course completion
✅ Generate professional certificates
✅ Personalize certificate content
✅ Store securely
✅ Send completion notifications

**Don't**
❌ Don't generate without validation
❌ Don't create duplicate certificates
❌ Don't ignore personalization
❌ Don't skip security measures

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
❌ Don't keep unlimited backups