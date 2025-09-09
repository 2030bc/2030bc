# DOC_MIDDLEWARE.md

## Overview
The Middleware component provides the HTTP pipeline for the Laravel docu-course platform. This comprehensive middleware stack handles authentication, authorization, localization, security, tracking, rate limiting, API versioning, CORS, and various cross-cutting concerns.

---

## Authentication Middleware

### app/Http/Middleware/Authenticate.php
**Purpose**: Handles user authentication and redirects unauthenticated users
**Dependencies**:
- use Illuminate\Auth\Middleware\Authenticate as Middleware
- use Illuminate\Http\Request

**Methods**:
- redirectTo(Request $request)
- unauthenticated($request, array $guards)

**Do**:
- ✅ Redirect to appropriate login page
- ✅ Preserve intended URL
- ✅ Handle API vs web requests
- ✅ Support multiple guards
- ✅ Provide clear error messages

**Don't**:
- ❌ Expose sensitive authentication details
- ❌ Skip guard validation
- ❌ Ignore redirect loops
- ❌ Hard-code redirect URLs

### app/Http/Middleware/RedirectIfAuthenticated.php
**Purpose**: Redirects authenticated users away from guest-only pages
**Dependencies**:
- use App\Providers\RouteServiceProvider
- use Illuminate\Http\Request
- use Illuminate\Support\Facades\Auth

**Methods**:
- handle(Request $request, Closure $next, ...$guards)

**Do**:
- ✅ Check multiple guards
- ✅ Redirect to appropriate dashboard
- ✅ Handle role-based redirects
- ✅ Support different user types

**Don't**:
- ❌ Allow authenticated access to guest pages
- ❌ Create redirect loops
- ❌ Ignore user roles in redirection

### app/Http/Middleware/EnsureEmailIsVerified.php
**Purpose**: Ensures user email is verified before accessing protected content
**Dependencies**:
- use Illuminate\Auth\Middleware\EnsureEmailIsVerified as Middleware

**Methods**:
- handle(Request $request, Closure $next)

**Do**:
- ✅ Check email verification status
- ✅ Redirect to verification page
- ✅ Allow verification routes
- ✅ Handle API requests appropriately

**Don't**:
- ❌ Allow unverified access to sensitive areas
- ❌ Block verification endpoints
- ❌ Skip verification for admin users

### app/Http/Middleware/RequireTwoFactor.php
**Purpose**: Enforces two-factor authentication for sensitive operations
**Dependencies**:
- use Illuminate\Http\Request
- use App\Services\User\AuthService
- use App\Enums\TwoFactorMethod

**Methods**:
- handle(Request $request, Closure $next)
- requiresTwoFactor(Request $request)
- redirectToTwoFactor()

**Do**:
- ✅ Check 2FA requirements
- ✅ Support multiple 2FA methods
- ✅ Handle emergency codes
- ✅ Log 2FA attempts

**Don't**:
- ❌ Skip 2FA for admin accounts
- ❌ Allow bypass mechanisms
- ❌ Store 2FA codes insecurely

### app/Http/Middleware/ValidateApiToken.php
**Purpose**: Validates API tokens for authenticated API requests
**Dependencies**:
- use Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful
- use App\Services\Api\ApiKeyService

**Methods**:
- handle(Request $request, Closure $next)
- validateApiKey(string $key)
- checkRateLimit(string $key)

**Do**:
- ✅ Validate API key format
- ✅ Check key expiration
- ✅ Implement rate limiting
- ✅ Log API usage

**Don't**:
- ❌ Accept expired tokens
- ❌ Skip rate limiting
- ❌ Expose token details in errors

---

## Authorization Middleware

### app/Http/Middleware/CheckPermission.php
**Purpose**: Validates user permissions using Spatie Permission package
**Dependencies**:
- use Spatie\Permission\Middleware\PermissionMiddleware
- use App\Enums\PermissionType

**Methods**:
- handle(Request $request, Closure $next, $permission, $guard = null)

**Do**:
- ✅ Check specific permissions
- ✅ Support multiple guards
- ✅ Handle permission inheritance
- ✅ Log authorization attempts

**Don't**:
- ❌ Bypass permission checks
- ❌ Hard-code permission names
- ❌ Skip audit logging

### app/Http/Middleware/CheckRole.php
**Purpose**: Validates user roles using Spatie Permission package
**Dependencies**:
- use Spatie\Permission\Middleware\RoleMiddleware
- use App\Enums\RoleType

**Methods**:
- handle(Request $request, Closure $next, $role, $guard = null)

**Do**:
- ✅ Check user roles
- ✅ Support role hierarchies
- ✅ Handle multiple roles
- ✅ Log role checks

**Don't**:
- ❌ Allow role escalation
- ❌ Skip role validation
- ❌ Hard-code role names

### app/Http/Middleware/CheckUserLevel.php
**Purpose**: Validates user level requirements for content access
**Dependencies**:
- use App\Enums\UserLevel
- use App\Models\User

**Methods**:
- handle(Request $request, Closure $next, int $requiredLevel)
- checkUserLevel(User $user, int $requiredLevel)

**Do**:
- ✅ Check user progression level
- ✅ Handle level upgrades
- ✅ Provide upgrade suggestions
- ✅ Track level-based access

**Don't**:
- ❌ Allow level bypass
- ❌ Ignore level requirements
- ❌ Skip level validation

### app/Http/Middleware/ValidateCourseAccess.php
**Purpose**: Validates user access to specific course content
**Dependencies**:
- use App\Services\Course\CourseAccessService
- use App\Enums\ContentAccessLevel

**Methods**:
- handle(Request $request, Closure $next)
- checkCourseAccess(int $courseId, int $userId)
- validateEnrollment(int $courseId, int $userId)

**Do**:
- ✅ Check course enrollment
- ✅ Validate payment status
- ✅ Handle access levels
- ✅ Track access attempts

**Don't**:
- ❌ Allow unauthorized access
- ❌ Skip payment validation
- ❌ Bypass enrollment checks

### app/Http/Middleware/ValidateOwnership.php
**Purpose**: Validates resource ownership for CRUD operations
**Dependencies**:
- use Illuminate\Database\Eloquent\Model

**Methods**:
- handle(Request $request, Closure $next, string $model)
- checkOwnership(Model $resource, int $userId)

**Do**:
- ✅ Validate resource ownership
- ✅ Support different ownership models
- ✅ Handle shared resources
- ✅ Log ownership violations

**Don't**:
- ❌ Allow unauthorized modifications
- ❌ Skip ownership validation
- ❌ Expose other users' data

---

## Localization Middleware

### app/Http/Middleware/SetLocale.php
**Purpose**: Sets application locale based on user preferences or request
**Dependencies**:
- use Illuminate\Support\Facades\App
- use Illuminate\Support\Facades\Session
- use App\Models\System\Language

**Methods**:
- handle(Request $request, Closure $next)
- determineLocale(Request $request)
- setAppLocale(string $locale)

**Do**:
- ✅ Check user preferences
- ✅ Fall back to browser language
- ✅ Validate available locales
- ✅ Cache locale decisions

**Don't**:
- ❌ Use unsupported locales
- ❌ Ignore user preferences
- ❌ Skip locale validation

### app/Http/Middleware/HandleRTLLanguage.php
**Purpose**: Handles Right-To-Left language settings and CSS adjustments
**Dependencies**:
- use App\Enums\TextDirection
- use App\Enums\LanguageDirection

**Methods**:
- handle(Request $request, Closure $next)
- isRTLLanguage(string $locale)
- setRTLAttributes()

**Do**:
- ✅ Detect RTL languages
- ✅ Apply RTL CSS classes
- ✅ Adjust layout direction
- ✅ Handle mixed content

**Don't**:
- ❌ Hard-code RTL languages
- ❌ Skip CSS adjustments
- ❌ Ignore text direction

### app/Http/Middleware/LocalizeRoutes.php
**Purpose**: Handles localized routing and URL generation
**Dependencies**:
- use Mcamara\LaravelLocalization\Middleware\LaravelLocalizationRoutes

**Methods**:
- handle(Request $request, Closure $next)

**Do**:
- ✅ Generate localized URLs
- ✅ Handle route redirects
- ✅ Maintain locale in URLs
- ✅ Support locale switching

**Don't**:
- ❌ Break URL structure
- ❌ Lose locale context
- ❌ Create duplicate content

---

## Security Middleware

### app/Http/Middleware/SecurityHeaders.php
**Purpose**: Adds security headers to HTTP responses
**Dependencies**:
- use Illuminate\Http\Response

**Methods**:
- handle(Request $request, Closure $next)
- addSecurityHeaders(Response $response)

**Security Headers**:
- X-Content-Type-Options: nosniff
- X-Frame-Options: DENY
- X-XSS-Protection: 1; mode=block
- Strict-Transport-Security: max-age=31536000
- Referrer-Policy: strict-origin-when-cross-origin

**Do**:
- ✅ Add comprehensive security headers
- ✅ Configure CSP policies
- ✅ Enable HSTS
- ✅ Prevent clickjacking

**Don't**:
- ❌ Skip security headers
- ❌ Use weak CSP policies
- ❌ Allow unsafe inline scripts

### app/Http/Middleware/RateLimitMiddleware.php
**Purpose**: Implements rate limiting for API and web requests
**Dependencies**:
- use Illuminate\Routing\Middleware\ThrottleRequests
- use App\Services\Api\ApiRateLimitService

**Methods**:
- handle(Request $request, Closure $next, $maxAttempts = 60, $decayMinutes = 1)
- resolveRequestSignature(Request $request)

**Rate Limits**:
- API: 100 requests per minute
- Login: 5 attempts per minute
- Registration: 3 attempts per minute
- Password reset: 2 attempts per minute

**Do**:
- ✅ Implement different limits for different endpoints
- ✅ Use user-specific rate limiting
- ✅ Provide clear error messages
- ✅ Log rate limit violations

**Don't**:
- ❌ Use same limits for all endpoints
- ❌ Skip rate limiting for admin users
- ❌ Ignore IP-based limiting

### app/Http/Middleware/ValidateSignature.php
**Purpose**: Validates request signatures for secure URL generation
**Dependencies**:
- use Illuminate\Routing\Middleware\ValidateSignature as Middleware

**Methods**:
- handle(Request $request, Closure $next)

**Do**:
- ✅ Validate URL signatures
- ✅ Check signature expiry
- ✅ Handle signature failures
- ✅ Log invalid signatures

**Don't**:
- ❌ Allow unsigned URLs for sensitive operations
- ❌ Skip signature validation
- ❌ Use weak signature algorithms

### app/Http/Middleware/PreventBruteForce.php
**Purpose**: Prevents brute force attacks on authentication endpoints
**Dependencies**:
- use Illuminate\Support\Facades\RateLimiter
- use App\Services\Security\BruteForceService

**Methods**:
- handle(Request $request, Closure $next)
- checkBruteForceAttempts(Request $request)
- recordFailedAttempt(Request $request)

**Do**:
- ✅ Track failed login attempts
- ✅ Implement progressive delays
- ✅ Block suspicious IPs
- ✅ Send security alerts

**Don't**:
- ❌ Allow unlimited login attempts
- ❌ Skip IP tracking
- ❌ Ignore attack patterns

### app/Http/Middleware/LogSuspiciousActivity.php
**Purpose**: Logs and monitors suspicious user activity
**Dependencies**:
- use App\Services\Security\SecurityMonitorService
- use App\Enums\ActivityType

**Methods**:
- handle(Request $request, Closure $next)
- detectSuspiciousActivity(Request $request)
- logSecurityEvent(array $data)

**Suspicious Indicators**:
- Multiple failed login attempts
- Unusual access patterns
- Privilege escalation attempts
- SQL injection attempts
- XSS attempts

**Do**:
- ✅ Monitor access patterns
- ✅ Detect attack attempts
- ✅ Log security events
- ✅ Alert administrators

**Don't**:
- ❌ Ignore security warnings
- ❌ Skip activity logging
- ❌ Allow unchecked access

---

## Tracking Middleware

### app/Http/Middleware/TrackUserActivity.php
**Purpose**: Tracks user activity and page visits for analytics
**Dependencies**:
- use App\Services\Analytics\AnalyticsService
- use App\Models\Analytics\UserSession

**Methods**:
- handle(Request $request, Closure $next)
- trackPageView(Request $request)
- updateUserSession(int $userId)

**Do**:
- ✅ Track page views
- ✅ Update session data
- ✅ Record user paths
- ✅ Respect privacy settings

**Don't**:
- ❌ Track sensitive pages
- ❌ Store personal data unnecessarily
- ❌ Ignore privacy preferences

### app/Http/Middleware/TrackUserProgress.php
**Purpose**: Tracks user progress through course content
**Dependencies**:
- use App\Services\User\ProgressService
- use App\Models\User\UserProgress

**Methods**:
- handle(Request $request, Closure $next)
- trackEpisodeProgress(int $episodeId, int $userId)
- updateLearningTime(int $userId, int $timeSpent)

**Do**:
- ✅ Track episode completions
- ✅ Record time spent
- ✅ Update progress metrics
- ✅ Calculate completion rates

**Don't**:
- ❌ Track outside course content
- ❌ Skip progress updates
- ❌ Ignore user preferences

### app/Http/Middleware/LogApiRequests.php
**Purpose**: Logs API requests for monitoring and analytics
**Dependencies**:
- use App\Services\Api\ApiLoggingService
- use App\Models\ApiKey

**Methods**:
- handle(Request $request, Closure $next)
- logApiRequest(Request $request, Response $response)
- trackApiUsage(string $apiKey)

**Do**:
- ✅ Log all API requests
- ✅ Track API usage metrics
- ✅ Monitor error rates
- ✅ Record response times

**Don't**:
- ❌ Log sensitive data
- ❌ Skip error logging
- ❌ Ignore rate limiting data

### app/Http/Middleware/TrackAffiliateClicks.php
**Purpose**: Tracks affiliate clicks and conversions
**Dependencies**:
- use App\Services\Affiliate\TrackingService
- use App\Models\Affiliate\AffiliateClick

**Methods**:
- handle(Request $request, Closure $next)
- trackAffiliateClick(Request $request)
- recordReferralData(array $data)

**Do**:
- ✅ Track referral sources
- ✅ Record click data
- ✅ Handle conversion attribution
- ✅ Respect privacy laws

**Don't**:
- ❌ Track without consent
- ❌ Store unnecessary personal data
- ❌ Ignore attribution windows

### app/Http/Middleware/ValidateContentAccess.php
**Purpose**: Validates access to premium and restricted content
**Dependencies**:
- use App\Services\Course\ContentAccessService
- use App\Enums\ContentAccessLevel

**Methods**:
- handle(Request $request, Closure $next)
- validateAccess(int $contentId, int $userId)
- checkSubscriptionStatus(int $userId)

**Do**:
- ✅ Check subscription status
- ✅ Validate payment history
- ✅ Handle access levels
- ✅ Track access violations

**Don't**:
- ❌ Allow unauthorized access
- ❌ Skip payment validation
- ❌ Ignore subscription expiry

---

## Performance Middleware

### app/Http/Middleware/CacheResponse.php
**Purpose**: Implements HTTP response caching for performance
**Dependencies**:
- use Illuminate\Http\Middleware\SetCacheHeaders
- use App\Services\Cache\ResponseCacheService

**Methods**:
- handle(Request $request, Closure $next)
- shouldCache(Request $request, Response $response)
- setCacheHeaders(Response $response)

**Do**:
- ✅ Cache static content
- ✅ Set appropriate cache headers
- ✅ Handle cache invalidation
- ✅ Support conditional requests

**Don't**:
- ❌ Cache dynamic user content
- ❌ Skip cache validation
- ❌ Use excessive cache times

### app/Http/Middleware/CompressResponse.php
**Purpose**: Compresses HTTP responses for better performance
**Dependencies**:
- use Illuminate\Http\Response

**Methods**:
- handle(Request $request, Closure $next)
- compressResponse(Response $response)
- supportsCompression(Request $request)

**Do**:
- ✅ Check client support
- ✅ Compress text responses
- ✅ Skip binary content
- ✅ Set compression headers

**Don't**:
- ❌ Compress already compressed content
- ❌ Skip client capability checks
- ❌ Use excessive compression

---

## API Middleware

### app/Http/Middleware/ApiVersioning.php
**Purpose**: Handles API versioning and compatibility
**Dependencies**:
- use App\Services\Api\VersionService

**Methods**:
- handle(Request $request, Closure $next)
- parseVersion(Request $request)
- validateVersion(string $version)

**Do**:
- ✅ Parse version from headers
- ✅ Validate version support
- ✅ Handle deprecated versions
- ✅ Provide migration guidance

**Don't**:
- ❌ Support unlimited versions
- ❌ Skip version validation
- ❌ Break backward compatibility suddenly

### app/Http/Middleware/ValidateJsonApi.php
**Purpose**: Validates JSON API specification compliance
**Dependencies**:
- use Illuminate\Http\Request

**Methods**:
- handle(Request $request, Closure $next)
- validateJsonApiHeaders(Request $request)
- validateJsonApiStructure(array $data)

**Do**:
- ✅ Validate JSON API format
- ✅ Check required headers
- ✅ Enforce specification compliance
- ✅ Provide clear error messages

**Don't**:
- ❌ Allow non-compliant requests
- ❌ Skip structure validation
- ❌ Ignore specification requirements

### app/Http/Middleware/CorsMiddleware.php
**Purpose**: Handles Cross-Origin Resource Sharing (CORS)
**Dependencies**:
- use Fruitcake\Cors\HandleCors

**Methods**:
- handle(Request $request, Closure $next)

**CORS Configuration**:
- Allowed origins: Configured domains
- Allowed methods: GET, POST, PUT, DELETE, OPTIONS
- Allowed headers: Content-Type, Authorization, X-Requested-With
- Max age: 86400 seconds

**Do**:
- ✅ Configure allowed origins
- ✅ Handle preflight requests
- ✅ Set appropriate headers
- ✅ Validate origin domains

**Don't**:
- ❌ Allow all origins in production
- ❌ Skip preflight handling
- ❌ Ignore security implications

### app/Http/Middleware/ForceHttps.php
**Purpose**: Forces HTTPS connections for security
**Dependencies**:
- use Illuminate\Http\Request

**Methods**:
- handle(Request $request, Closure $next)
- shouldForceHttps(Request $request)

**Do**:
- ✅ Redirect HTTP to HTTPS
- ✅ Check environment settings
- ✅ Handle load balancer headers
- ✅ Preserve query parameters

**Don't**:
- ❌ Force HTTPS in development
- ❌ Create redirect loops
- ❌ Ignore proxy headers

### app/Http/Middleware/ContentSecurityPolicy.php
**Purpose**: Implements Content Security Policy headers
**Dependencies**:
- use Illuminate\Http\Response

**Methods**:
- handle(Request $request, Closure $next)
- buildCSPHeader()
- addCSPDirectives(array $directives)

**CSP Directives**:
- default-src: 'self'
- script-src: 'self' 'unsafe-inline'
- style-src: 'self' 'unsafe-inline'
- img-src: 'self' data: https:
- font-src: 'self' https:
- connect-src: 'self' https:

**Do**:
- ✅ Implement strict CSP policies
- ✅ Allow necessary resources
- ✅ Report CSP violations
- ✅ Test policies thoroughly

**Don't**:
- ❌ Use 'unsafe-eval' unnecessarily
- ❌ Allow all sources
- ❌ Skip CSP testing
- ❌ Ignore violation reports

---

## Middleware Contracts

### 2030b/app/Http/Middleware/Contracts/MiddlewareInterface.php

**Purpose**: Defines the base contract for all custom middleware classes in the application.

**Dependencies**:
```php
use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;
```

**Methods**:
- handle(Request $request, Closure $next): Response
- shouldProcess(Request $request): bool
- getPriority(): int
- getName(): string

**Do**:
- Implement consistent middleware interface
- Define middleware priority
- Handle requests efficiently
- Provide meaningful middleware names

**Don't**:
- Don't create blocking middleware
- Don't ignore middleware priority
- Don't implement heavy processing in middleware
- Don't bypass middleware contracts

### 2030b/app/Http/Middleware/Contracts/AuthenticatableMiddleware.php

**Purpose**: Contract for middleware that requires user authentication and handles authentication-related concerns.

**Dependencies**:
```php
use Illuminate\Auth\Middleware\Authenticate;
use Illuminate\Http\Request;
use App\Models\User;
```

**Methods**:
- authenticate(Request $request): User
- redirectTo(Request $request): string
- shouldAuthenticate(Request $request): bool
- getAuthGuard(): string
- handleUnauthenticated(Request $request): Response

**Do**:
- Implement consistent authentication flow
- Handle unauthenticated users gracefully
- Use appropriate auth guards
- Redirect to proper login pages

**Don't**:
- Don't bypass authentication checks
- Don't ignore auth guard configuration
- Don't create security vulnerabilities
- Don't expose authentication details

### 2030b/app/Http/Middleware/Contracts/AuthorizableMiddleware.php

**Purpose**: Contract for middleware that performs authorization checks and permission validation.

**Dependencies**:
```php
use Illuminate\Http\Request;
use App\Models\User;
use Spatie\Permission\Models\Permission;
use Spatie\Permission\Models\Role;
```

**Methods**:
- authorize(Request $request, User $user): bool
- getRequiredPermissions(): array
- getRequiredRoles(): array
- handleUnauthorized(Request $request): Response
- shouldAuthorize(Request $request): bool

**Do**:
- Check user permissions and roles
- Handle unauthorized access gracefully
- Use Spatie Permission package consistently
- Implement fine-grained authorization

**Don't**:
- Don't bypass authorization checks
- Don't ignore role-based access control
- Don't create authorization vulnerabilities
- Don't expose permission details

### 2030b/app/Http/Middleware/Contracts/ValidatableMiddleware.php

**Purpose**: Contract for middleware that validates request data and ensures data integrity.

**Dependencies**:
```php
use Illuminate\Http\Request;
use Illuminate\Validation\Validator;
use App\Http\Requests\FormRequest;
```

**Methods**:
- validate(Request $request): bool
- getValidationRules(): array
- getValidationMessages(): array
- handleValidationFailure(Request $request, Validator $validator): Response
- shouldValidate(Request $request): bool

**Do**:
- Implement consistent validation logic
- Provide clear validation messages
- Handle validation failures gracefully
- Use appropriate validation rules

**Don't**:
- Don't skip request validation
- Don't ignore validation errors
- Don't bypass data integrity checks
- Don't expose validation logic

### 2030b/app/Http/Middleware/Contracts/RateLimitableMiddleware.php

**Purpose**: Contract for middleware that implements rate limiting and throttling functionality.

**Dependencies**:
```php
use Illuminate\Http\Request;
use Illuminate\Cache\RateLimiter;
use Illuminate\Support\Facades\RateLimiter as RateLimiterFacade;
```

**Methods**:
- checkRateLimit(Request $request): bool
- getRateLimitKey(Request $request): string
- getMaxAttempts(): int
- getDecayMinutes(): int
- handleRateLimitExceeded(Request $request): Response
- shouldRateLimit(Request $request): bool

**Do**:
- Implement appropriate rate limiting
- Use unique rate limit keys
- Handle rate limit exceeded gracefully
- Configure reasonable limits

**Don't**:
- Don't bypass rate limiting
- Don't use weak rate limit keys
- Don't ignore rate limit configuration
- Don't create denial of service risks

---

## Best Practices

### Middleware Design
- Keep middleware focused on single concerns
- Use dependency injection for services
- Handle errors gracefully
- Provide clear error messages
- Log important operations

### Performance Considerations
- Avoid expensive operations in middleware
- Use caching where appropriate
- Implement early returns for invalid requests
- Monitor middleware performance
- Use queued processing for heavy operations

### Security Best Practices
- Validate all input data
- Implement proper error handling
- Use secure defaults
- Log security events
- Follow principle of least privilege

### Testing Strategies
- Test middleware in isolation
- Mock external dependencies
- Test error conditions
- Verify security behaviors
- Test performance implications