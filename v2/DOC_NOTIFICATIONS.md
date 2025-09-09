# DOC_NOTIFICATIONS.md

Notifications handle user communication through multiple channels (email, SMS, push) using Laravel's notification system with queue support and personalization.

## app/Notifications/WelcomeNotification.php

**Purpose**
→ Sends personalized welcome notification to new users with onboarding information, course recommendations, and platform introduction in their preferred language.

**Dependencies**
```php
use Illuminate\Notifications\Notification;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Messages\DatabaseMessage;
use App\Services\Course\SearchService;
use App\Services\System\LocalizationService;
```

**Methods**
- via()
- toMail()
- toDatabase()
- toArray()
- shouldSend()

**Do**
✅ Personalize welcome content
✅ Include course recommendations
✅ Support multiple languages
✅ Provide onboarding guidance
✅ Include platform benefits

**Don't**
❌ Don't send to unverified emails
❌ Don't ignore language preferences
❌ Don't include sensitive data
❌ Don't overwhelm new users

---

## app/Notifications/CoursePublishedNotification.php

**Purpose**
→ Notifies interested users when new courses are published, includes course details, author information, and enrollment links with personalized recommendations.

**Dependencies**
```php
use Illuminate\Notifications\Notification;
use Illuminate\Notifications\Messages\MailMessage;
use App\Models\Course\DocuCourse;
use App\Models\User;
use App\Services\User\ProfileService;
```

**Methods**
- via()
- toMail()
- toBroadcast()
- shouldSend()
- getRecommendations()

**Do**
✅ Target interested users only
✅ Include course previews
✅ Provide enrollment links
✅ Personalize recommendations
✅ Support real-time updates

**Don't**
❌ Don't spam all users
❌ Don't ignore user preferences
❌ Don't send duplicate notifications
❌ Don't include incomplete courses

---

## app/Notifications/PaymentReceiptNotification.php

**Purpose**
→ Sends payment receipts and invoices to users after successful transactions, includes transaction details, tax information, and course access instructions.

**Dependencies**
```php
use Illuminate\Notifications\Notification;
use Illuminate\Notifications\Messages\MailMessage;
use App\Models\Payment\Transaction;
use App\Models\Payment\Invoice;
use App\Services\Payment\InvoiceService;
```

**Methods**
- via()
- toMail()
- attachInvoice()
- formatCurrency()
- includeAccessInfo()

**Do**
✅ Include complete transaction details
✅ Attach PDF invoices
✅ Format currency properly
✅ Provide access instructions
✅ Include support contact

**Don't**
❌ Don't include payment credentials
❌ Don't ignore tax information
❌ Don't send without verification
❌ Don't skip legal compliance

---

## app/Notifications/CommissionEarnedNotification.php

**Purpose**
→ Notifies affiliates when they earn commissions from referrals, includes commission details, conversion information, and payout schedule.

**Dependencies**
```php
use Illuminate\Notifications\Notification;
use Illuminate\Notifications\Messages\MailMessage;
use App\Models\Affiliate\Commission;
use App\Models\Affiliate\Conversion;
use App\Services\Affiliate\CommissionService;
```

**Methods**
- via()
- toMail()
- calculateTotal()
- getPayoutDate()
- includeConversionDetails()

**Do**
✅ Include commission breakdown
✅ Show conversion details
✅ Provide payout information
✅ Include performance metrics
✅ Support multiple currencies

**Don't**
❌ Don't reveal customer information
❌ Don't ignore currency conversion
❌ Don't send premature notifications
❌ Don't skip commission validation

---

## app/Notifications/LevelUpNotification.php

**Purpose**
→ Congratulates users on level advancement, explains new benefits, shows progress achievements, and encourages continued engagement with rewards.

**Dependencies**
```php
use Illuminate\Notifications\Notification;
use Illuminate\Notifications\Messages\MailMessage;
use App\Models\User;
use App\Models\User\UserLevel;
use App\Services\User\LevelService;
```

**Methods**
- via()
- toMail()
- toBroadcast()
- getBenefits()
- getAchievements()

**Do**
✅ Celebrate user achievements
✅ Explain new benefits clearly
✅ Show progress visually
✅ Encourage continued learning
✅ Include next level requirements

**Don't**
❌ Don't minimize achievements
❌ Don't hide benefit details
❌ Don't ignore progress context
❌ Don't skip motivational content

---

## app/Notifications/TwoFactorCodeNotification.php

**Purpose**
→ Sends two-factor authentication codes to users via email or SMS, includes security context, expiration time, and fraud prevention measures.

**Dependencies**
```php
use Illuminate\Notifications\Notification;
use Illuminate\Notifications\Messages\MailMessage;
use PragmaRX\Google2FA\Google2FA;
use App\Services\Security\AuthenticationService;
```

**Methods**
- via()
- toMail()
- toSms()
- generateCode()
- isSecureContext()

**Do**
✅ Send codes promptly
✅ Include security context
✅ Set appropriate expiration
✅ Detect suspicious activity
✅ Support multiple channels

**Don't**
❌ Don't delay code delivery
❌ Don't ignore security context
❌ Don't use predictable codes
❌ Don't skip fraud detection

---

## app/Notifications/BackupSuccessNotification.php

**Purpose**
→ Notifies administrators of successful backup completion, includes backup details, file sizes, verification status, and storage locations.

**Dependencies**
```php
use Illuminate\Notifications\Notification;
use Illuminate\Notifications\Messages\MailMessage;
use Spatie\Backup\BackupDestination\BackupDestination;
use App\Services\File\BackupService;
```

**Methods**
- via()
- toMail()
- getBackupDetails()
- formatFileSize()
- includeVerification()

**Do**
✅ Include backup statistics
✅ Show verification status
✅ List storage locations
✅ Include timing information
✅ Provide restoration guidance

**Don't**
❌ Don't ignore backup failures
❌ Don't skip verification details
❌ Don't overwhelm with data
❌ Don't send false positives

---

## app/Notifications/SystemAlertNotification.php

**Purpose**
→ Sends critical system alerts to administrators for issues like service outages, security breaches, performance problems, and maintenance requirements.

**Dependencies**
```php
use Illuminate\Notifications\Notification;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Messages\SlackMessage;
use App\Services\Health\SystemMonitorService;
```

**Methods**
- via()
- toMail()
- toSlack()
- getAlertLevel()
- includeResolution()

**Do**
✅ Send immediately for critical issues
✅ Include severity levels
✅ Provide resolution steps
✅ Use multiple channels
✅ Include system context

**Don't**
❌ Don't delay critical alerts
❌ Don't ignore severity levels
❌ Don't send false alarms
❌ Don't skip resolution guidance

---

## app/Notifications/NewUserRegisteredNotification.php

**Purpose**
→ Notifies administrators of new user registrations with user details, registration source, affiliate information, and initial activity tracking.

**Dependencies**
```php
use Illuminate\Notifications\Notification;
use Illuminate\Notifications\Messages\MailMessage;
use App\Models\User;
use App\Services\System\AnalyticsService;
```

**Methods**
- via()
- toMail()
- getUserDetails()
- getRegistrationSource()
- includeAffiliateInfo()

**Do**
✅ Include registration analytics
✅ Show referral source
✅ Track initial activity
✅ Identify suspicious patterns
✅ Support user segmentation

**Don't**
❌ Don't expose sensitive data
❌ Don't ignore privacy settings
❌ Don't overwhelm administrators
❌ Don't skip fraud detection

---

## app/Notifications/AffiliatePayoutNotification.php

**Purpose**
→ Notifies affiliates of commission payouts with payout details, payment method information, tax documentation, and performance summaries.

**Dependencies**
```php
use Illuminate\Notifications\Notification;
use Illuminate\Notifications\Messages\MailMessage;
use App\Models\Affiliate\Commission;
use App\Services\Affiliate\PayoutService;
use App\Services\Compliance\TaxComplianceService;
```

**Methods**
- via()
- toMail()
- getPayoutDetails()
- includeTaxInfo()
- getPerformanceSummary()

**Do**
✅ Include complete payout details
✅ Provide tax documentation
✅ Show performance metrics
✅ Include payment method info
✅ Support multiple currencies

**Don't**
❌ Don't ignore tax requirements
❌ Don't skip payment verification
❌ Don't hide payout calculations
❌ Don't ignore compliance rules