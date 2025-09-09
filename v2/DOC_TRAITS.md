# DOC_TRAITS.md

## Traits - Reusable Cross-Cutting Behaviors

### Overview
Traits provide reusable functionality that can be mixed into multiple classes. They implement cross-cutting concerns like UUID generation, activity logging, media handling, and caching strategies.

### Architecture Principles
- **Single Responsibility**: Each trait handles one specific concern
- **Composition over Inheritance**: Traits enable multiple behavior composition
- **Laravel Integration**: Seamless integration with Eloquent models and Laravel ecosystem
- **Boot Methods**: Proper use of Laravel model boot lifecycle
- **Spatie Package Integration**: Enhanced functionality through Spatie packages

---

## app/Traits/HasUuid.php

**Purpose**: Provides automatic UUID generation and management for Eloquent models using the dual ID+UUID strategy for local performance and external security.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Support\Str;
use Ramsey\Uuid\Uuid;
```

**Methods**:
- bootHasUuid()
- initializeHasUuid()
- generateUuid()
- getRouteKeyName()
- resolveRouteBinding()

**Do**:
✅ Always use UUID for external API responses and public URLs
✅ Index UUID columns for performance
✅ Use auto-increment ID for internal foreign key relationships
✅ Implement proper route model binding with UUID

**Don't**:
❌ Don't expose auto-increment IDs in public APIs
❌ Don't skip UUID indexing in migrations
❌ Don't use UUID for internal joins (use ID instead)
❌ Don't generate UUID manually (let trait handle it)

---

## app/Traits/LogsActivity.php

**Purpose**: Integrates with Spatie Activity Log package to automatically track model changes, user actions, and system events for audit trails and compliance.

**Dependencies**:
```php
use Spatie\Activitylog\Traits\LogsActivity as SpatieLogsActivity;
use Spatie\Activitylog\LogOptions;
use Spatie\Activitylog\Models\Activity;
```

**Methods**:
- getActivitylogOptions()
- tapActivity()
- getDescriptionForEvent()
- getLogNameToUse()
- attributesToBeLogged()

**Do**:
✅ Log sensitive operations (payments, user data changes)
✅ Customize log descriptions for business context
✅ Use proper log names for different event types
✅ Filter sensitive attributes from logs

**Don't**:
❌ Don't log everything (performance impact)
❌ Don't store sensitive data in activity logs
❌ Don't ignore performance implications of extensive logging
❌ Don't log system-generated updates without business value

---

## app/Traits/HasMedia.php

**Purpose**: Integrates Spatie Media Library for file uploads, image processing, and media management with automatic conversions and optimizations.

**Dependencies**:
```php
use Spatie\MediaLibrary\HasMedia as SpatieHasMedia;
use Spatie\MediaLibrary\InteractsWithMedia;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Spatie\Image\Enums\Fit;
```

**Methods**:
- registerMediaConversions()
- registerMediaCollections()
- getMediaCollection()
- addMediaFromRequest()
- getFirstMediaUrl()

**Do**:
✅ Define specific media collections (avatars, course_covers, documents)
✅ Create responsive image conversions (thumbnail, medium, large)
✅ Set file size and type restrictions per collection
✅ Use WebP conversions for better performance

**Don't**:
❌ Don't store media without proper validation
❌ Don't create unnecessary image conversions
❌ Don't forget to configure storage disks properly
❌ Don't ignore file size limits and security validation

---

## app/Traits/HasPermissions.php

**Purpose**: Integrates Spatie Permission package for role-based access control, providing methods for permission checking and role management.

**Dependencies**:
```php
use Spatie\Permission\Traits\HasRoles;
use Spatie\Permission\Models\Permission;
use Spatie\Permission\Models\Role;
```

**Methods**:
- hasPermissionTo()
- assignRole()
- removeRole()
- syncRoles()
- getPermissionsViaRoles()
- getAllPermissions()

**Do**:
✅ Use specific permission names (create_course, edit_payment)
✅ Implement hierarchical roles (super_admin > admin > moderator)
✅ Cache permission checks for performance
✅ Use middleware for route-level permission enforcement

**Don't**:
❌ Don't hardcode permission checks in multiple places
❌ Don't create overly granular permissions without need
❌ Don't bypass permission system for "convenience"
❌ Don't forget to clear permission cache after changes

---

## app/Traits/Billable.php

**Purpose**: Provides payment processing capabilities integrating with multiple providers (Stripe, Binance) and handles billing operations, invoice generation, and transaction management.

**Dependencies**:
```php
use Laravel\Cashier\Billable as CashierBillable;
use App\Services\Payment\PaymentService;
use App\Services\Payment\StripeService;
use App\Services\Payment\BinanceService;
use Money\Money;
```

**Methods**:
- charge()
- refund()
- createInvoice()
- processPayment()
- getBillingHistory()
- syncWithPaymentProvider()

**Do**:
✅ Handle multiple payment providers gracefully
✅ Store all transactions with proper audit trail
✅ Implement idempotency for payment operations
✅ Use proper money objects for currency calculations

**Don't**:
❌ Don't process payments without proper validation
❌ Don't store sensitive payment data locally
❌ Don't ignore webhook signature verification
❌ Don't forget proper error handling and retries

---

## app/Traits/Trackable.php

**Purpose**: Provides comprehensive user activity tracking including page views, course progress, affiliate clicks, and engagement metrics for analytics and personalization.

**Dependencies**:
```php
use App\Models\Analytics\UserSession;
use App\Models\Analytics\ContentView;
use App\Models\Analytics\EngagementMetric;
use Illuminate\Http\Request;
```

**Methods**:
- trackView()
- trackProgress()
- trackClick()
- trackEngagement()
- getTrackingData()
- recordSession()

**Do**:
✅ Track user journey for UX improvements
✅ Respect privacy settings and GDPR compliance
✅ Use efficient batch processing for analytics
✅ Implement proper data retention policies

**Don't**:
❌ Don't track without user consent where required
❌ Don't create performance bottlenecks with excessive tracking
❌ Don't store personally identifiable information unnecessarily
❌ Don't ignore data privacy regulations

---

## app/Traits/Progressable.php

**Purpose**: Manages user progress tracking through courses and episodes with completion percentages, time spent calculations, and achievement unlocking.

**Dependencies**:
```php
use App\Models\User\UserProgress;
use App\Events\EpisodeCompleted;
use App\Events\UserLevelChanged;
use Carbon\Carbon;
```

**Methods**:
- updateProgress()
- markCompleted()
- calculateCompletionPercentage()
- getTimeSpent()
- awardPoints()
- checkLevelUp()

**Do**:
✅ Update progress incrementally for better UX
✅ Fire events for gamification features
✅ Calculate accurate time spent metrics
✅ Handle resume functionality properly

**Don't**:
❌ Don't lose progress data due to technical issues
❌ Don't allow progress manipulation by users
❌ Don't ignore performance when updating frequently
❌ Don't forget to validate progress data integrity

---

## app/Traits/Localizable.php

**Purpose**: Provides multi-language support with RTL language handling, locale detection, and content translation using Laravel Localization and Spatie Translatable packages.

**Dependencies**:
```php
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
use Spatie\Translatable\HasTranslations;
use App\Enums\LanguageDirection;
use App\Enums\TextDirection;
```

**Methods**:
- setLocale()
- getTranslation()
- setTranslation()
- isRtlLanguage()
- getAvailableLocales()
- fallbackLocale()

**Do**:
✅ Support both LTR and RTL languages properly
✅ Implement proper fallback mechanisms
✅ Use efficient translation caching
✅ Validate translations before storing

**Don't**:
❌ Don't assume left-to-right text direction
❌ Don't hardcode language-specific layouts
❌ Don't ignore translation completeness validation
❌ Don't forget to update URLs for localized routes

---

## app/Traits/Searchable.php

**Purpose**: Integrates Laravel Scout and Spatie Searchable for full-text search capabilities across courses, episodes, and user content with advanced filtering and relevance scoring.

**Dependencies**:
```php
use Laravel\Scout\Searchable as ScoutSearchable;
use Spatie\Searchable\Searchable as SpatieSearchable;
use Spatie\Searchable\SearchResult;
```

**Methods**:
- toSearchableArray()
- searchableAs()
- getSearchResult()
- shouldBeSearchable()
- makeSearchable()
- makeUnsearchable()

**Do**:
✅ Index relevant content for search performance
✅ Implement search result ranking and relevance
✅ Use appropriate search drivers (database, Algolia, etc.)
✅ Handle search query sanitization

**Don't**:
❌ Don't index everything without considering relevance
❌ Don't ignore search performance optimization
❌ Don't forget to update search index on content changes
❌ Don't expose sensitive content in search results

---

## app/Traits/Cacheable.php

**Purpose**: Provides intelligent caching strategies for expensive operations with automatic cache invalidation, tag-based clearing, and multi-level caching support.

**Dependencies**:
```php
use Illuminate\Support\Facades\Cache;
use Illuminate\Cache\TaggedCache;
use Illuminate\Database\Eloquent\Model;
```

**Methods**:
- getCacheKey()
- getCacheTags()
- remember()
- forget()
- flush()
- invalidateRelatedCache()

**Do**:
✅ Use tagged caching for efficient group invalidation
✅ Set appropriate cache TTL based on data volatility
✅ Implement cache warming for critical data
✅ Monitor cache hit ratios for optimization

**Don't**:
❌ Don't cache user-specific data globally
❌ Don't ignore cache invalidation on data updates
❌ Don't cache everything without performance analysis
❌ Don't forget to handle cache failures gracefully

---

## app/Traits/Backupable.php

**Purpose**: Integrates with Spatie Backup package for automated data backup, export functionality, and disaster recovery with configurable retention policies.

**Dependencies**:
```php
use Spatie\Backup\Tasks\Backup\BackupJob;
use Spatie\Backup\BackupDestination\BackupDestination;
use Illuminate\Console\Command;
```

**Methods**:
- backup()
- restore()
- getBackupDestinations()
- scheduleBackup()
- validateBackup()
- cleanupOldBackups()

**Do**:
✅ Implement automated backup scheduling
✅ Verify backup integrity regularly
✅ Store backups in multiple locations
✅ Test restore procedures regularly

**Don't**:
❌ Don't backup without encryption for sensitive data
❌ Don't ignore backup storage costs
❌ Don't forget to test backup restoration
❌ Don't skip backup monitoring and alerting

---

## Trait Integration Best Practices

### Boot Method Priority
1. HasUuid (infrastructure)
2. LogsActivity (auditing)
3. Searchable (indexing)
4. Cacheable (performance)

### Performance Considerations
- Limit traits per model (max 5-6 recommended)
- Use lazy loading for expensive trait operations
- Implement proper caching for frequently accessed trait methods
- Monitor database queries from trait operations

### Testing Requirements
- Unit test each trait in isolation
- Integration test trait combinations
- Test boot method interactions
- Verify event firing and listening

### Spatie Package Integration
- spatie/laravel-activitylog for LogsActivity
- spatie/laravel-medialibrary for HasMedia  
- spatie/laravel-permission for HasPermissions
- spatie/laravel-translatable for Localizable
- spatie/laravel-searchable for Searchable
- spatie/laravel-backup for Backupable