# DOC_NOTIFICATIONS.md

## Overview
The Notifications component provides a comprehensive multi-channel notification system for the Laravel docu-course platform, integrating seamlessly with events, listeners, services, and user preferences. This component ensures consistent notification delivery across email, SMS, database, and broadcast channels while maintaining full integration with the project's event-driven architecture. Updated.

---

## Notification Architecture Integration

### Channel Integration
All notification channels integrate with the channel contracts defined in DOC_CONTRACTS_INTERFACES.md:

- **EmailChannelInterface**: Handles email delivery with template support, bounce handling, and delivery tracking
- **SmsChannelInterface**: Manages SMS delivery with phone number validation and delivery confirmation
- **NotificationChannelInterface**: Base channel contract for consistent notification delivery across all channels
- **DatabaseNotifications**: In-app notifications stored in the database for user access

### Event-Driven Integration
Notifications are triggered by domain events documented in DOC_EVENTS.md:

- **UserRegistered Event** → WelcomeNotification, NewUserRegisteredNotification
- **PaymentProcessed Event** → PaymentReceiptNotification
- **CoursePublished Event** → CoursePublishedNotification
- **UserLevelChanged Event** → LevelUpNotification
- **AffiliateCommissionEarned Event** → CommissionEarnedNotification, AffiliatePayoutNotification
- **System Events** → BackupSuccessNotification, SystemAlertNotification

### Service Layer Integration
All notifications integrate with the NotificationService documented in DOC_SERVICES.md:

```php
NotificationService::sendNotification(int $userId, string $type, array $data, array $channels)
NotificationService::sendBulkNotification(array $userIds, string $type, array $data)
NotificationService::updateNotificationPreferences(int $userId, array $preferences)
```

### Listener Integration
Notification listeners from DOC_LISTENERS.md handle notification dispatching:

- **SendEmailVerificationNotification**: Handles email verification with EmailVerificationNotification
- **SendNewUserNotification**: Dispatches NewUserRegisteredNotification to administrators
- **SendAffiliateNotification**: Manages AffiliateNotification for commission updates

---

## Notification Classes

### User Lifecycle Notifications

#### app/Notifications/WelcomeNotification.php
**Purpose**: Sends personalized welcome messages to new users with course recommendations and platform introduction.

**Channels**: Email (toMail), Database (toDatabase)

**Service Integration**:
- Course\SearchService: Provides personalized course recommendations
- System\LocalizationService: Ensures multi-language support

**Event Integration**: Triggered by UserRegistered event

**Methods**:
- via(): array - Determines notification channels based on user preferences
- toMail(): MailMessage - Creates welcome email with course recommendations
- toDatabase(): array - Stores in-app welcome notification
- shouldSend(): bool - Respects user notification preferences
- getRecommendations(): array - Fetches recommended courses

**Localization Support**: Uses Localizable trait for multi-language template rendering

#### app/Notifications/LevelUpNotification.php
**Purpose**: Congratulates users on reaching new levels, emphasizes achievements and unlocks benefits.

**Channels**: Email (toMail), Broadcast (toBroadcast)

**Service Integration**:
- User\LevelService: Provides level benefits and achievement data

**Event Integration**: Triggered by UserLevelChanged event

**Methods**:
- via(): array - Uses email and real-time broadcast channels
- toMail(): MailMessage - Creates achievement celebration email
- toBroadcast(): BroadcastMessage - Real-time level up notification
- getBenefits(): array - Retrieves unlocked benefits
- getAchievements(): array - Lists earned achievements

**Real-Time Integration**: Broadcasts to user-specific channels for immediate UI updates

#### app/Notifications/TwoFactorCodeNotification.php
**Purpose**: Delivers 2FA codes via email or SMS with security context and fraud detection.

**Channels**: Email (toMail), SMS (toSms)

**Service Integration**:
- Security\AuthenticationService: Manages 2FA code generation and validation
- Google2FA integration for secure code generation

**Methods**:
- via(): array - Selects channel based on user preference and security context
- toMail(): MailMessage - Email delivery with security warnings
- toSms(): SmsMessage - SMS delivery for time-sensitive codes
- generateCode(): string - Secure code generation
- isSecureContext(): bool - Validates request security

**Security Features**: Fraud detection, rate limiting, secure code expiration

### Content & Course Notifications

#### app/Notifications/CoursePublishedNotification.php
**Purpose**: Notifies interested users about new course publications with personalized recommendations.

**Channels**: Email (toMail), Broadcast (toBroadcast)

**Service Integration**:
- User\ProfileService: Identifies users interested in course topics

**Event Integration**: Triggered by CoursePublished event

**Methods**:
- via(): array - Targets only interested users to prevent spam
- toMail(): MailMessage - Course announcement with details
- toBroadcast(): BroadcastMessage - Real-time course availability updates
- shouldSend(): bool - Filters based on user interests and preferences
- getRecommendations(): array - Related course suggestions

**Targeting Strategy**: Uses user interests and course categories for precise targeting

### Payment & Transaction Notifications

#### app/Notifications/PaymentReceiptNotification.php
**Purpose**: Sends payment receipts and invoices with complete transaction details.

**Channels**: Email (toMail)

**Service Integration**:
- Payment\InvoiceService: Generates PDF invoices and tax information

**Event Integration**: Triggered by PaymentProcessed event

**Methods**:
- via(): array - Email delivery for record-keeping
- toMail(): MailMessage - Receipt with PDF attachment
- attachInvoice(): Attachment - PDF invoice generation
- formatCurrency(): string - Proper currency formatting
- includeAccessInfo(): array - Course access details

**Compliance Features**: Tax information, legal requirements, audit trail

### Affiliate System Notifications

#### app/Notifications/CommissionEarnedNotification.php
**Purpose**: Notifies affiliates of earned commissions with detailed breakdown and payout information.

**Channels**: Email (toMail)

**Service Integration**:
- Affiliate\CommissionService: Provides commission calculations and validation

**Event Integration**: Triggered by AffiliateCommissionEarned event

**Methods**:
- via(): array - Email delivery for financial records
- toMail(): MailMessage - Commission details and breakdown
- calculateTotal(): Money - Accurate commission calculations
- getPayoutDate(): Carbon - Next payout schedule
- includeConversionDetails(): array - Source conversion information

#### app/Notifications/AffiliatePayoutNotification.php
**Purpose**: Notifies affiliates of completed payouts with tax information and performance metrics.

**Channels**: Email (toMail)

**Service Integration**:
- Affiliate\PayoutService: Manages payout processing
- Compliance\TaxComplianceService: Provides tax documentation

**Methods**:
- via(): array - Email for financial documentation
- toMail(): MailMessage - Payout confirmation with tax info
- getPayoutDetails(): array - Complete payout breakdown
- includeTaxInfo(): array - Tax compliance documentation
- getPerformanceSummary(): array - Affiliate performance metrics

### Administrative Notifications

#### app/Notifications/NewUserRegisteredNotification.php
**Purpose**: Alerts administrators about new user registrations with security and analytics data.

**Channels**: Email (toMail)

**Service Integration**:
- System\AnalyticsService: Provides registration analytics and security checks

**Event Integration**: Triggered by UserRegistered event

**Methods**:
- via(): array - Email to admin team
- toMail(): MailMessage - User details and registration context
- getUserDetails(): array - Safe user information
- getRegistrationSource(): string - Registration channel tracking
- includeAffiliateInfo(): array - Referral and affiliate data

**Security Features**: Suspicious pattern detection, registration source tracking

### System & Infrastructure Notifications

#### app/Notifications/BackupSuccessNotification.php
**Purpose**: Reports successful backup operations to administrators with verification details.

**Channels**: Email (toMail)

**Service Integration**:
- File\BackupService: Spatie Backup integration for verification

**Methods**:
- via(): array - Email for infrastructure monitoring
- toMail(): MailMessage - Backup success confirmation
- getBackupDetails(): array - File sizes, locations, timing
- formatFileSize(): string - Human-readable file sizes
- includeVerification(): array - Backup integrity checks

#### app/Notifications/SystemAlertNotification.php
**Purpose**: Delivers critical system alerts for service outages, security breaches, and performance issues.

**Channels**: Email (toMail), Slack (toSlack)

**Service Integration**:
- Health\SystemMonitorService: Provides system status and alert severity

**Methods**:
- via(): array - Multi-channel for critical alerts
- toMail(): MailMessage - Detailed alert information
- toSlack(): SlackMessage - Real-time team notifications
- getAlertLevel(): string - Severity classification
- includeResolution(): array - Resolution steps and context

---

## Notification Preferences & User Control

### Preference Integration
All notifications respect user preferences stored in user_profiles.notification_settings JSON field:

```php
// User notification preferences structure
{
    "email": {
        "welcome": true,
        "course_published": false,
        "payment_receipt": true,
        "level_up": true,
        "commission_earned": true
    },
    "sms": {
        "two_factor": true,
        "security_alerts": true
    },
    "database": {
        "welcome": true,
        "level_up": true,
        "course_published": true
    },
    "broadcast": {
        "level_up": true,
        "course_published": false
    }
}
```

### Localization Integration
All notifications support multi-language delivery using the Localizable trait:

- **Multi-language templates**: Dynamic template selection based on user preferred_language
- **RTL support**: Proper text direction for Arabic, Hebrew, and other RTL languages
- **Cultural adaptation**: Date formats, currency symbols, and cultural contexts

### Queue Integration
Notifications are queued for performance using the ShouldQueue interface:

- **Bulk notifications**: Automatically queued to prevent blocking
- **Immediate notifications**: 2FA and security alerts sent synchronously
- **Scheduled notifications**: Support for delayed delivery
- **Failed job handling**: Retry logic and failure notifications

---

## Best Practices & Implementation Standards

### Security & Privacy
- **Consent validation**: All notifications respect user opt-in preferences
- **Data minimization**: Only necessary data included in notification payload
- **Secure delivery**: Encrypted channels for sensitive information
- **Audit logging**: All notification activities logged for compliance

### Performance Optimization
- **Channel selection**: Efficient channel determination based on user preferences
- **Template caching**: Notification templates cached for performance
- **Bulk processing**: Optimized bulk notification delivery
- **Queue management**: Proper queue configuration for notification types

### Error Handling
- **Delivery failures**: Graceful handling of undeliverable notifications
- **Retry logic**: Exponential backoff for temporary failures
- **Fallback channels**: Alternative delivery methods for critical notifications
- **Monitoring**: Delivery rate tracking and failure alerting

### Integration Compliance
- **Event consistency**: All notifications properly integrated with domain events
- **Service dependency**: Proper service injection and interface compliance
- **Contract adherence**: Full compliance with notification channel interfaces
- **Testing coverage**: Comprehensive testing as per TESTING_GUIDE.md requirements
