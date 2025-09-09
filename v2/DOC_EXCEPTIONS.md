# app/Exceptions/Handler.php

## Purpose
→ Global exception handler for the Laravel application
→ Manages error reporting, logging, and HTTP response formatting
→ Provides consistent error responses for web and API requests
→ Integrates with Laravel's error handling pipeline

## Dependencies
use Illuminate\Foundation\Exceptions\Handler as ExceptionHandler
use Illuminate\Http\Request
use Illuminate\Http\Response
use Illuminate\Http\JsonResponse
use Illuminate\Validation\ValidationException
use Illuminate\Auth\AuthenticationException
use Illuminate\Database\Eloquent\ModelNotFoundException
use Symfony\Component\HttpKernel\Exception\NotFoundHttpException
use Throwable
use App\Exceptions\ApiException
use App\Exceptions\CourseAccessException
use App\Exceptions\PaymentException
use App\DTOs\ApiResponse

## Methods
register()
report()
render()
renderable()
reportable()
renderApiException()
renderValidationException()
renderAuthenticationException()
renderNotFoundException()
convertToApiResponse()
shouldReturnJson()
prepareJsonResponse()
getErrorMessage()
getErrorCode()
logException()

## Do
✅ Always return consistent error formats for API endpoints
✅ Log sensitive errors securely without exposing details
✅ Use proper HTTP status codes for different exception types
✅ Implement rate limiting for error responses
✅ Provide helpful error messages for development environment

## Don't
❌ Don't expose sensitive information in production error messages
❌ Don't return stack traces in production API responses
❌ Don't ignore security-related exceptions
❌ Don't use generic error messages for validation failures
❌ Don't log user passwords or tokens in error contexts

# app/Exceptions/CourseAccessException.php

## Purpose
→ Custom exception for course access permission violations
→ Handles user level restrictions and premium content access
→ Provides specific error messages for different access scenarios
→ Integrates with course access control middleware

## Dependencies
use Exception
use Throwable
use App\Enums\ContentAccessLevel
use App\Enums\UserLevel
use App\Models\Course\DocuCourse
use App\Models\User

## Methods
__construct()
forInsufficientLevel()
forPremiumContentRequired()
forCourseNotFound()
forCourseNotPublished()
forUserNotEnrolled()
getMessage()
getErrorCode()
getRequiredLevel()
getCourse()
getUser()
toArray()

## Do
✅ Provide clear access requirement messages
✅ Include required user level in exception details
✅ Log course access attempts for analytics
✅ Use appropriate HTTP status codes (403 Forbidden)
✅ Suggest upgrade path for premium content

## Don't
❌ Don't expose course pricing in error messages
❌ Don't reveal course structure to unauthorized users
❌ Don't provide hints about bypassing access controls
❌ Don't log sensitive user payment information
❌ Don't use generic access denied messages

# app/Exceptions/PaymentException.php

## Purpose
→ Custom exception for payment processing errors
→ Handles Stripe and Binance payment gateway failures
→ Provides user-friendly payment error messages
→ Integrates with payment retry mechanisms

## Dependencies
use Exception
use Throwable
use App\Enums\PaymentStatus
use App\Enums\PaymentProvider
use App\Models\Payment\Transaction
use App\DTOs\Payment\PaymentData

## Methods
__construct()
forInsufficientFunds()
forPaymentDeclined()
forInvalidPaymentMethod()
forGatewayTimeout()
forCurrencyNotSupported()
forPaymentAlreadyProcessed()
getMessage()
getErrorCode()
getPaymentProvider()
getTransactionId()
shouldRetry()
getRetryDelay()
toArray()

## Do
✅ Provide clear payment failure reasons
✅ Include transaction ID for support purposes
✅ Implement retry logic for transient failures
✅ Log payment errors securely for debugging
✅ Use appropriate HTTP status codes (402 Payment Required)

## Don't
❌ Don't expose payment gateway internal errors
❌ Don't log complete payment card details
❌ Don't retry payments automatically without user consent
❌ Don't reveal payment processing logic
❌ Don't ignore payment security exceptions

# app/Exceptions/FileUploadException.php

## Purpose
→ Custom exception for file upload validation and processing errors
→ Handles course content and media upload failures
→ Provides specific error messages for different file issues
→ Integrates with Spatie Media Library exceptions

## Dependencies
use Exception
use Throwable
use App\Enums\FileType
use App\Rules\ValidCourseFile
use App\Rules\FileSizeLimit
use Illuminate\Http\UploadedFile

## Methods
__construct()
forFileTooLarge()
forInvalidFileType()
forCorruptedFile()
forVirusDetected()
forStorageFailure()
forInsufficientStorage()
getMessage()
getErrorCode()
getFile()
getMaxSize()
getAllowedTypes()
toArray()

## Do
✅ Validate file types before processing
✅ Check file size limits early in upload process
✅ Scan files for security threats
✅ Provide clear file requirement messages
✅ Clean up temporary files on failure

## Don't
❌ Don't process files without validation
❌ Don't expose server file system paths
❌ Don't allow unlimited file uploads
❌ Don't skip virus scanning for any file type
❌ Don't ignore file storage quota limits

# app/Exceptions/ValidationException.php

## Purpose
→ Enhanced validation exception for custom business rules
→ Extends Laravel's base ValidationException with domain context
→ Provides structured error messages for API responses
→ Integrates with DTO validation patterns

## Dependencies
use Illuminate\Validation\ValidationException as BaseValidationException
use Illuminate\Support\MessageBag
use App\Foundation\Core\BaseDto
use App\DTOs\ApiResponse

## Methods
__construct()
withErrors()
errorBag()
redirectTo()
status()
getResponse()
withMessages()
validator()
toArray()
toJsonResponse()
getErrorBag()

## Do
✅ Provide field-specific error messages
✅ Use consistent error message formats
✅ Include validation context in responses
✅ Support multiple languages for error messages
✅ Return structured JSON for API endpoints

## Don't
❌ Don't return generic validation messages
❌ Don't expose internal validation rule names
❌ Don't ignore nested object validation errors
❌ Don't use technical jargon in user-facing messages
❌ Don't omit required field information

# app/Exceptions/ApiException.php

## Purpose
→ Base exception class for all API-related errors
→ Provides consistent API error response formatting
→ Handles HTTP status codes and error metadata
→ Integrates with API versioning and documentation

## Dependencies
use Exception
use Throwable
use Illuminate\Http\JsonResponse
use App\DTOs\ApiResponse
use App\Enums\LogLevel

## Methods
__construct()
render()
report()
getHttpStatusCode()
getErrorCode()
getErrorType()
getMetadata()
toArray()
toJsonResponse()
withMetadata()
shouldReport()
getContext()

## Do
✅ Use standard HTTP status codes
✅ Provide consistent error response structure
✅ Include request correlation IDs
✅ Log API errors with proper context
✅ Support error code documentation

## Don't
❌ Don't expose internal system details
❌ Don't use non-standard error formats
❌ Don't ignore API versioning in error responses
❌ Don't omit error tracking metadata
❌ Don't return HTML content for API errors

# app/Exceptions/AuthenticationException.php

## Purpose
→ Custom authentication exception for enhanced security handling
→ Manages authentication failures and token issues
→ Provides specific error messages for different auth scenarios
→ Integrates with Laravel Sanctum and Breeze authentication

## Dependencies
use Illuminate\Auth\AuthenticationException as BaseAuthenticationException
use App\Enums\AuthProvider
use App\Enums\TokenType
use App\Models\User

## Methods
__construct()
forInvalidCredentials()
forExpiredToken()
forInvalidToken()
forTwoFactorRequired()
forAccountLocked()
forEmailNotVerified()
guards()
redirectTo()
getMessage()
getErrorCode()
getAuthProvider()
shouldLockAccount()
toArray()

## Do
✅ Implement account lockout after failed attempts
✅ Log suspicious authentication activities
✅ Use rate limiting for login attempts
✅ Provide clear authentication requirements
✅ Support multiple authentication methods

## Don't
❌ Don't reveal which part of credentials is wrong
❌ Don't expose user account existence in error messages
❌ Don't allow unlimited authentication attempts
❌ Don't log successful authentication details excessively
❌ Don't ignore two-factor authentication requirements

# app/Exceptions/NotFoundException.php

## Purpose
→ Custom not found exception for specific resource types
→ Provides context-aware 404 error messages
→ Handles UUID-based resource lookups
→ Integrates with Laravel's model route binding

## Dependencies
use Symfony\Component\HttpKernel\Exception\NotFoundHttpException
use App\Models\User
use App\Models\Course\DocuCourse
use App\Models\Course\Episode

## Methods
__construct()
forResource()
forUser()
forCourse()
forEpisode()
forPayment()
getMessage()
getErrorCode()
getResourceType()
getResourceId()
toArray()

## Do
✅ Use specific error messages for different resource types
✅ Include helpful suggestions for similar resources
✅ Log 404 errors for analytics
✅ Use consistent resource identifier formats
✅ Provide search suggestions when appropriate

## Don't
❌ Don't reveal internal database structure
❌ Don't expose sensitive resource information
❌ Don't provide hints about existing resources
❌ Don't ignore potential security scanning attempts
❌ Don't use generic "not found" messages