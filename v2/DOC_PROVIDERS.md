# DOC_PROVIDERS.md

## 2030b/app/Providers/AppServiceProvider.php

**Purpose**: Main application service provider handling core application services registration, dependency injection, and global configurations.

**Dependencies**:
```php
use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\Schema;
use Illuminate\Pagination\Paginator;
use Illuminate\Support\Facades\Blade;
use Illuminate\Support\Facades\View;
use Illuminate\Support\Facades\Gate;
use App\Contracts\Services\UserServiceInterface;
use App\Services\User\UserService;
use App\Contracts\Repositories\UserRepositoryInterface;
use App\Repositories\User\UserRepository;
use Illuminate\Database\Eloquent\Model;
```

**Methods**:
- register(): void
- boot(): void
- registerRepositories(): void
- registerServices(): void
- configureDatabase(): void
- configurePagination(): void
- registerBladeDirectives(): void
- configureModels(): void

**Do**:
- Register all core application services and repositories
- Configure database schema default string length
- Set up pagination views and configuration
- Register custom Blade directives for UI helpers
- Configure model strict mode in development
- Register global view composers

**Don't**:
- Don't register heavy services that should be lazy-loaded
- Don't perform database operations in service provider boot
- Don't register environment-specific services here
- Don't ignore service binding failures

---

## 2030b/app/Providers/AuthServiceProvider.php

**Purpose**: Authentication and authorization provider managing policies, gates, and authentication guards configuration.

**Dependencies**:
```php
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;
use Illuminate\Support\Facades\Gate;
use App\Models\User;
use App\Models\DocuCourse;
use App\Models\Episode;
use App\Models\Transaction;
use App\Policies\UserPolicy;
use App\Policies\DocuCoursePolicy;
use App\Policies\EpisodePolicy;
use App\Policies\TransactionPolicy;
use Laravel\Sanctum\Sanctum;
use Spatie\Permission\Models\Role;
use Spatie\Permission\Models\Permission;
```

**Methods**:
- boot(): void
- registerPolicies(): void
- registerGates(): void
- configureSanctum(): void
- registerSuperAdminGate(): void

**Do**:
- Register all model policies for authorization
- Define custom gates for complex authorization logic
- Configure Sanctum token authentication
- Set up role-based access control with Spatie Permission
- Register super admin bypass gates

**Don't**:
- Don't define business logic in gates
- Don't bypass authorization checks in policies
- Don't register policies for models without authorization needs
- Don't ignore policy return values

---

## 2030b/app/Providers/EventServiceProvider.php

**Purpose**: Event and listener registration provider managing application events, observers, and event-driven architecture.

**Dependencies**:
```php
use Illuminate\Auth\Events\Registered;
use Illuminate\Auth\Listeners\SendEmailVerificationNotification;
use Illuminate\Foundation\Support\Providers\EventServiceProvider as ServiceProvider;
use App\Events\UserRegistered;
use App\Events\UserLevelChanged;
use App\Events\CourseCompleted;
use App\Events\EpisodeCompleted;
use App\Events\PaymentProcessed;
use App\Events\AffiliateConversion;
use App\Listeners\CreateUserProfile;
use App\Listeners\UpdateUserLevel;
use App\Listeners\AwardCourseCompletion;
use App\Listeners\ProcessAffiliateCommission;
use App\Listeners\SendWelcomeNotification;
use App\Observers\UserObserver;
use App\Observers\DocuCourseObserver;
use App\Observers\TransactionObserver;
```

**Methods**:
- boot(): void
- listen(): array
- shouldDiscoverEvents(): bool
- discoverEventsWithin(): array
- registerObservers(): void

**Do**:
- Map events to their corresponding listeners
- Register model observers for automated behavior
- Use event discovery for automatic event registration
- Maintain loose coupling between components via events
- Log important business events

**Don't**:
- Don't perform heavy operations in event listeners
- Don't create circular event dependencies
- Don't ignore listener failures silently
- Don't use events for synchronous operations that need immediate feedback

---

## 2030b/app/Providers/RouteServiceProvider.php

**Purpose**: Route configuration provider handling route registration, middleware assignment, and API/web route separation.

**Dependencies**:
```php
use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Foundation\Support\Providers\RouteServiceProvider as ServiceProvider;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\RateLimiter;
use Illuminate\Support\Facades\Route;
use App\Http\Middleware\LocalizationMiddleware;
use App\Http\Middleware\TrackUserActivity;
```

**Methods**:
- boot(): void
- configureRateLimiting(): void
- routes(): void
- mapApiRoutes(): void
- mapWebRoutes(): void
- mapModuleRoutes(): void

**Do**:
- Configure rate limiting for API and authentication routes
- Separate API and web route registration
- Apply appropriate middleware to route groups
- Use route model binding for cleaner controllers
- Register localization routes with proper prefixes

**Don't**:
- Don't define routes directly in the provider
- Don't ignore rate limiting on public endpoints
- Don't mix API and web route concerns
- Don't forget to apply CORS middleware to API routes

---

## 2030b/app/Providers/RepositoryServiceProvider.php

**Purpose**: Repository pattern implementation provider binding repository interfaces to concrete implementations.

**Dependencies**:
```php
use Illuminate\Support\ServiceProvider;
use App\Contracts\Repositories\UserRepositoryInterface;
use App\Contracts\Repositories\DocuCourseRepositoryInterface;
use App\Contracts\Repositories\EpisodeRepositoryInterface;
use App\Contracts\Repositories\TransactionRepositoryInterface;
use App\Contracts\Repositories\AffiliateRepositoryInterface;
use App\Repositories\User\UserRepository;
use App\Repositories\Course\DocuCourseRepository;
use App\Repositories\Course\EpisodeRepository;
use App\Repositories\Payment\TransactionRepository;
use App\Repositories\Affiliate\AffiliateRepository;
```

**Methods**:
- register(): void
- registerUserRepositories(): void
- registerCourseRepositories(): void
- registerPaymentRepositories(): void
- registerAffiliateRepositories(): void
- registerSystemRepositories(): void

**Do**:
- Bind all repository interfaces to concrete implementations
- Group repository registrations by domain
- Use singleton bindings for stateless repositories
- Maintain consistency in repository interface naming
- Register repositories before services that depend on them

**Don't**:
- Don't bind repositories as instances
- Don't register repositories with business logic
- Don't create circular dependencies between repositories
- Don't ignore repository interface contracts

---

## 2030b/app/Providers/PaymentServiceProvider.php

**Purpose**: Payment system provider managing payment gateway integrations, currency handling, and transaction processing services.

**Dependencies**:
```php
use Illuminate\Support\ServiceProvider;
use Stripe\Stripe;
use Binance\Connector\Futures;
use App\Services\Payment\StripeService;
use App\Services\Payment\BinanceService;
use App\Services\Payment\CurrencyService;
use App\Services\Payment\TransactionService;
use App\Contracts\Services\PaymentServiceInterface;
use Akaunting\Money\Money;
use Florianv\Exchanger\Exchanger;
```

**Methods**:
- register(): void
- boot(): void
- registerStripeService(): void
- registerBinanceService(): void
- registerCurrencyService(): void
- configurePaymentGateways(): void

**Do**:
- Initialize payment gateway SDK configurations
- Register payment service implementations
- Configure currency exchange rate providers
- Set up webhook endpoint registrations
- Validate payment gateway credentials

**Don't**:
- Don't expose payment credentials in logs
- Don't process payments without proper validation
- Don't ignore webhook signature verification
- Don't cache sensitive payment data

---

## 2030b/app/Providers/AffiliateServiceProvider.php

**Purpose**: Affiliate system provider managing affiliate program services, commission calculations, and referral tracking.

**Dependencies**:
```php
use Illuminate\Support\ServiceProvider;
use App\Services\Affiliate\AffiliateService;
use App\Services\Affiliate\CommissionService;
use App\Services\Affiliate\ReferralService;
use App\Contracts\Services\AffiliateServiceInterface;
use App\Contracts\Services\CommissionServiceInterface;
use App\Contracts\Services\ReferralServiceInterface;
use Illuminate\Support\Facades\Cookie;
```

**Methods**:
- register(): void
- boot(): void
- registerAffiliateServices(): void
- configureReferralTracking(): void
- registerCommissionCalculators(): void

**Do**:
- Register affiliate service implementations
- Configure referral cookie settings and expiration
- Set up commission calculation rules
- Register affiliate event listeners
- Configure tracking middleware

**Don't**:
- Don't process commissions without validation
- Don't ignore referral attribution rules
- Don't expose affiliate earnings data
- Don't allow commission manipulation

---

## 2030b/app/Providers/MediaServiceProvider.php

**Purpose**: Media handling provider managing file uploads, image processing, and media library integration with Spatie packages.

**Dependencies**:
```php
use Illuminate\Support\ServiceProvider;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Spatie\ImageOptimizer\OptimizerChainFactory;
use Intervention\Image\ImageManager;
use App\Services\Media\MediaService;
use App\Services\Media\ImageProcessingService;
use League\Flysystem\AwsS3V3\AwsS3V3Adapter;
```

**Methods**:
- register(): void
- boot(): void
- configureMediaLibrary(): void
- configureImageOptimization(): void
- configureCloudStorage(): void
- registerMediaServices(): void

**Do**:
- Configure Spatie MediaLibrary collections and conversions
- Set up automatic image optimization
- Configure cloud storage adapters
- Register media processing services
- Set up media validation rules

**Don't**:
- Don't process large files synchronously
- Don't ignore file type validation
- Don't expose direct storage paths
- Don't skip media optimization for performance

---

## 2030b/app/Providers/LocalizationServiceProvider.php

**Purpose**: Localization provider managing multi-language support, RTL/LTR handling, and translation services.

**Dependencies**:
```php
use Illuminate\Support\ServiceProvider;
use Mcamara\LaravelLocalization\LaravelLocalization;
use Spatie\Translatable\Translatable;
use App\Services\Localization\TranslationService;
use App\Http\Middleware\LocalizationMiddleware;
use Illuminate\Support\Facades\App;
```

**Methods**:
- register(): void
- boot(): void
- configureSupportedLocales(): void
- configureTranslatableModels(): void
- registerTranslationServices(): void
- setupRTLSupport(): void

**Do**:
- Configure supported locales and fallbacks
- Register translation service implementations
- Set up RTL language detection and handling
- Configure translatable model attributes
- Register localization middleware

**Don't**:
- Don't hardcode language strings in templates
- Don't ignore RTL layout requirements
- Don't cache translated content without locale keys
- Don't mix translation approaches in the same model

---

## 2030b/app/Providers/HorizonServiceProvider.php

**Purpose**: Laravel Horizon provider for queue monitoring, job processing management, and Redis queue configuration.

**Dependencies**:
```php
use Illuminate\Support\ServiceProvider;
use Laravel\Horizon\Horizon;
use Laravel\Horizon\HorizonApplicationServiceProvider;
use Illuminate\Support\Facades\Gate;
use App\Models\User;
```

**Methods**:
- register(): void
- boot(): void
- gate(): bool
- authorization(): void
- configureQueues(): void

**Do**:
- Configure Horizon dashboard authorization
- Set up queue processing rules and limits
- Configure Redis connection for queues
- Monitor failed job handling
- Set up queue worker scaling rules

**Don't**:
- Don't expose Horizon dashboard publicly
- Don't ignore queue processing failures
- Don't run heavy jobs without proper timeout
- Don't forget to configure queue priorities

---

## 2030b/app/Providers/TelescopeServiceProvider.php

**Purpose**: Laravel Telescope provider for application debugging, performance monitoring, and development insights.

**Dependencies**:
```php
use Illuminate\Support\ServiceProvider;
use Laravel\Telescope\Telescope;
use Laravel\Telescope\TelescopeApplicationServiceProvider;
use Illuminate\Support\Facades\Gate;
use App\Models\User;
```

**Methods**:
- register(): void
- boot(): void
- gate(): bool
- authorization(): void
- configureTelescope(): void
- hideFromTelescope(): void

**Do**:
- Configure Telescope dashboard authorization
- Filter sensitive data from Telescope entries
- Set up performance monitoring thresholds
- Configure entry pruning and cleanup
- Monitor database queries and performance

**Don't**:
- Don't enable Telescope in production without restrictions
- Don't log sensitive user data
- Don't ignore performance alerts from Telescope
- Don't forget to prune old telescope entries

---

## 2030b/app/Providers/ContractServiceProvider.php

**Purpose**: Contract binding provider managing interface-to-implementation bindings for dependency injection.

**Dependencies**:
```php
use Illuminate\Support\ServiceProvider;
use App\Contracts\Services\UserServiceInterface;
use App\Contracts\Services\CourseServiceInterface;
use App\Contracts\Services\PaymentServiceInterface;
use App\Contracts\Services\AffiliateServiceInterface;
use App\Services\User\UserService;
use App\Services\Course\CourseService;
use App\Services\Payment\PaymentService;
use App\Services\Affiliate\AffiliateService;
```

**Methods**:
- register(): void
- registerUserContracts(): void
- registerCourseContracts(): void
- registerPaymentContracts(): void
- registerAffiliateContracts(): void
- registerSystemContracts(): void

**Do**:
- Bind all service interfaces to implementations
- Group contract bindings by domain
- Use singleton bindings for stateless services
- Maintain consistency in contract naming
- Document contract dependencies

**Don't**:
- Don't bind contracts to concrete classes without interfaces
- Don't create circular dependencies
- Don't bind services with heavy initialization
- Don't ignore contract method signatures

---

## 2030b/app/Providers/CommandBusServiceProvider.php

**Purpose**: Command bus provider implementing CQRS pattern for command handling and business operation dispatch.

**Dependencies**:
```php
use Illuminate\Support\ServiceProvider;
use App\CQRS\Commands\CommandBus;
use App\CQRS\Commands\CommandHandlerInterface;
use App\CQRS\Commands\CreateUserCommand;
use App\CQRS\Commands\ProcessPaymentCommand;
use App\CQRS\Commands\Handlers\CreateUserHandler;
use App\CQRS\Commands\Handlers\ProcessPaymentHandler;
```

**Methods**:
- register(): void
- registerCommandBus(): void
- registerCommandHandlers(): void
- mapCommandsToHandlers(): void
- configureMiddleware(): void

**Do**:
- Register command bus implementation
- Map commands to their handlers
- Configure command middleware pipeline
- Validate command handler registrations
- Set up command logging and monitoring

**Don't**:
- Don't process commands without validation
- Don't create commands with business logic
- Don't ignore command handler failures
- Don't mix queries with commands

---

## 2030b/app/Providers/QueryBusServiceProvider.php

**Purpose**: Query bus provider implementing CQRS pattern for query handling and read operation dispatch.

**Dependencies**:
```php
use Illuminate\Support\ServiceProvider;
use App\CQRS\Queries\QueryBus;
use App\CQRS\Queries\QueryHandlerInterface;
use App\CQRS\Queries\GetUserQuery;
use App\CQRS\Queries\GetCoursesQuery;
use App\CQRS\Queries\Handlers\GetUserHandler;
use App\CQRS\Queries\Handlers\GetCoursesHandler;
```

**Methods**:
- register(): void
- registerQueryBus(): void
- registerQueryHandlers(): void
- mapQueriesToHandlers(): void
- configureCaching(): void

**Do**:
- Register query bus implementation
- Map queries to their handlers
- Configure query result caching
- Set up read-only database connections
- Implement query performance monitoring

**Don't**:
- Don't modify data in query handlers
- Don't create queries without proper DTOs
- Don't ignore query performance issues
- Don't cache sensitive query results
