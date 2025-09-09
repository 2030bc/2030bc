# DOC_REQUESTS.md

## 2030b/app/Http/Requests/Auth/LoginRequest.php

**Purpose**: Validates user login credentials and handles authentication request data.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\RateLimiter;
use Illuminate\Support\Str;
use Illuminate\Validation\ValidationException;
use App\Rules\ValidCredentials;
```

**Methods**:
- authorize(): bool
- rules(): array
- authenticate(): void
- ensureIsNotRateLimited(): void
- throttleKey(): string
- messages(): array

**Do**:
- Rate limit login attempts
- Validate credentials securely
- Handle failed authentication gracefully
- Use secure authentication methods

**Don't**:
- Don't expose authentication details
- Don't ignore rate limiting
- Don't bypass credential validation
- Don't create brute force vulnerabilities

## 2030b/app/Http/Requests/Auth/RegisterRequest.php

**Purpose**: Validates user registration data including email uniqueness and password strength requirements.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Validation\Rules\Password;
use App\Rules\UniqueEmail;
use App\Rules\ValidUsername;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- prepareForValidation(): void
- passedValidation(): void

**Do**:
- Validate email uniqueness
- Enforce strong password requirements
- Sanitize input data
- Provide clear validation messages

**Don't**:
- Don't allow duplicate registrations
- Don't accept weak passwords
- Don't bypass registration validation
- Don't expose user data

## 2030b/app/Http/Requests/Auth/ForgotPasswordRequest.php

**Purpose**: Validates forgot password requests and handles password reset token generation.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Validation\Rule;
use App\Rules\ValidEmail;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getEmail(): string

**Do**:
- Validate email format
- Check email existence
- Rate limit reset requests
- Handle invalid emails gracefully

**Don't**:
- Don't expose user existence
- Don't ignore rate limiting
- Don't bypass email validation
- Don't create enumeration attacks

## 2030b/app/Http/Requests/Auth/ResetPasswordRequest.php

**Purpose**: Validates password reset requests with tokens and new password requirements.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Validation\Rules\Password;
use App\Rules\ValidResetToken;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getCredentials(): array

**Do**:
- Validate reset tokens
- Enforce password strength
- Verify token expiration
- Handle invalid tokens gracefully

**Don't**:
- Don't accept invalid tokens
- Don't bypass token validation
- Don't allow weak passwords
- Don't ignore token expiration

## 2030b/app/Http/Requests/Auth/UpdatePasswordRequest.php

**Purpose**: Validates password update requests for authenticated users with current password verification.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Support\Facades\Hash;
use Illuminate\Validation\Rules\Password;
use App\Rules\CurrentPassword;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- validateCurrentPassword(): bool

**Do**:
- Verify current password
- Enforce new password strength
- Require authentication
- Log password changes

**Don't**:
- Don't bypass current password verification
- Don't allow weak new passwords
- Don't ignore password change logging
- Don't expose password validation details

## 2030b/app/Http/Requests/Auth/TwoFactorRequest.php

**Purpose**: Validates two-factor authentication codes and handles 2FA verification requests.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Rules\ValidTwoFactorCode;
use PragmaRX\Google2FA\Google2FA;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- verifyCode(): bool
- getBackupCodes(): array

**Do**:
- Validate 2FA codes
- Support backup codes
- Rate limit 2FA attempts
- Handle invalid codes gracefully

**Don't**:
- Don't bypass 2FA validation
- Don't ignore backup codes
- Don't create timing attacks
- Don't expose 2FA secrets

## User Management Requests

## 2030b/app/Http/Requests/User/UpdateProfileRequest.php

**Purpose**: Validates user profile update data including personal information and preferences.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Validation\Rule;
use App\Rules\ValidTimezone;
use App\Rules\ValidLanguage;
use App\Enums\Gender;
use App\Enums\TextDirection;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- prepareForValidation(): void

**Do**:
- Validate profile data formats
- Check enum value constraints
- Sanitize user inputs
- Enforce business rules

**Don't**:
- Don't bypass profile validation
- Don't allow invalid enum values
- Don't ignore data sanitization
- Don't expose sensitive profile data

## 2030b/app/Http/Requests/User/CreateUserRequest.php

**Purpose**: Validates administrative user creation with role assignments and initial profile data.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Validation\Rules\Password;
use App\Rules\ValidRole;
use App\Rules\UniqueEmail;
use Spatie\Permission\Models\Role;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getRoles(): Collection

**Do**:
- Validate admin permissions
- Check role assignments
- Enforce user creation rules
- Validate email uniqueness

**Don't**:
- Don't allow unauthorized user creation
- Don't bypass role validation
- Don't ignore permission checks
- Don't create duplicate users

## 2030b/app/Http/Requests/User/UpdateUserRequest.php

**Purpose**: Validates administrative user updates including role changes and profile modifications.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Validation\Rule;
use App\Models\User;
use App\Rules\ValidRole;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getUser(): User

**Do**:
- Validate admin permissions
- Check user existence
- Validate role changes
- Enforce update constraints

**Don't**:
- Don't allow unauthorized updates
- Don't bypass permission checks
- Don't ignore role constraints
- Don't update non-existent users

## 2030b/app/Http/Requests/User/DeleteUserRequest.php

**Purpose**: Validates user deletion requests with appropriate authorization and safety checks.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Models\User;
use App\Rules\CanDeleteUser;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- shouldSoftDelete(): bool

**Do**:
- Verify deletion permissions
- Check deletion constraints
- Use soft deletes when appropriate
- Validate user existence

**Don't**:
- Don't allow unauthorized deletions
- Don't delete without validation
- Don't ignore deletion constraints
- Don't bypass soft delete logic

## Course Management Requests

## 2030b/app/Http/Requests/Course/CreateCourseRequest.php

**Purpose**: Validates course creation data including content, pricing, and access level configuration.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Rules\ValidCategory;
use App\Rules\ValidLanguage;
use App\Rules\ValidCurrency;
use App\Enums\CourseStatus;
use App\Enums\ContentAccessLevel;
use App\Enums\DifficultyLevel;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- prepareForValidation(): void

**Do**:
- Validate course authorship permissions
- Check category and language validity
- Validate pricing information
- Enforce content guidelines

**Don't**:
- Don't allow unauthorized course creation
- Don't bypass content validation
- Don't ignore pricing constraints
- Don't create invalid course structures

## 2030b/app/Http/Requests/Course/UpdateCourseRequest.php

**Purpose**: Validates course update requests including content modifications and status changes.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Models\DocuCourse;
use App\Rules\ValidCategory;
use App\Enums\CourseStatus;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getCourse(): DocuCourse

**Do**:
- Verify course ownership
- Validate update permissions
- Check status transition rules
- Enforce content constraints

**Don't**:
- Don't allow unauthorized updates
- Don't bypass ownership checks
- Don't ignore status rules
- Don't update without validation

## 2030b/app/Http/Requests/Course/PublishCourseRequest.php

**Purpose**: Validates course publishing requests ensuring content completeness and quality standards.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Models\DocuCourse;
use App\Rules\CourseReadyForPublishing;
use App\Services\Course\CourseValidationService;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- validateCourseContent(): bool

**Do**:
- Verify publishing permissions
- Check content completeness
- Validate course structure
- Ensure quality standards

**Don't**:
- Don't publish incomplete courses
- Don't bypass quality checks
- Don't ignore content validation
- Don't publish without authorization

## 2030b/app/Http/Requests/Course/CreateEpisodeRequest.php

**Purpose**: Validates episode creation within courses including content structure and access controls.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Models\DocuCourse;
use App\Rules\ValidEpisodeOrder;
use App\Enums\ContentAccessLevel;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getCourse(): DocuCourse

**Do**:
- Verify course ownership
- Validate episode ordering
- Check access level constraints
- Enforce content structure

**Don't**:
- Don't create episodes without permission
- Don't ignore ordering constraints
- Don't bypass access validation
- Don't create invalid episode structures

## 2030b/app/Http/Requests/Course/UpdateEpisodeRequest.php

**Purpose**: Validates episode content updates and structural modifications within courses.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Models\Episode;
use App\Rules\ValidEpisodeOrder;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getEpisode(): Episode

**Do**:
- Verify episode ownership
- Validate content changes
- Check ordering constraints
- Enforce access rules

**Don't**:
- Don't update without authorization
- Don't ignore ordering rules
- Don't bypass content validation
- Don't create inconsistent structures

## 2030b/app/Http/Requests/Course/CourseUploadRequest.php

**Purpose**: Validates course file uploads including format verification and size limitations.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Rules\ValidCourseFile;
use App\Rules\AllowedFileSize;
use App\Models\DocuCourse;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getMaxFileSize(): int

**Do**:
- Validate file formats
- Check file size limits
- Verify upload permissions
- Scan for security threats

**Don't**:
- Don't allow unauthorized uploads
- Don't bypass file validation
- Don't ignore size limits
- Don't upload malicious files

## 2030b/app/Http/Requests/Course/MediaUploadRequest.php

**Purpose**: Validates media file uploads for courses including images, videos, and documents.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Rules\ValidMediaFile;
use App\Rules\AllowedMimeType;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getAllowedMimeTypes(): array

**Do**:
- Validate media file types
- Check file integrity
- Verify upload permissions
- Process media appropriately

**Don't**:
- Don't upload invalid media
- Don't bypass mime type checks
- Don't ignore file integrity
- Don't upload without authorization

## Payment Requests

## 2030b/app/Http/Requests/Payment/ProcessPaymentRequest.php

**Purpose**: Validates payment processing requests including amount verification and payment method validation.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Rules\ValidPaymentAmount;
use App\Rules\ValidPaymentMethod;
use App\Rules\ValidCurrency;
use App\Enums\PaymentProvider;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getPaymentData(): array

**Do**:
- Validate payment amounts
- Verify payment methods
- Check user authorization
- Validate currency codes

**Don't**:
- Don't process invalid amounts
- Don't bypass payment validation
- Don't ignore authorization checks
- Don't accept invalid currencies

## 2030b/app/Http/Requests/Payment/StripePaymentRequest.php

**Purpose**: Validates Stripe-specific payment requests with payment intent and method verification.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Rules\ValidStripeToken;
use App\Rules\ValidStripePaymentMethod;
use Stripe\PaymentMethod;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getStripePaymentMethod(): PaymentMethod

**Do**:
- Validate Stripe tokens
- Verify payment methods
- Check Stripe integration
- Handle Stripe errors

**Don't**:
- Don't accept invalid tokens
- Don't bypass Stripe validation
- Don't ignore payment method verification
- Don't expose Stripe secrets

## 2030b/app/Http/Requests/Payment/BinancePaymentRequest.php

**Purpose**: Validates Binance Pay cryptocurrency payment requests with wallet and amount verification.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Rules\ValidCryptoAmount;
use App\Rules\ValidCryptoWallet;
use App\Services\Payment\BinanceService;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getCryptoAmount(): string

**Do**:
- Validate cryptocurrency amounts
- Verify wallet addresses
- Check Binance integration
- Handle crypto-specific rules

**Don't**:
- Don't accept invalid crypto amounts
- Don't bypass wallet validation
- Don't ignore Binance requirements
- Don't expose crypto credentials

## 2030b/app/Http/Requests/Payment/CreateInvoiceRequest.php

**Purpose**: Validates invoice creation requests with line items and tax calculations.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Rules\ValidInvoiceItems;
use App\Rules\ValidTaxAmount;
use App\Models\User;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- calculateTotal(): float

**Do**:
- Validate invoice items
- Calculate tax amounts
- Verify invoice permissions
- Check item pricing

**Don't**:
- Don't create invalid invoices
- Don't bypass tax calculations
- Don't ignore pricing validation
- Don't create unauthorized invoices

## 2030b/app/Http/Requests/Payment/RefundRequest.php

**Purpose**: Validates refund requests including authorization checks and refund amount validation.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Models\Transaction;
use App\Rules\ValidRefundAmount;
use App\Rules\CanRefundTransaction;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getTransaction(): Transaction

**Do**:
- Verify refund permissions
- Validate refund amounts
- Check transaction eligibility
- Handle partial refunds

**Don't**:
- Don't process unauthorized refunds
- Don't bypass refund validation
- Don't ignore transaction checks
- Don't refund invalid amounts

## Affiliate Requests

## 2030b/app/Http/Requests/Affiliate/CreateAffiliateRequest.php

**Purpose**: Validates affiliate program registration with commission structure and approval workflow.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Rules\ValidCommissionRate;
use App\Rules\ValidAffiliateData;
use App\Models\User;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getCommissionRate(): float

**Do**:
- Validate affiliate eligibility
- Check commission rates
- Verify user qualifications
- Enforce program terms

**Don't**:
- Don't bypass eligibility checks
- Don't accept invalid rates
- Don't ignore program requirements
- Don't create unauthorized affiliates

## 2030b/app/Http/Requests/Affiliate/UpdateAffiliateRequest.php

**Purpose**: Validates affiliate account updates including commission rate changes and status modifications.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Models\Affiliate;
use App\Rules\ValidCommissionRate;
use App\Enums\AffiliateStatus;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getAffiliate(): Affiliate

**Do**:
- Verify update permissions
- Validate commission changes
- Check status transitions
- Enforce business rules

**Don't**:
- Don't allow unauthorized updates
- Don't bypass rate validation
- Don't ignore status rules
- Don't update without verification

## 2030b/app/Http/Requests/Affiliate/CreateReferralCodeRequest.php

**Purpose**: Validates referral code creation with uniqueness checks and expiration settings.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Rules\UniqueReferralCode;
use App\Rules\ValidReferralType;
use App\Models\Affiliate;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- generateCode(): string

**Do**:
- Validate code uniqueness
- Check referral types
- Set appropriate expiration
- Verify affiliate ownership

**Don't**:
- Don't create duplicate codes
- Don't bypass uniqueness checks
- Don't ignore expiration rules
- Don't create unauthorized codes

## 2030b/app/Http/Requests/Affiliate/TrackClickRequest.php

**Purpose**: Validates affiliate click tracking requests with session and referrer information.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Rules\ValidReferralCode;
use App\Rules\ValidTrackingData;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getTrackingData(): array

**Do**:
- Validate referral codes
- Track click data accurately
- Prevent click fraud
- Respect privacy rules

**Don't**:
- Don't track invalid codes
- Don't ignore fraud prevention
- Don't bypass privacy checks
- Don't track without consent

## System Configuration Requests

## 2030b/app/Http/Requests/System/UpdateConfigurationRequest.php

**Purpose**: Validates system configuration updates with appropriate authorization and value validation.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Rules\ValidConfigurationKey;
use App\Rules\ValidConfigurationValue;
use App\Models\Configuration;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getConfigurationValue(): mixed

**Do**:
- Verify admin permissions
- Validate configuration keys
- Check value formats
- Enforce system constraints

**Don't**:
- Don't allow unauthorized config changes
- Don't bypass value validation
- Don't ignore system constraints
- Don't update critical configs without verification

## 2030b/app/Http/Requests/System/CreateLanguageRequest.php

**Purpose**: Validates language configuration creation with locale codes and direction settings.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Rules\ValidLocaleCode;
use App\Rules\UniqueLanguageCode;
use App\Enums\LanguageDirection;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getLocaleData(): array

**Do**:
- Validate locale codes
- Check language uniqueness
- Verify direction settings
- Enforce localization standards

**Don't**:
- Don't create invalid locales
- Don't bypass uniqueness checks
- Don't ignore direction rules
- Don't create unauthorized languages

## 2030b/app/Http/Requests/System/UpdateCurrencyRequest.php

**Purpose**: Validates currency configuration updates including exchange rates and status changes.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use App\Rules\ValidCurrencyCode;
use App\Rules\ValidExchangeRate;
use App\Models\Currency;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getCurrency(): Currency

**Do**:
- Validate currency codes
- Check exchange rates
- Verify rate sources
- Update rates appropriately

**Don't**:
- Don't accept invalid rates
- Don't bypass rate validation
- Don't ignore currency standards
- Don't update without authorization

## API Requests

## 2030b/app/Http/Requests/Api/ApiRequest.php

**Purpose**: Base request class for API endpoints providing common validation and authentication patterns.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Contracts\Validation\Validator;
use Illuminate\Http\Exceptions\HttpResponseException;
use Laravel\Sanctum\HasApiTokens;
```

**Methods**:
- authorize(): bool
- failedValidation(Validator $validator): void
- failedAuthorization(): void
- getApiUser(): User
- wantsJson(): bool

**Do**:
- Implement consistent API responses
- Handle validation failures properly
- Use Sanctum for API authentication
- Return JSON error responses

**Don't**:
- Don't return HTML responses for API
- Don't bypass API authentication
- Don't ignore validation patterns
- Don't create inconsistent API behavior

## 2030b/app/Http/Requests/Api/CreateTokenRequest.php

**Purpose**: Validates API token creation requests with ability assignments and expiration settings.

**Dependencies**:
```php
use App\Http\Requests\Api\ApiRequest;
use App\Rules\ValidTokenAbilities;
use Laravel\Sanctum\PersonalAccessToken;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getAbilities(): array

**Do**:
- Validate token abilities
- Set appropriate expiration
- Verify token permissions
- Log token creation

**Don't**:
- Don't create tokens without validation
- Don't bypass ability checks
- Don't ignore expiration settings
- Don't create unauthorized tokens

## 2030b/app/Http/Requests/Api/RevokeTokenRequest.php

**Purpose**: Validates API token revocation requests with ownership verification and security checks.

**Dependencies**:
```php
use App\Http\Requests\Api\ApiRequest;
use Laravel\Sanctum\PersonalAccessToken;
use App\Rules\ValidTokenId;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getToken(): PersonalAccessToken

**Do**:
- Verify token ownership
- Validate token existence
- Log token revocation
- Handle revocation gracefully

**Don't**:
- Don't revoke without verification
- Don't bypass ownership checks
- Don't ignore security implications
- Don't revoke system tokens

## Permission Requests

## 2030b/app/Http/Requests/Permission/AssignRoleRequest.php

**Purpose**: Validates role assignment requests with permission checks and user verification.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use Spatie\Permission\Models\Role;
use App\Models\User;
use App\Rules\ValidRole;
use App\Rules\CanAssignRole;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getRole(): Role
- getUser(): User

**Do**:
- Verify assignment permissions
- Check role validity
- Validate user existence
- Enforce role constraints

**Don't**:
- Don't assign without authorization
- Don't bypass role validation
- Don't ignore permission constraints
- Don't assign invalid roles

## 2030b/app/Http/Requests/Permission/CreatePermissionRequest.php

**Purpose**: Validates permission creation with guard name verification and uniqueness checks.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use Spatie\Permission\Models\Permission;
use App\Rules\ValidGuardName;
use App\Rules\UniquePermission;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getGuardName(): string

**Do**:
- Validate permission names
- Check guard names
- Ensure permission uniqueness
- Verify creation permissions

**Don't**:
- Don't create duplicate permissions
- Don't bypass guard validation
- Don't ignore uniqueness checks
- Don't create without authorization

## 2030b/app/Http/Requests/Permission/UpdateRoleRequest.php

**Purpose**: Validates role updates including permission assignments and role configuration changes.

**Dependencies**:
```php
use Illuminate\Foundation\Http\FormRequest;
use Spatie\Permission\Models\Role;
use Spatie\Permission\Models\Permission;
use App\Rules\ValidPermissions;
```

**Methods**:
- authorize(): bool
- rules(): array
- messages(): array
- getRole(): Role
- getPermissions(): Collection

**Do**:
- Verify role update permissions
- Validate permission assignments
- Check role constraints
- Update roles atomically

**Don't**:
- Don't update without authorization
- Don't bypass permission validation
- Don't ignore role constraints
- Don't create inconsistent permissions
