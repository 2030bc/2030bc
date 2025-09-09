# app/Enums/UserStatus.php

## Purpose
→ Represents user account status states throughout the platform lifecycle
→ Used in user table status field and authentication middleware
→ Controls user access permissions and account state management

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case ACTIVE = 1;      // Active user account
case INACTIVE = 2;    // Temporarily inactive account
case BANNED = 3;      // Permanently banned account
case PENDING = 4;     // Pending email verification
```

## Do
✅ Use backed integers for database efficiency (TINYINT)
✅ Check status in authentication middleware
✅ Include status validation in user creation
✅ Log status changes for audit trail
✅ Use enum cases instead of magic numbers

## Don't
❌ Store status as strings in database
❌ Hardcode status values in controllers
❌ Allow direct status changes without validation
❌ Forget to handle all status cases in switch statements
❌ Use nullable status fields

# app/Enums/UserLevel.php

## Purpose
→ Defines 14 user progression levels (1-14) for platform access control
→ Used in content access validation and commission calculations
→ Determines user benefits and feature availability

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case LEVEL_1 = 1;     // Beginner (0-100 points)
case LEVEL_2 = 2;     // Beginner (101-250 points)
case LEVEL_3 = 3;     // Beginner (251-500 points)
case LEVEL_4 = 4;     // Intermediate (501-1000 points)
case LEVEL_5 = 5;     // Intermediate (1001-2000 points)
case LEVEL_6 = 6;     // Intermediate (2001-4000 points)
case LEVEL_7 = 7;     // Advanced (4001-7000 points)
case LEVEL_8 = 8;     // Advanced (7001-10000 points)
case LEVEL_9 = 9;     // Advanced (10001-15000 points)
case LEVEL_10 = 10;   // Expert (15001-25000 points)
case LEVEL_11 = 11;   // VIP (25001-40000 points)
case LEVEL_12 = 12;   // VIP (40001-60000 points)
case LEVEL_13 = 13;   // Elite (60001-100000 points)
case LEVEL_14 = 14;   // Master (100000+ points)
```

## Do
✅ Use for content access control middleware
✅ Calculate level from user points automatically
✅ Store point thresholds in user_levels table
✅ Cache level calculations for performance
✅ Update level on point changes via events

## Don't
❌ Hardcode point thresholds in enum
❌ Allow manual level assignment without points
❌ Skip level validation in access control
❌ Use levels for business logic without points check
❌ Forget to trigger level change events

# app/Enums/TextDirection.php

## Purpose
→ Controls text direction for multi-language support
→ Used in UI rendering and CSS class assignment
→ Supports RTL languages like Arabic

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case LTR = 1;         // Left-to-right (English, French, etc.)
case RTL = 2;         // Right-to-left (Arabic, Hebrew, etc.)
```

## Do
✅ Set on user preferences and language detection
✅ Use in Blade templates for CSS classes
✅ Store in session for performance
✅ Apply to form layouts and navigation
✅ Test with actual RTL content

## Don't
❌ Hardcode direction in templates
❌ Ignore direction in form validation
❌ Use string comparisons for direction checks
❌ Forget to apply to dynamic content
❌ Skip RTL testing with real languages

# app/Enums/Gender.php

## Purpose
→ Represents user gender identity options
→ Used in user profile and demographic reporting
→ Supports inclusive gender options

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case MALE = 1;                    // Male gender
case FEMALE = 2;                  // Female gender
case OTHER = 3;                   // Other gender identity
case PREFER_NOT_TO_SAY = 4;       // Privacy preference
```

## Do
✅ Make gender optional in user profiles
✅ Use for demographic analytics only
✅ Respect privacy preferences
✅ Include in user profile forms
✅ Store securely and privately

## Don't
❌ Make gender required for registration
❌ Use for business logic decisions
❌ Expose gender in public APIs
❌ Assume gender from names
❌ Use for content filtering

# app/Enums/CourseStatus.php

## Purpose
→ Manages course publication lifecycle states
→ Controls course visibility and access
→ Used in content management and publishing workflow

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case DRAFT = 1;       // Course in development
case PUBLISHED = 2;   // Live and accessible
case ARCHIVED = 3;    // Hidden but preserved
case PRIVATE = 4;     // Restricted access only
```

## Do
✅ Use in course listing queries
✅ Validate status changes in service layer
✅ Log status transitions for audit
✅ Apply access control based on status
✅ Use in admin course management

## Don't
❌ Allow direct status manipulation by users
❌ Skip validation on status changes
❌ Expose draft courses in public APIs
❌ Delete courses instead of archiving
❌ Ignore status in search indexing

# app/Enums/ContentAccessLevel.php

## Purpose
→ Defines access requirements for course content
→ Used in authorization middleware and content gates
→ Controls monetization and user level restrictions

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case PUBLIC = 1;              // Open access, no login required
case AUTHENTICATED = 2;       // Requires user account
case LEVEL_RESTRICTED = 3;    // Requires specific user level
case PREMIUM = 4;             // Requires payment
case VIP = 5;                 // Requires VIP membership (Level 11+)
```

## Do
✅ Check access level in middleware
✅ Use with user level validation
✅ Apply to episodes and course parts
✅ Cache access checks for performance
✅ Log access denials for analytics

## Don't
❌ Bypass access checks in development
❌ Use without proper authentication
❌ Hardcode access levels in templates
❌ Skip level validation for restricted content
❌ Allow access escalation through URL manipulation

# app/Enums/DifficultyLevel.php

## Purpose
→ Categorizes course complexity and skill requirements
→ Used in course filtering and recommendations
→ Helps users select appropriate content

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case BEGINNER = 1;        // Entry level, no prerequisites
case INTERMEDIATE = 2;    // Some prior knowledge required
case ADVANCED = 3;        // Significant experience needed
case EXPERT = 4;          // Professional level content
```

## Do
✅ Use in course search filters
✅ Display prominently in course listings
✅ Consider in recommendation algorithms
✅ Validate against user skill assessments
✅ Use for content organization

## Don't
❌ Change difficulty without content review
❌ Use for access control (use ContentAccessLevel)
❌ Ignore in course progression paths
❌ Auto-assign without instructor input
❌ Use as substitute for prerequisites

# app/Enums/PaymentStatus.php

## Purpose
→ Tracks payment transaction states through processing lifecycle
→ Used in payment services and webhook handlers
→ Critical for financial reconciliation and user access

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case PENDING = 1;         // Payment initiated, awaiting confirmation
case COMPLETED = 2;       // Payment successfully processed
case FAILED = 3;          // Payment failed or declined
case REFUNDED = 4;        // Payment reversed/refunded
```

## Do
✅ Update status atomically in transactions
✅ Log all status changes with timestamps
✅ Use in webhook payment processing
✅ Validate status transitions (pending → completed)
✅ Trigger events on status changes

## Don't
❌ Skip status validation in payment flow
❌ Allow invalid status transitions
❌ Process payments without status tracking
❌ Ignore failed payment cleanup
❌ Forget to update user access on completion

# app/Enums/TransactionType.php

## Purpose
→ Categorizes different types of financial transactions
→ Used in transaction logging and financial reporting
→ Supports various business transaction models

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case PURCHASE = 1;        // Course or content purchase
case REFUND = 2;          // Money returned to customer
case WITHDRAWAL = 3;      // Affiliate commission withdrawal
case COMMISSION = 4;      // Affiliate commission earning
case BONUS = 5;           // Platform bonus or reward
```

## Do
✅ Use for financial reporting queries
✅ Apply different processing rules per type
✅ Include in transaction audit logs
✅ Use for tax calculation logic
✅ Filter dashboard analytics by type

## Don't
❌ Mix transaction types in single records
❌ Skip type validation in transaction creation
❌ Use for non-financial activities
❌ Forget type-specific business rules
❌ Ignore type in reconciliation processes

# app/Enums/PaymentProvider.php

## Purpose
→ Identifies payment gateway used for transactions
→ Used in payment service routing and configuration
→ Supports multiple payment integration strategies

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case BINANCE = 1;         // Binance cryptocurrency payments
case STRIPE = 2;          // Stripe credit card payments
case PAYPAL = 3;          // PayPal payments
case LOCAL = 4;           // Local payment gateways
case INTERNAL = 5;        // Internal credit system
```

## Do
✅ Route payments to correct service implementation
✅ Use for provider-specific webhook handling
✅ Apply provider-specific validation rules
✅ Track provider performance metrics
✅ Configure provider settings dynamically

## Don't
❌ Hardcode provider logic in controllers
❌ Skip provider validation in payment flow
❌ Mix provider-specific data formats
❌ Ignore provider-specific error handling
❌ Forget to test each provider integration

# app/Enums/CurrencyType.php

## Purpose
→ Categorizes currency types for proper handling
→ Used in currency service and exchange rate management
→ Supports multiple currency ecosystems

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case FIAT = 1;            // Traditional currencies (USD, EUR, GBP)
case CRYPTO = 2;          // Cryptocurrencies (BTC, ETH, USDT)
case LOCAL = 3;           // Local/regional currencies
case PLATFORM = 4;        // Platform-specific tokens/credits
```

## Do
✅ Use for exchange rate management
✅ Apply type-specific validation rules
✅ Route to appropriate payment providers
✅ Handle precision differences by type
✅ Cache exchange rates by currency type

## Don't
❌ Treat all currency types identically
❌ Skip type validation in conversions
❌ Mix precision levels across types
❌ Ignore type in exchange rate updates
❌ Forget type-specific tax implications

# app/Enums/AffiliateStatus.php

## Purpose
→ Manages affiliate account states and permissions
→ Used in affiliate program access control
→ Controls commission eligibility and payouts

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case PENDING = 1;         // Application under review
case ACTIVE = 2;          // Approved and active affiliate
case SUSPENDED = 3;       // Temporarily suspended account
case TERMINATED = 4;      // Permanently terminated
```

## Do
✅ Check status before commission calculations
✅ Use in affiliate dashboard access control
✅ Log status changes with reasons
✅ Send notifications on status changes
✅ Validate status in referral tracking

## Don't
❌ Process commissions for inactive affiliates
❌ Allow terminated affiliates to create referrals
❌ Skip status checks in payout processing
❌ Change status without proper authorization
❌ Ignore status in affiliate analytics

# app/Enums/ReferralType.php

## Purpose
→ Defines what actions trigger affiliate commissions
→ Used in referral tracking and commission calculation
→ Supports different affiliate program models

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case SIGNUP = 1;          // Commission on user registration
case PURCHASE = 2;        // Commission on course purchase
case BOTH = 3;            // Commission on both actions
```

## Do
✅ Use in commission calculation logic
✅ Configure per affiliate program
✅ Track conversion events by type
✅ Apply different commission rates per type
✅ Validate type in referral code creation

## Don't
❌ Mix referral types in single tracking
❌ Skip type validation in conversions
❌ Apply wrong commission rates
❌ Ignore type in analytics reporting
❌ Change type without affiliate notification

# app/Enums/ConversionType.php

## Purpose
→ Categorizes successful affiliate conversion events
→ Used in conversion tracking and analytics
→ Determines commission eligibility and amounts

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case SIGNUP = 1;          // User registration conversion
case PURCHASE = 2;        // Course purchase conversion
case SUBSCRIPTION = 3;    // Subscription signup conversion
```

## Do
✅ Record conversions with accurate timestamps
✅ Use for affiliate performance analytics
✅ Apply conversion-specific commission rules
✅ Track conversion funnel optimization
✅ Validate conversion authenticity

## Don't
❌ Record duplicate conversions
❌ Skip conversion validation checks
❌ Mix conversion types in calculations
❌ Ignore fraud detection for conversions
❌ Process invalid conversion events

# app/Enums/CommissionStatus.php

## Purpose
→ Tracks commission payment lifecycle states
→ Used in affiliate payout processing
→ Ensures proper financial controls and auditing

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case PENDING = 1;         // Commission earned, awaiting approval
case PAYABLE = 2;         // Approved for payment
case PAID = 3;            // Commission paid out
case CANCELLED = 4;       // Commission cancelled/reversed
```

## Do
✅ Update status through payment processing
✅ Use in affiliate payout calculations
✅ Log status changes with audit trail
✅ Apply minimum payout thresholds
✅ Validate status transitions

## Don't
❌ Skip status validation in payouts
❌ Allow invalid status transitions
❌ Process cancelled commissions
❌ Forget to update affiliate balance
❌ Ignore status in financial reporting

# app/Enums/SocialPlatform.php

## Purpose
→ Identifies social media platforms for sharing and tracking
→ Used in social sharing features and analytics
→ Supports viral marketing and referral attribution

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case FACEBOOK = 1;        // Facebook sharing
case TWITTER = 2;         // Twitter/X sharing
case LINKEDIN = 3;        // LinkedIn sharing
case WHATSAPP = 4;        // WhatsApp sharing
case TELEGRAM = 5;        // Telegram sharing
case EMAIL = 6;           // Email sharing
```

## Do
✅ Use for social sharing URL generation
✅ Track platform-specific analytics
✅ Apply platform-appropriate content formatting
✅ Use in social media integration
✅ Optimize content for each platform

## Don't
❌ Hardcode platform URLs
❌ Skip platform validation in sharing
❌ Use same content format for all platforms
❌ Ignore platform-specific API limits
❌ Forget to track sharing success rates

# app/Enums/AuthProvider.php

## Purpose
→ Identifies authentication method used for user login
→ Used in Laravel Socialite integration
→ Supports multiple authentication strategies

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case LOCAL = 1;           // Email/password authentication
case GOOGLE = 2;          // Google OAuth
case FACEBOOK = 3;        // Facebook OAuth
case GITHUB = 4;          // GitHub OAuth
case TWITTER = 5;         // Twitter OAuth
```

## Do
✅ Use in user registration tracking
✅ Apply provider-specific validation rules
✅ Handle provider-specific user data
✅ Use in authentication analytics
✅ Configure provider settings dynamically

## Don't
❌ Mix authentication providers in single flow
❌ Skip provider validation
❌ Ignore provider-specific security requirements
❌ Hardcode provider configurations
❌ Forget to handle provider failures

# app/Enums/TokenType.php

## Purpose
→ Categorizes Laravel Sanctum token types and purposes
→ Used in API authentication and token management
→ Supports different token access levels

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case API = 1;             // General API access token
case MOBILE = 2;          // Mobile app access token
case WEB = 3;             // Web application token
case ADMIN = 4;           // Administrative access token
```

## Do
✅ Use in token creation and validation
✅ Apply type-specific permissions
✅ Set appropriate token expiration per type
✅ Use in API rate limiting
✅ Log token usage by type

## Don't
❌ Use wrong token type for access level
❌ Skip type validation in API endpoints
❌ Mix token types in authentication
❌ Ignore type in token revocation
❌ Use same permissions for all types

# app/Enums/TwoFactorMethod.php

## Purpose
→ Defines two-factor authentication methods available
→ Used in 2FA setup and verification processes
→ Supports multiple 2FA security options

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case APP = 1;             // Authenticator app (Google Authenticator)
case SMS = 2;             // SMS verification code
case EMAIL = 3;           // Email verification code
case BACKUP_CODES = 4;    // One-time backup codes
```

## Do
✅ Use in 2FA setup and verification
✅ Allow multiple methods per user
✅ Validate method availability
✅ Use backup codes as fallback
✅ Log 2FA usage for security monitoring

## Don't
❌ Rely on single 2FA method only
❌ Skip method validation
❌ Store 2FA secrets insecurely
❌ Ignore 2FA in security policies
❌ Forget backup recovery options

# app/Enums/SessionStatus.php

## Purpose
→ Tracks user session states for security monitoring
→ Used in session management and security features
→ Supports concurrent session control

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case ACTIVE = 1;          // Currently active session
case EXPIRED = 2;         // Session expired naturally
case REVOKED = 3;         // Session manually revoked
case INVALID = 4;         // Invalid/corrupted session
```

## Do
✅ Use in session cleanup processes
✅ Track session security events
✅ Implement concurrent session limits
✅ Log suspicious session activity
✅ Use in user security dashboard

## Don't
❌ Allow invalid sessions to persist
❌ Skip session status validation
❌ Ignore session security monitoring
❌ Forget to cleanup expired sessions
❌ Use sessions without status tracking

# app/Enums/StripeStatus.php

## Purpose
→ Maps Stripe-specific payment statuses
→ Used in Stripe webhook processing
→ Handles Stripe payment intent lifecycle

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case REQUIRES_PAYMENT_METHOD = 1;    // Needs payment method
case REQUIRES_CONFIRMATION = 2;      // Needs user confirmation
case REQUIRES_ACTION = 3;            // Needs additional action
case PROCESSING = 4;                 // Payment processing
case REQUIRES_CAPTURE = 5;           // Needs manual capture
case CANCELED = 6;                   // Payment cancelled
case SUCCEEDED = 7;                  // Payment succeeded
```

## Do
✅ Use in Stripe webhook handlers
✅ Map to internal PaymentStatus enum
✅ Handle status-specific business logic
✅ Log Stripe status transitions
✅ Use for payment UI state management

## Don't
❌ Use Stripe statuses outside payment service
❌ Skip status mapping to internal enums
❌ Ignore status-specific error handling
❌ Mix Stripe statuses with other providers
❌ Forget to handle all possible statuses

# app/Enums/BinanceOrderType.php

## Purpose
→ Defines Binance API order types for crypto payments
→ Used in Binance payment service integration
→ Supports different cryptocurrency trading strategies

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case MARKET = 1;          // Market order (immediate execution)
case LIMIT = 2;           // Limit order (specific price)
case STOP_LOSS = 3;       // Stop loss order
case TAKE_PROFIT = 4;     // Take profit order
```

## Do
✅ Use in Binance API integration
✅ Apply order type specific validation
✅ Handle order type specific responses
✅ Use for crypto payment processing
✅ Log order execution details

## Don't
❌ Use for non-crypto payments
❌ Skip order type validation
❌ Ignore order type specific risks
❌ Mix order types in single transaction
❌ Forget order type specific fees

# app/Enums/WebhookEvent.php

## Purpose
→ Categorizes webhook events from payment providers
→ Used in webhook routing and processing
→ Ensures proper event handling across providers

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case PAYMENT_SUCCEEDED = 1;       // Payment completed successfully
case PAYMENT_FAILED = 2;          // Payment failed
case SUBSCRIPTION_CREATED = 3;    // New subscription
case INVOICE_PAID = 4;            // Invoice payment received
```

## Do
✅ Use in webhook routing logic
✅ Apply event-specific processing
✅ Log webhook events for debugging
✅ Validate webhook signatures by event
✅ Use for automated business processes

## Don't
❌ Process unknown webhook events
❌ Skip webhook signature validation
❌ Ignore webhook event ordering
❌ Process duplicate webhook events
❌ Forget idempotency in webhook handling

# app/Enums/RefundReason.php

## Purpose
→ Categorizes reasons for payment refunds
→ Used in refund processing and analytics
→ Supports refund policy enforcement

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case DUPLICATE = 1;               // Duplicate payment
case FRAUDULENT = 2;              // Fraudulent transaction
case REQUESTED_BY_CUSTOMER = 3;   // Customer requested refund
case SUBSCRIPTION_CANCELED = 4;   // Subscription cancellation
```

## Do
✅ Use in refund processing logic
✅ Apply reason-specific policies
✅ Track refund reasons for analytics
✅ Use in fraud detection systems
✅ Include in financial reporting

## Don't
❌ Process refunds without valid reasons
❌ Skip reason validation
❌ Ignore reason in refund policies
❌ Mix refund reasons in processing
❌ Forget to log refund reasons

# app/Enums/RoleType.php

## Purpose
→ Defines system-wide user roles for Spatie Permission package
→ Used in role-based access control (RBAC)
→ Supports hierarchical permission management

## Dependencies
```php
use BackedEnum;
use Spatie\Permission\Models\Role;
```

## Values
```php
case SUPER_ADMIN = 1;     // Full system access
case ADMIN = 2;           // Administrative access
case INSTRUCTOR = 3;      // Course creation/management
case STUDENT = 4;         // Course consumption
case AFFILIATE = 5;       // Affiliate program access
case GUEST = 6;           // Limited public access
```

## Do
✅ Use with Spatie Permission package
✅ Apply role hierarchy in permissions
✅ Use in middleware and policies
✅ Seed roles during application setup
✅ Cache role checks for performance

## Don't
❌ Hardcode role names in controllers
❌ Skip role validation in sensitive operations
❌ Create roles without proper permissions
❌ Use roles for business logic decisions
❌ Forget to assign default roles to users

# app/Enums/PermissionType.php

## Purpose
→ Categorizes permissions by functional area
→ Used in permission organization and management
→ Supports modular permission architecture

## Dependencies
```php
use BackedEnum;
use Spatie\Permission\Models\Permission;
```

## Values
```php
case COURSE = 1;          // Course-related permissions
case USER = 2;            // User management permissions
case PAYMENT = 3;         // Payment system permissions
case SYSTEM = 4;          // System administration permissions
case AFFILIATE = 5;       // Affiliate program permissions
```

## Do
✅ Group permissions by functional area
✅ Use in permission seeding
✅ Apply in role assignment logic
✅ Use for permission management UI
✅ Organize permissions logically

## Don't
❌ Mix permission types in assignments
❌ Create permissions without categories
❌ Use for direct access control
❌ Ignore type in permission queries
❌ Forget to organize permissions by type

# app/Enums/GuardName.php

## Purpose
→ Defines Laravel authentication guards
→ Used in Spatie Permission guard configuration
→ Supports multiple authentication contexts

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case WEB = 1;             // Web session guard
case API = 2;             // API token guard (Sanctum)
case ADMIN = 3;           // Admin panel guard
```

## Do
✅ Use in guard-specific permission assignments
✅ Configure guards in auth configuration
✅ Apply guard-specific middleware
✅ Use in multi-context applications
✅ Validate guards in permission checks

## Don't
❌ Mix guards in permission assignments
❌ Skip guard validation
❌ Use wrong guard for context
❌ Ignore guard in policy definitions
❌ Forget to configure guard middleware

# app/Enums/MediaCollection.php

## Purpose
→ Defines Spatie MediaLibrary collection names
→ Used in media file organization and retrieval
→ Supports structured media management

## Dependencies
```php
use BackedEnum;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
```

## Values
```php
case AVATARS = 1;         // User profile pictures
case COURSE_IMAGES = 2;   // Course cover images
case EPISODE_FILES = 3;   // Episode content files
case DOCUMENTS = 4;       // Course documents/resources
```

## Do
✅ Use in MediaLibrary addMediaToCollection()
✅ Apply collection-specific validation
✅ Use for media organization queries
✅ Apply collection-specific conversions
✅ Use in media cleanup processes

## Don't
❌ Mix media types in collections
❌ Skip collection validation
❌ Use undefined collection names
❌ Ignore collection-specific rules
❌ Forget to define collection constraints

# app/Enums/ImageConversion.php

## Purpose
→ Defines image conversion sizes for MediaLibrary
→ Used in responsive image generation
→ Supports optimized image delivery

## Dependencies
```php
use BackedEnum;
use Spatie\MediaLibrary\Conversions\Conversion;
```

## Values
```php
case THUMBNAIL = 1;       // Small thumbnail (150x150)
case MEDIUM = 2;          // Medium size (400x300)
case LARGE = 3;           // Large size (800x600)
case WEBP = 4;            // WebP format conversion
```

## Do
✅ Use in MediaLibrary conversion definitions
✅ Apply responsive image strategies
✅ Use for performance optimization
✅ Generate conversions asynchronously
✅ Use appropriate formats per use case

## Don't
❌ Generate unnecessary conversions
❌ Skip conversion optimization
❌ Use single size for all contexts
❌ Ignore modern image formats
❌ Forget to cleanup old conversions

# app/Enums/FileType.php

## Purpose
→ Categorizes uploaded file types for validation
→ Used in file upload validation and processing
→ Supports type-specific handling logic

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case IMAGE = 1;           // Image files (jpg, png, gif, webp)
case VIDEO = 2;           // Video files (mp4, webm, avi)
case AUDIO = 3;           // Audio files (mp3, wav, ogg)
case DOCUMENT = 4;        // Documents (pdf, doc, txt)
case ARCHIVE = 5;         // Archive files (zip, tar, rar)
```

## Do
✅ Use in file upload validation
✅ Apply type-specific processing
✅ Use for virus scanning configuration
✅ Apply type-specific storage policies
✅ Use in file serving logic

## Don't
❌ Skip file type validation
❌ Trust client-provided MIME types
❌ Use single validation for all types
❌ Ignore type-specific security risks
❌ Forget type-specific size limits

# app/Enums/StorageDisk.php

## Purpose
→ Defines Laravel filesystem disk configurations
→ Used in file storage routing and management
→ Supports multi-cloud storage strategies

## Dependencies
```php
use BackedEnum;
use Illuminate\Support\Facades\Storage;
```

## Values
```php
case LOCAL = 1;           // Local filesystem storage
case PUBLIC = 2;          // Public accessible storage
case S3 = 3;              // Amazon S3 cloud storage
case GCS = 4;             // Google Cloud Storage
```

## Do
✅ Use in Storage::disk() configurations
✅ Apply disk-specific access policies
✅ Use for backup and redundancy
✅ Apply cost optimization strategies
✅ Use environment-specific disks

## Don't
❌ Hardcode disk names in code
❌ Mix public/private content on same disk
❌ Skip disk availability validation
❌ Ignore disk-specific limitations
❌ Forget backup strategies per disk

# app/Enums/ActivityType.php

## Purpose
→ Categorizes user and system activities for logging
→ Used in Spatie ActivityLog integration
→ Supports comprehensive audit trails

## Dependencies
```php
use BackedEnum;
use Spatie\Activitylog\Models\Activity;
```

## Values
```php
case CREATED = 1;         // Resource creation
case UPDATED = 2;         // Resource modification
case DELETED = 3;         // Resource deletion
case VIEWED = 4;          // Resource access/viewing
case DOWNLOADED = 5;      // File downloads
```

## Do
✅ Use in activity logging throughout app
✅ Apply activity-specific analytics
✅ Use for user behavior tracking
✅ Apply in audit trail generation
✅ Use for compliance reporting

## Don't
❌ Log sensitive data in activities
❌ Skip activity type validation
❌ Ignore activity performance impact
❌ Log too granular activities
❌ Forget to cleanup old activity logs

# app/Enums/AuditEvent.php

## Purpose
→ Defines security-focused audit events
→ Used in security monitoring and compliance
→ Tracks critical system events

## Dependencies
```php
use BackedEnum;
```

## Values
```php
case LOGIN = 1;           // User login events
case LOGOUT = 2;          // User logout events
case PAYMENT = 3;         // Payment transactions
case COURSE_ACCESS = 4;   // Course access events
case PROFILE_UPDATE = 5;  // Profile modifications
```

## Do
✅ Use for security event monitoring
✅ Apply in compliance reporting
✅ Use for fraud detection
✅ Apply retention policies
✅ Use in security dashboards

## Don't
❌ Mix audit events with regular activities
❌ Skip critical security events
❌ Log audit events without timestamps
❌ Ignore audit event analysis
❌ Forget audit event archival

# app/Enums/LogLevel.php

## Purpose
→ Defines application logging severity levels
→ Used in Laravel logging configuration
→ Supports structured log management

## Dependencies
```php
use BackedEnum;
use Psr\Log\LogLevel as PsrLogLevel;
```

## Values
```php
case EMERGENCY = 1;       // System unusable
case ALERT = 2;           // Immediate action required
case CRITICAL = 3;        // Critical conditions
case ERROR = 4;           // Error conditions
case WARNING = 5;         // Warning conditions
case NOTICE = 6;          // Normal but significant
case INFO = 7;            // Informational messages
case DEBUG = 8;           // Debug-level messages
```

## Do
✅ Use in logging configuration
✅ Apply appropriate levels per message type
✅ Use for log filtering and analysis
✅ Apply in monitoring alerts
✅ Use environment-specific level thresholds

## Don't
❌ Use wrong severity levels
❌ Log sensitive data at high levels
❌ Ignore log level performance impact
❌ Use DEBUG level in production
❌ Skip log level standardization

# Additional System Enums

## LanguageDirection
→ Purpose: Controls language text direction (LTR/RTL)
→ Values: LTR = 1, RTL = 2
→ Used in: Multi-language UI rendering

## MetricPeriod  
→ Purpose: Defines time periods for system metrics
→ Values: MINUTE = 1, HOURLY = 2, DAILY = 3, WEEKLY = 4, MONTHLY = 5
→ Used in: Analytics and reporting systems

## ConfigType
→ Purpose: Defines configuration value data types
→ Values: STRING = 1, INTEGER = 2, BOOLEAN = 3, ARRAY = 4, OBJECT = 5
→ Used in: Dynamic configuration management

## DeviceType
→ Purpose: Categorizes user devices for analytics
→ Values: DESKTOP = 1, MOBILE = 2, TABLET = 3, UNKNOWN = 4
→ Used in: User analytics and responsive design

## ContentType
→ Purpose: Categorizes content for analytics and sharing
→ Values: COURSE = 1, EPISODE = 2, PROFILE = 3
→ Used in: Content management and analytics