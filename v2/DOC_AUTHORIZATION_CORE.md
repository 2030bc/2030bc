# DOC_AUTHORIZATION_CORE.md

## Overview
The Authorization Core provides a comprehensive, multi-layered security architecture for the Laravel docu-course platform, integrating CQRS patterns, Spatie Permission package, Laravel policies, middleware security, and the 14-level user progression system. This unified authorization framework ensures consistent, secure, and scalable access control across all application layers. Updated.

---

## Multi-Layer Authorization Architecture

### Layer 1: Authentication & Identity
**Foundation Layer** - Establishes user identity and session management

**Components**:
- **Laravel Breeze**: Web-based authentication scaffolding
- **Laravel Sanctum**: API token-based authentication
- **Two-Factor Authentication**: Google2FA integration for enhanced security
- **Social Authentication**: OAuth integration via Laravel Socialite

**Integration Points**:
- User model with HasApiTokens trait for Sanctum integration
- AuthService for centralized authentication logic
- Session management with secure cookie configuration
- Password policies enforced through StrongPassword validation rule

### Layer 2: Role-Based Access Control (Spatie Permission)
**Permission Layer** - Defines roles, permissions, and assignments

**Spatie Integration Components**:
```php
// Core Spatie models and traits
Spatie\Permission\Models\Role
Spatie\Permission\Models\Permission
Spatie\Permission\Traits\HasRoles (User model)
Spatie\Permission\Traits\HasPermissions (User model)
```

**Permission Matrix**:
- **Super Administrator**: All system permissions, configuration access
- **Administrator**: User management, content moderation, system monitoring
- **Moderator**: Content review, user support, basic administration
- **Instructor**: Course creation, student management, analytics access
- **Premium User**: Enhanced features, priority support, advanced tools
- **Standard User**: Basic platform access, course enrollment, progress tracking

**Role Hierarchy Implementation**:
```php
// Role inheritance structure
Super Administrator → Administrator → Moderator → Instructor → Premium User → Standard User
```

**Service Integration**:
- **PermissionService**: Creates, assigns, and manages permissions
- **RoleService**: Handles role assignments and hierarchy management
- **User model methods**: hasPermissionTo(), assignRole(), syncRoles()

### Layer 3: User Progression System
**Level-Based Layer** - 14-level user advancement system affecting access rights

**Level-Based Access Control**:
- **Levels 1-3 (Novice)**: Basic course access, limited upload quotas (100MB)
- **Levels 4-6 (Intermediate)**: Enhanced features, increased quotas (250MB)
- **Levels 7-10 (Advanced)**: Course creation abilities, instructor features (500MB)
- **Levels 11-14 (Expert/VIP)**: Premium content access, maximum benefits (1GB)

**Service Integration**:
- **LevelService**: Calculates levels, manages progression, distributes rewards
- **User progression tracking**: Points accumulation and level advancement
- **Feature unlocking**: Progressive feature access based on user levels

### Layer 4: Laravel Policy System
**Resource-Level Layer** - Fine-grained resource access control

**Policy Integration Matrix**:
- **UserPolicy**: Profile management, administrative actions, role assignments
- **DocuCoursePolicy**: Course access, enrollment validation, content viewing
- **EpisodePolicy**: Episode access, progress tracking, note creation
- **TransactionPolicy**: Payment access, refund processing, financial reporting
- **AffiliatePolicy**: Commission tracking, payout requests, fraud prevention
- **MediaPolicy**: File access, upload permissions, storage quota enforcement

**Authorization Gate Registration**:
```php
// AuthServiceProvider integration
Gate::resource('courses', DocuCoursePolicy::class);
Gate::define('admin.panel', [UserPolicy::class, 'accessAdminPanel']);
Gate::before(function ($user, $ability) {
    return $user->hasRole('Super Administrator') ? true : null;
});
```

### Layer 5: Middleware Security
**Request-Level Layer** - HTTP request filtering and validation

**Authorization Middleware Chain**:
- **CheckPermission**: Validates specific permissions using Spatie package
- **CheckRole**: Enforces role-based access with hierarchy support
- **CheckUserLevel**: Validates user progression requirements
- **ValidateCourseAccess**: Course-specific access validation
- **ValidateOwnership**: Resource ownership verification
- **RateLimitableMiddleware**: API rate limiting and throttling

**Middleware Integration Pattern**:
```php
// Route group with layered authorization
Route::middleware(['auth', 'verified', 'permission:courses.create', 'user.level:7'])
    ->group(function () {
        Route::post('/courses', [CourseController::class, 'store']);
    });
```

### Layer 6: Service-Level Authorization
**Business Logic Layer** - Service method authorization

**Authorization Pattern in Services**:
```php
// Service-level authorization example
public function createCourse(CreateCourseDTO $dto, User $user): Course
{
    // Multi-layer authorization check
    if (!$user->hasPermissionTo('courses.create')) {
        throw new UnauthorizedException('Insufficient permissions');
    }
    
    if (!$this->levelService->checkLevelAccess($user->id, 'course_creation')) {
        throw new InsufficientLevelException('Minimum level 7 required');
    }
    
    // Business logic continues...
}
```

**Service Authorization Integration**:
- **CourseService**: Enrollment validation, access level checks
- **PaymentService**: Transaction authorization, financial access
- **AffiliateService**: Commission access, payout authorization
- **UserService**: Profile management, administrative operations

---

## CQRS Authorization Implementation

### Command Authorization
**Write Operations** - Commands that modify system state

**Command Handler Authorization Pattern**:
```php
abstract class AuthorizedCommandHandler implements CommandHandlerInterface
{
    protected function authorize(User $user, string $permission): void
    {
        if (!$user->hasPermissionTo($permission)) {
            throw new UnauthorizedException();
        }
    }
    
    protected function authorizeOwnership(User $user, Model $resource): void
    {
        if ($resource->user_id !== $user->id && !$user->hasRole('Administrator')) {
            throw new OwnershipException();
        }
    }
}
```

**Command Authorization Examples**:
- **CreateCourseCommand**: Requires 'courses.create' permission and minimum level 7
- **UpdateUserCommand**: Requires ownership or 'users.update' permission
- **ProcessPaymentCommand**: Requires transaction ownership or 'payments.process' permission
- **AssignRoleCommand**: Requires 'roles.assign' permission and role hierarchy validation

### Query Authorization
**Read Operations** - Queries that retrieve system data

**Query Handler Authorization Pattern**:
```php
abstract class AuthorizedQueryHandler implements QueryHandlerInterface
{
    protected function authorizeRead(User $user, string $permission): void
    {
        if (!$user->hasPermissionTo($permission)) {
            throw new UnauthorizedException();
        }
    }
    
    protected function filterByAccess(User $user, Collection $resources): Collection
    {
        return $resources->filter(function ($resource) use ($user) {
            return $this->canAccess($user, $resource);
        });
    }
}
```

**Query Authorization Examples**:
- **GetUserQuery**: Filters users based on privacy settings and administrative access
- **GetCoursesQuery**: Filters courses by access level, enrollment status, and publication state
- **GetTransactionsQuery**: Restricts to owned transactions unless administrative access
- **GetAnalyticsQuery**: Requires appropriate reporting permissions and data access levels

---

## API Authorization Architecture

### Sanctum Token Management
**API Authentication** - Token-based API access control

**Token Scopes and Abilities**:
```php
// Token creation with specific abilities
$user->createToken('course-management', [
    'courses:read',
    'courses:create',
    'episodes:read',
    'episodes:update'
])->plainTextToken;
```

**API Authorization Middleware**:
- **auth:sanctum**: Validates API tokens and establishes user context
- **ability:permission**: Checks token abilities for specific actions
- **throttle:api**: Rate limiting for API endpoints
- **api.auth**: Custom API authentication middleware

### API Gateway Authorization
**External Access Control** - Third-party API access management

**API Key Management**:
- **API keys**: Generated for external integrations
- **Rate limiting**: Per-key rate limits and quotas
- **Scope restrictions**: Limited access to specific endpoints
- **Usage tracking**: API usage analytics and billing integration

---

## Course Access Control System

### Content Access Matrix
**Multi-Dimensional Access Control** - Course content access based on multiple factors

**Access Dimensions**:
1. **User Level**: Minimum progression level required
2. **Payment Status**: Free, premium, or VIP content
3. **Enrollment Status**: Enrolled, guest, or preview access
4. **Publication Status**: Published, draft, or preview content
5. **Geographic Restrictions**: Region-based content availability

**Access Validation Logic**:
```php
public function validateCourseAccess(User $user, Course $course): bool
{
    // Level requirement check
    if ($course->min_level > $user->level) {
        return false;
    }
    
    // Payment validation for premium content
    if ($course->isPremium() && !$user->hasPaidAccess($course)) {
        return false;
    }
    
    // Enrollment requirement
    if ($course->requiresEnrollment() && !$user->isEnrolled($course)) {
        return false;
    }
    
    return true;
}
```

### Progressive Content Unlocking
**Sequential Access Control** - Episode-by-episode progression

**Unlock Criteria**:
- **Sequential completion**: Previous episodes must be completed
- **Time restrictions**: Minimum time spent on previous content
- **Assessment requirements**: Quizzes or evaluations must be passed
- **Engagement thresholds**: Minimum engagement metrics achieved

---

## Affiliate Authorization System

### Commission Access Control
**Financial Authorization** - Commission and payout access management

**Authorization Levels**:
- **Affiliate View**: Own commission data and basic analytics
- **Affiliate Admin**: Team commission management and advanced reporting
- **Financial Admin**: All commission data and payout processing
- **Compliance Officer**: Audit access and regulatory reporting

**Fraud Prevention Authorization**:
- **Identity verification**: Enhanced authentication for financial operations
- **Behavioral analysis**: Suspicious activity detection and authorization holds
- **Compliance checks**: Regulatory compliance validation for payouts
- **Multi-factor approval**: Enhanced authorization for large transactions

---

## Security Integration & Compliance

### Audit & Logging Integration
**Security Monitoring** - Comprehensive authorization event logging

**Spatie ActivityLog Integration**:
```php
// Automatic authorization logging
activity('authorization')
    ->causedBy($user)
    ->performedOn($resource)
    ->withProperties([
        'permission' => $permission,
        'role' => $user->getRoleNames(),
        'level' => $user->level,
        'result' => $authorized
    ])
    ->log($authorized ? 'authorized' : 'denied');
```

**Security Event Types**:
- **Permission grants/denials**: All permission check results
- **Role assignments**: Role changes and assignments
- **Level progressions**: User level advancement events
- **Access violations**: Unauthorized access attempts
- **Administrative actions**: Administrative authorization events

### Rate Limiting & Throttling
**Abuse Prevention** - Authorization-aware rate limiting

**Rate Limiting Strategies**:
- **User-level limits**: Different limits based on user roles and levels
- **Permission-based throttling**: Enhanced limits for privileged operations
- **Geographic restrictions**: Region-based access control and limiting
- **Temporal restrictions**: Time-based access windows and scheduling

---

## Testing & Quality Assurance

### Authorization Testing Framework
**Security Validation** - Comprehensive authorization testing

**Test Categories**:
- **Permission Tests**: Spatie permission integration validation
- **Policy Tests**: Laravel policy authorization verification
- **Level Tests**: User progression access control testing
- **Integration Tests**: Multi-layer authorization workflow testing
- **Security Tests**: Authorization bypass and vulnerability testing

**Test Coverage Requirements**:
- **95% coverage**: All authorization decision paths
- **Edge case testing**: Boundary conditions and error scenarios
- **Performance testing**: Authorization decision latency benchmarking
- **Security auditing**: Regular penetration testing and vulnerability assessment

### Authorization Monitoring
**Runtime Security** - Production authorization monitoring

**Monitoring Metrics**:
- **Authorization success rates**: Permission grant/denial ratios
- **Performance metrics**: Authorization decision latency tracking
- **Security incidents**: Failed authorization attempt patterns
- **Usage analytics**: Permission and role usage statistics
- **Compliance reporting**: Regulatory compliance metric tracking

---

## Best Practices & Implementation Guidelines

### Security Principles
- **Defense in Depth**: Multiple authorization layers for critical operations
- **Principle of Least Privilege**: Minimum necessary permissions granted
- **Fail Secure**: Authorization failures default to denial
- **Audit Everything**: Comprehensive logging of authorization decisions

### Performance Optimization
- **Permission Caching**: Strategic caching of frequently checked permissions
- **Role Hierarchy Optimization**: Efficient role inheritance checking
- **Query Optimization**: Minimized database queries for authorization
- **Lazy Loading**: Efficient loading of user roles and permissions

### Integration Standards
- **Consistent Patterns**: Uniform authorization implementation across layers
- **Service Contracts**: Well-defined interfaces for authorization services
- **Event Integration**: Authorization events properly integrated with domain events
- **Error Handling**: Standardized authorization error responses and logging

---

## Commands

### app/Commands/CreateUserCommand.php
**Purpose**: Command DTO for user creation operations
**Dependencies**: 
- use App\Foundation\Core\BaseDto
- use App\DTOs\User\ProfileData
- use App\Enums\UserStatus
- use App\Enums\UserLevel

**Properties**:
- name
- email
- password
- firstName
- lastName
- preferredLanguage
- textDirection
- timezone

**Do**:
- ✅ Validate all input data in constructor
- ✅ Use readonly properties for immutability
- ✅ Include all required data for user creation
- ✅ Implement proper type hints and validation

**Don't**:
- ❌ Include business logic in command objects
- ❌ Make properties mutable after construction
- ❌ Skip validation of required fields
- ❌ Include database operations directly

### app/Commands/UpdateUserCommand.php
**Purpose**: Command DTO for user update operations
**Dependencies**:
- use App\Foundation\Core\BaseDto
- use App\DTOs\User\ProfileData
- use App\Enums\UserStatus
- use App\Enums\UserLevel

**Properties**:
- userId
- name
- firstName
- lastName
- preferredLanguage
- timezone
- status
- level

**Do**:
- ✅ Include user ID for identification
- ✅ Support partial updates with nullable properties
- ✅ Validate enum values
- ✅ Maintain data integrity

**Don't**:
- ❌ Allow updating of immutable fields (email, UUID)
- ❌ Skip authorization checks
- ❌ Include sensitive data updates (password)
- ❌ Modify multiple aggregates

### app/Commands/DeleteUserCommand.php
**Purpose**: Command DTO for user deletion (soft delete) operations
**Dependencies**:
- use App\Foundation\Core\BaseDto
- use Ramsey\Uuid\UuidInterface

**Properties**:
- userId
- reason
- deletedBy

**Do**:
- ✅ Use soft delete by default
- ✅ Log deletion reason
- ✅ Track who performed deletion
- ✅ Validate user exists before deletion

**Don't**:
- ❌ Perform hard deletes without explicit flag
- ❌ Delete users with active transactions
- ❌ Skip audit logging
- ❌ Allow self-deletion for admins

### app/Commands/CreateCourseCommand.php
**Purpose**: Command DTO for course creation operations
**Dependencies**:
- use App\Foundation\Core\BaseDto
- use App\DTOs\Course\CourseData
- use App\Enums\CourseStatus
- use App\Enums\ContentAccessLevel

**Properties**:
- title
- alias
- description
- categoryId
- authorId
- languageId
- accessLevel
- requiredUserLevel
- price
- currencyId

**Do**:
- ✅ Validate unique course alias
- ✅ Ensure author has instructor permissions
- ✅ Validate currency for paid courses
- ✅ Set appropriate access levels

**Don't**:
- ❌ Create courses without proper authorization
- ❌ Skip price validation for paid content
- ❌ Allow duplicate aliases
- ❌ Set inconsistent access levels

### app/Commands/UpdateCourseCommand.php
**Purpose**: Command DTO for course update operations
**Dependencies**:
- use App\Foundation\Core\BaseDto
- use App\DTOs\Course\CourseData
- use App\Enums\CourseStatus

**Properties**:
- courseId
- title
- description
- categoryId
- status
- accessLevel
- price
- currencyId

**Do**:
- ✅ Validate course ownership
- ✅ Maintain version control
- ✅ Update search indexes
- ✅ Notify enrolled users of changes

**Don't**:
- ❌ Allow unauthorized updates
- ❌ Change published course structure drastically
- ❌ Skip price change notifications
- ❌ Modify enrollment-affecting fields without consent

### app/Commands/PublishCourseCommand.php
**Purpose**: Command DTO for course publishing operations
**Dependencies**:
- use App\Foundation\Core\BaseDto
- use App\Enums\CourseStatus

**Properties**:
- courseId
- publishedBy
- publishedAt
- notifySubscribers

**Do**:
- ✅ Validate course is complete
- ✅ Check all episodes are ready
- ✅ Verify media files are processed
- ✅ Update search indexes

**Don't**:
- ❌ Publish incomplete courses
- ❌ Skip content validation
- ❌ Forget to update metrics
- ❌ Ignore SEO meta requirements

### app/Commands/ProcessPaymentCommand.php
**Purpose**: Command DTO for payment processing operations
**Dependencies**:
- use App\Foundation\Core\BaseDto
- use App\DTOs\Payment\PaymentData
- use App\Enums\PaymentProvider
- use App\Enums\CurrencyType

**Properties**:
- userId
- amount
- currencyId
- provider
- paymentMethodId
- description
- metadata

**Do**:
- ✅ Validate payment amounts
- ✅ Use appropriate currency precision
- ✅ Include fraud detection data
- ✅ Implement idempotency keys

**Don't**:
- ❌ Process payments without verification
- ❌ Skip amount validation
- ❌ Store sensitive payment data
- ❌ Forget transaction logging

### app/Commands/CreateAffiliateCommand.php
**Purpose**: Command DTO for affiliate account creation
**Dependencies**:
- use App\Foundation\Core\BaseDto
- use App\DTOs\Affiliate\AffiliateData
- use App\Enums\AffiliateStatus

**Properties**:
- userId
- commissionRate
- status
- approvedBy

**Do**:
- ✅ Validate user eligibility
- ✅ Set appropriate commission rates
- ✅ Require approval process
- ✅ Generate unique referral codes

**Don't**:
- ❌ Auto-approve all applications
- ❌ Set excessive commission rates
- ❌ Skip background checks
- ❌ Allow duplicate affiliate accounts

### app/Commands/CalculateCommissionCommand.php
**Purpose**: Command DTO for commission calculation operations
**Dependencies**:
- use App\Foundation\Core\BaseDto
- use App\DTOs\Affiliate\CommissionData
- use App\Enums\CommissionStatus

**Properties**:
- affiliateId
- conversionId
- amount
- rate
- calculatedAt

**Do**:
- ✅ Use precise decimal calculations
- ✅ Apply tier-based rates
- ✅ Include tax considerations
- ✅ Track calculation history

**Don't**:
- ❌ Use floating point for money calculations
- ❌ Skip rate validation
- ❌ Forget minimum payout thresholds
- ❌ Calculate without proper attribution

---

## Queries

### app/Queries/GetUserQuery.php
**Purpose**: Query DTO for user retrieval operations
**Dependencies**:
- use App\Foundation\Core\BaseDto
- use Ramsey\Uuid\UuidInterface

**Properties**:
- userId
- includeProfile
- includeProgress
- includePermissions

**Methods**:
- validate()
- getFilters()
- getIncludes()

**Do**:
- ✅ Support selective field inclusion
- ✅ Implement proper caching
- ✅ Validate user existence
- ✅ Respect privacy settings

**Don't**:
- ❌ Return sensitive data unnecessarily
- ❌ Skip authorization checks
- ❌ Include too much related data
- ❌ Expose internal IDs

### app/Queries/GetUserProfileQuery.php
**Purpose**: Query DTO for user profile retrieval
**Dependencies**:
- use App\Foundation\Core\BaseDto
- use App\Enums\UserLevel

**Properties**:
- userId
- includeStatistics
- includeAchievements
- publicOnly

**Methods**:
- validate()
- getIncludes()
- isPublicAccess()

**Do**:
- ✅ Respect privacy settings
- ✅ Include relevant statistics
- ✅ Cache frequently accessed profiles
- ✅ Support public/private views

**Don't**:
- ❌ Expose private information
- ❌ Skip privacy validation
- ❌ Include unnecessary data
- ❌ Forget rate limiting

### app/Queries/GetCourseQuery.php
**Purpose**: Query DTO for single course retrieval
**Dependencies**:
- use App\Foundation\Core\BaseDto
- use App\Enums\CourseStatus
- use App\Enums\ContentAccessLevel

**Properties**:
- courseId
- includeEpisodes
- includeAuthor
- includeProgress
- checkAccess

**Methods**:
- validate()
- getIncludes()
- shouldCheckAccess()

**Do**:
- ✅ Validate course access permissions
- ✅ Include enrollment status
- ✅ Return localized content
- ✅ Track view analytics

**Don't**:
- ❌ Return restricted content
- ❌ Skip access validation
- ❌ Include unpublished content
- ❌ Forget progress tracking

### app/Queries/GetCourseListQuery.php
**Purpose**: Query DTO for course listing with filters
**Dependencies**:
- use App\Foundation\Core\BaseDto
- use App\Enums\CourseStatus
- use App\Enums\DifficultyLevel

**Properties**:
- page
- perPage
- categoryId
- difficulty
- accessLevel
- searchTerm
- sortBy
- sortOrder

**Methods**:
- validate()
- getFilters()
- getPagination()
- getSort()

**Do**:
- ✅ Implement efficient pagination
- ✅ Support multiple filters
- ✅ Include search functionality
- ✅ Cache popular queries

**Don't**:
- ❌ Allow unlimited page sizes
- ❌ Skip input validation
- ❌ Return sensitive course data
- ❌ Ignore performance implications

### app/Queries/GetPaymentHistoryQuery.php
**Purpose**: Query DTO for payment history retrieval
**Dependencies**:
- use App\Foundation\Core\BaseDto
- use App\Enums\PaymentStatus
- use App\Enums\TransactionType

**Properties**:
- userId
- startDate
- endDate
- status
- type
- page
- perPage

**Methods**:
- validate()
- getDateRange()
- getFilters()
- getPagination()

**Do**:
- ✅ Validate date ranges
- ✅ Implement proper pagination
- ✅ Filter by transaction types
- ✅ Include currency information

**Don't**:
- ❌ Return other users' payments
- ❌ Skip authorization checks
- ❌ Allow excessive date ranges
- ❌ Include sensitive payment details

### app/Queries/GetAffiliateStatsQuery.php
**Purpose**: Query DTO for affiliate statistics retrieval
**Dependencies**:
- use App\Foundation\Core\BaseDto
- use App\Enums\MetricPeriod

**Properties**:
- affiliateId
- period
- startDate
- endDate
- includeBreakdown

**Methods**:
- validate()
- getPeriod()
- getDateRange()
- includesBreakdown()

**Do**:
- ✅ Aggregate data efficiently
- ✅ Support multiple time periods
- ✅ Include conversion metrics
- ✅ Cache expensive calculations

**Don't**:
- ❌ Expose other affiliates' data
- ❌ Skip performance optimization
- ❌ Calculate in real-time always
- ❌ Include sensitive commission details

### app/Queries/GetUserProgressQuery.php
**Purpose**: Query DTO for user progress retrieval
**Dependencies**:
- use App\Foundation\Core\BaseDto
- use App\Enums\ContentType

**Properties**:
- userId
- courseId
- includeNotes
- includeTimeSpent
- includeCompletionRate

**Methods**:
- validate()
- getIncludes()
- getCourseFilter()

**Do**:
- ✅ Track detailed progress metrics
- ✅ Include time spent learning
- ✅ Support course-specific progress
- ✅ Update progress in real-time

**Don't**:
- ❌ Return other users' progress
- ❌ Skip privacy validation
- ❌ Include incomplete data
- ❌ Forget to update last accessed

---

## Command Handlers

### app/Handlers/Commands/CreateUserHandler.php
**Purpose**: Handles user creation command execution
**Dependencies**:
- use App\Commands\CreateUserCommand
- use App\Contracts\Repositories\UserRepositoryInterface
- use App\Services\User\AuthService
- use App\Events\UserRegistered

**Methods**:
- handle(CreateUserCommand $command)
- validateUniqueEmail(string $email)
- hashPassword(string $password)
- generateUserUuid()
- assignDefaultRole()

**Do**:
- ✅ Validate all input data
- ✅ Hash passwords securely
- ✅ Generate unique UUIDs
- ✅ Fire domain events
- ✅ Log user creation

**Don't**:
- ❌ Store plain text passwords
- ❌ Skip email uniqueness validation
- ❌ Forget to assign default roles
- ❌ Skip audit logging

### app/Handlers/Commands/UpdateUserHandler.php
**Purpose**: Handles user update command execution
**Dependencies**:
- use App\Commands\UpdateUserCommand
- use App\Contracts\Repositories\UserRepositoryInterface
- use App\Events\UserProfileUpdated

**Methods**:
- handle(UpdateUserCommand $command)
- validateOwnership(int $userId)
- updateUserData(array $data)
- clearUserCache(int $userId)

**Do**:
- ✅ Validate user ownership
- ✅ Update only allowed fields
- ✅ Clear relevant caches
- ✅ Fire update events

**Don't**:
- ❌ Allow unauthorized updates
- ❌ Skip data validation
- ❌ Update immutable fields
- ❌ Forget cache invalidation

### app/Handlers/Commands/DeleteUserHandler.php
**Purpose**: Handles user deletion command execution
**Dependencies**:
- use App\Commands\DeleteUserCommand
- use App\Contracts\Repositories\UserRepositoryInterface
- use App\Events\UserDeleted

**Methods**:
- handle(DeleteUserCommand $command)
- validateDeletion(int $userId)
- softDeleteUser(int $userId)
- cleanupUserData(int $userId)

**Do**:
- ✅ Use soft delete by default
- ✅ Validate deletion eligibility
- ✅ Clean up related data
- ✅ Log deletion reason

**Don't**:
- ❌ Hard delete without explicit request
- ❌ Delete users with active subscriptions
- ❌ Skip data cleanup
- ❌ Forget audit trail

### app/Handlers/Commands/CreateCourseHandler.php
**Purpose**: Handles course creation command execution
**Dependencies**:
- use App\Commands\CreateCourseCommand
- use App\Contracts\Repositories\CourseRepositoryInterface
- use App\Events\CourseCreated

**Methods**:
- handle(CreateCourseCommand $command)
- validateAuthorPermissions(int $authorId)
- generateCourseAlias(string $title)
- createCourseStructure(array $data)

**Do**:
- ✅ Validate author permissions
- ✅ Generate unique aliases
- ✅ Set proper defaults
- ✅ Initialize course structure

**Don't**:
- ❌ Allow unauthorized course creation
- ❌ Skip alias uniqueness check
- ❌ Create without category
- ❌ Forget initial SEO setup

### app/Handlers/Commands/ProcessPaymentHandler.php
**Purpose**: Handles payment processing command execution
**Dependencies**:
- use App\Commands\ProcessPaymentCommand
- use App\Services\Payment\PaymentService
- use App\Events\PaymentProcessed

**Methods**:
- handle(ProcessPaymentCommand $command)
- validatePaymentData(array $data)
- processWithProvider(string $provider, array $data)
- recordTransaction(array $result)

**Do**:
- ✅ Validate payment amounts
- ✅ Use appropriate payment providers
- ✅ Record all transactions
- ✅ Handle payment failures gracefully

**Don't**:
- ❌ Process without validation
- ❌ Skip fraud detection
- ❌ Store sensitive data
- ❌ Forget error handling

---

## Query Handlers

### app/Handlers/Queries/GetUserHandler.php
**Purpose**: Handles user retrieval query execution
**Dependencies**:
- use App\Queries\GetUserQuery
- use App\Contracts\Repositories\UserRepositoryInterface
- use App\Services\Cache\UserCacheService

**Methods**:
- handle(GetUserQuery $query)
- findUser(int $userId)
- includeRelations(array $includes)
- formatUserData(array $userData)

**Do**:
- ✅ Use caching for frequent queries
- ✅ Validate access permissions
- ✅ Include requested relations only
- ✅ Format response consistently

**Don't**:
- ❌ Return sensitive data
- ❌ Skip authorization checks
- ❌ Include unnecessary relations
- ❌ Forget cache strategies

### app/Handlers/Queries/GetCourseHandler.php
**Purpose**: Handles course retrieval query execution
**Dependencies**:
- use App\Queries\GetCourseQuery
- use App\Contracts\Repositories\CourseRepositoryInterface
- use App\Services\Course\CourseAccessService

**Methods**:
- handle(GetCourseQuery $query)
- findCourse(int $courseId)
- checkAccessPermissions(int $courseId, int $userId)
- includeEpisodes(bool $include)

**Do**:
- ✅ Validate course access
- ✅ Include user progress
- ✅ Return localized content
- ✅ Track analytics

**Don't**:
- ❌ Return restricted content
- ❌ Skip access validation
- ❌ Include unpublished episodes
- ❌ Forget view tracking

### app/Handlers/Queries/GetPaymentHistoryHandler.php
**Purpose**: Handles payment history query execution
**Dependencies**:
- use App\Queries\GetPaymentHistoryQuery
- use App\Contracts\Repositories\PaymentRepositoryInterface

**Methods**:
- handle(GetPaymentHistoryQuery $query)
- validateDateRange(DateTime $start, DateTime $end)
- buildFilters(array $filters)
- paginateResults(array $results, int $page, int $perPage)

**Do**:
- ✅ Validate user authorization
- ✅ Implement efficient pagination
- ✅ Filter by date ranges
- ✅ Include currency information

**Don't**:
- ❌ Return other users' data
- ❌ Allow unlimited queries
- ❌ Skip input validation
- ❌ Include sensitive details

---

## Best Practices

### Command Design
- Commands should be immutable DTOs
- Include all necessary data for operation
- Validate data in constructor
- Use proper type hints and enums
- Fire domain events after successful execution

### Query Design  
- Queries should specify exactly what data is needed
- Support pagination for list queries
- Implement caching strategies
- Validate access permissions
- Use database indexes effectively

### Handler Implementation
- Keep handlers focused on single responsibility
- Use dependency injection for services
- Implement proper error handling
- Log important operations
- Maintain transactional integrity

### Security Considerations
- Always validate user permissions
- Use parameterized queries
- Implement rate limiting
- Log security-relevant operations
- Validate all input data

### Performance Optimization
- Use caching for frequent queries
- Implement database indexes
- Avoid N+1 query problems
- Use pagination for large datasets
- Monitor query performance
