# DOC_RULES.md

## Overview
The Rules component provides custom validation rules for the Laravel docu-course platform. These rules extend Laravel's built-in validation system with domain-specific validation logic for files, content, security, and business requirements. Updated.

---

## File Validation Rules

### app/Rules/ValidCourseFile.php
**Purpose**: Validates uploaded course files for format, size, and content structure
**Dependencies**:
- use Illuminate\Contracts\Validation\Rule
- use Illuminate\Http\UploadedFile
- use App\Enums\FileType
- use App\Services\File\FileValidatorService

**Methods**:
- passes($attribute, $value)
- message()
- validateFileType(UploadedFile $file)
- validateFileSize(UploadedFile $file)
- validateCourseStructure(UploadedFile $file)
- scanForMalware(UploadedFile $file)

**Validation Criteria**:
- Allowed file types: ZIP, RAR, TAR.GZ
- Maximum file size: 500MB
- Required course structure (episodes, metadata)
- Malware scanning
- Content validation

**Do**:
- ✅ Validate file MIME types
- ✅ Check file size limits
- ✅ Scan for malware
- ✅ Validate course structure
- ✅ Log validation attempts

**Don't**:
- ❌ Trust client-provided MIME types
- ❌ Skip malware scanning
- ❌ Allow executable files
- ❌ Process corrupted archives
- ❌ Forget to validate content

### app/Rules/ValidImageDimensions.php
**Purpose**: Validates image dimensions and quality for platform requirements
**Dependencies**:
- use Illuminate\Contracts\Validation\Rule
- use Illuminate\Http\UploadedFile
- use Intervention\Image\Facades\Image

**Methods**:
- passes($attribute, $value)
- message()
- validateDimensions(UploadedFile $file)
- validateAspectRatio(int $width, int $height)
- validateImageQuality(UploadedFile $file)

**Validation Criteria**:
- Minimum dimensions: 800x600
- Maximum dimensions: 4000x3000
- Allowed aspect ratios: 16:9, 4:3, 1:1
- Image quality checks
- Format validation

**Do**:
- ✅ Check minimum/maximum dimensions
- ✅ Validate aspect ratios
- ✅ Verify image integrity
- ✅ Support multiple formats
- ✅ Optimize for web display

**Don't**:
- ❌ Allow oversized images
- ❌ Skip quality validation
- ❌ Process corrupted images
- ❌ Ignore aspect ratio requirements
- ❌ Accept inappropriate formats

### app/Rules/ValidVideoDuration.php
**Purpose**: Validates video duration limits for episode content
**Dependencies**:
- use Illuminate\Contracts\Validation\Rule
- use Illuminate\Http\UploadedFile
- use FFMpeg\FFMpeg

**Methods**:
- passes($attribute, $value)
- message()
- getVideoDuration(UploadedFile $file)
- validateDurationLimits(int $duration)
- validateVideoFormat(UploadedFile $file)

**Validation Criteria**:
- Minimum duration: 30 seconds
- Maximum duration: 4 hours (14400 seconds)
- Allowed formats: MP4, AVI, MOV, MKV
- Quality requirements
- Codec validation

**Do**:
- ✅ Use FFMpeg for accurate duration
- ✅ Validate video codecs
- ✅ Check format compatibility
- ✅ Verify audio tracks
- ✅ Test video integrity

**Don't**:
- ❌ Trust metadata duration
- ❌ Allow unsupported formats
- ❌ Skip codec validation
- ❌ Process corrupted videos
- ❌ Ignore quality requirements

### app/Rules/ValidDocumentType.php
**Purpose**: Validates document types and content for platform safety
**Dependencies**:
- use Illuminate\Contracts\Validation\Rule
- use Illuminate\Http\UploadedFile
- use App\Enums\FileType

**Methods**:
- passes($attribute, $value)
- message()
- validateDocumentType(UploadedFile $file)
- scanDocumentContent(UploadedFile $file)
- validateFileIntegrity(UploadedFile $file)

**Validation Criteria**:
- Allowed types: PDF, DOCX, TXT, MD
- Maximum file size: 50MB
- Content scanning for malicious code
- Document structure validation
- Text encoding validation

**Do**:
- ✅ Validate document MIME types
- ✅ Scan for embedded scripts
- ✅ Check file integrity
- ✅ Validate text encoding
- ✅ Limit file sizes

**Don't**:
- ❌ Allow executable documents
- ❌ Skip content scanning
- ❌ Process password-protected files
- ❌ Trust file extensions only
- ❌ Allow macro-enabled documents

### app/Rules/FileSizeLimit.php
**Purpose**: Validates file size limits based on user level and file type
**Dependencies**:
- use Illuminate\Contracts\Validation\Rule
- use Illuminate\Http\UploadedFile
- use App\Enums\UserLevel
- use App\Enums\FileType

**Methods**:
- passes($attribute, $value)
- message()
- getSizeLimitForUser(int $userLevel, string $fileType)
- formatFileSize(int $bytes)
- validateTotalStorage(int $userId, int $fileSize)

**Size Limits by User Level**:
- Level 1-3: 100MB per file, 1GB total
- Level 4-6: 250MB per file, 5GB total
- Level 7-10: 500MB per file, 20GB total
- Level 11-14: 1GB per file, 100GB total

**Do**:
- ✅ Check user level limits
- ✅ Validate total storage usage
- ✅ Consider file type in limits
- ✅ Provide clear error messages
- ✅ Update limits dynamically

**Don't**:
- ❌ Apply same limits to all users
- ❌ Skip total storage validation
- ❌ Allow unlimited uploads
- ❌ Forget to check quotas
- ❌ Ignore file type differences

---

## Security Validation Rules

### app/Rules/StrongPassword.php
**Purpose**: Validates password strength according to security requirements
**Dependencies**:
- use Illuminate\Contracts\Validation\Rule
- use Illuminate\Validation\Rules\Password

**Methods**:
- passes($attribute, $value)
- message()
- validateLength(string $password)
- validateComplexity(string $password)
- checkCommonPasswords(string $password)
- validateAgainstUserData(string $password, array $userData)

**Password Requirements**:
- Minimum 8 characters
- At least one uppercase letter
- At least one lowercase letter
- At least one number
- At least one special character
- Not in common passwords list
- Not similar to user data

**Do**:
- ✅ Enforce complexity rules
- ✅ Check against common passwords
- ✅ Validate against user information
- ✅ Provide helpful error messages
- ✅ Support password strength indicators

**Don't**:
- ❌ Store passwords in plain text
- ❌ Allow weak passwords
- ❌ Skip complexity validation
- ❌ Ignore common password lists
- ❌ Allow personal information in passwords

### app/Rules/UniqueSlug.php
**Purpose**: Validates uniqueness of slugs across different content types
**Dependencies**:
- use Illuminate\Contracts\Validation\Rule
- use Illuminate\Database\Eloquent\Model
- use Illuminate\Support\Str

**Methods**:
- passes($attribute, $value)
- message()
- checkSlugUniqueness(string $slug, string $table, int $excludeId)
- generateAlternativeSlug(string $slug)
- validateSlugFormat(string $slug)

**Slug Requirements**:
- Lowercase letters, numbers, hyphens only
- No consecutive hyphens
- No leading/trailing hyphens
- Maximum 100 characters
- Unique across specified table

**Do**:
- ✅ Validate slug format
- ✅ Check uniqueness in database
- ✅ Suggest alternative slugs
- ✅ Handle special characters
- ✅ Support update scenarios

**Don't**:
- ❌ Allow special characters
- ❌ Skip uniqueness validation
- ❌ Create overly long slugs
- ❌ Use sequential numbers only
- ❌ Ignore SEO best practices

---

## Usage Examples

### Using Rules in Form Requests

```php
// In CreateCourseRequest.php
public function rules()
{
    return [
        'title' => 'required|string|max:255',
        'alias' => ['required', 'string', new UniqueSlug('docu_courses')],
        'course_file' => ['required', 'file', new ValidCourseFile()],
        'cover_image' => ['nullable', 'image', new ValidImageDimensions()],
        'trailer_video' => ['nullable', 'file', new ValidVideoDuration()],
        'documents.*' => ['nullable', 'file', new ValidDocumentType()],
        'password' => ['required', 'string', new StrongPassword()],
    ];
}
```

### Using Rules in Controllers

```php
public function uploadCourse(Request $request)
{
    $request->validate([
        'course_file' => [
            'required',
            'file',
            new ValidCourseFile(),
            new FileSizeLimit(auth()->user()->level)
        ]
    ]);
    
    // Process validated file
}
```

---

## Best Practices

### Rule Design
- Keep rules focused on single validation concern
- Provide clear, actionable error messages
- Support both sync and async validation
- Include helpful suggestions when possible
- Cache expensive validation operations

### Performance Considerations
- Avoid expensive operations in rules
- Use database indexes for uniqueness checks
- Cache validation results when appropriate
- Implement early returns for invalid data
- Use queued validation for large files

### Security Best Practices
- Never trust client-provided data
- Validate file contents, not just extensions
- Implement rate limiting for validation attempts
- Log suspicious validation failures
- Use secure temporary file handling

### Error Handling
- Provide specific error messages
- Include context about validation requirements
- Support multiple languages
- Log validation failures appropriately  
- Offer suggestions for fixing errors

---

## Overview
The Rules component provides comprehensive custom validation rules for the Laravel docu-course platform, ensuring data integrity, security, and business logic compliance throughout the application. These rules integrate seamlessly with FormRequest validation, service layer validation, and business constraint enforcement while supporting multi-language error messaging and contextual validation patterns.

---

## Validation Architecture Integration

### FormRequest Integration
All validation rules are designed for seamless integration with FormRequest classes from DOC_REQUESTS.md:

```php
// Example integration in CreateCourseRequest.php
public function rules(): array
{
    return [
        'title' => ['required', 'string', 'max:255'],
        'alias' => ['required', 'string', new UniqueSlug('docu_courses', $this->course)],
        'course_file' => ['required', 'file', new ValidCourseFile()],
        'cover_image' => ['nullable', 'image', new ValidImageDimensions()],
        'trailer_video' => ['nullable', 'file', new ValidVideoDuration()],
        'documents.*' => ['nullable', 'file', new ValidDocumentType()],
        'password' => ['required', 'string', new StrongPassword($this->user())]
    ];
}
```

### Business Logic Alignment
Validation rules enforce business constraints defined in services and models:

- **User Level Constraints**: FileSizeLimit rule integrates with user progression system
- **Course Structure Requirements**: ValidCourseFile rule enforces episode organization standards
- **Financial Validation**: Payment amount validation aligns with Money PHP usage
- **Security Policies**: StrongPassword rule enforces authentication security requirements

### Database Constraint Synchronization
Rules mirror database constraints and enum values from DATABASE_SCHEMA_V3.md:

- **UniqueSlug**: Enforces slug uniqueness across specified tables
- **Enum Validation**: Custom rules validate against PHP enum definitions
- **Foreign Key Validation**: Rules verify referenced resource existence
- **Index Optimization**: Validation leverages database indexes for performance

### Multi-Language Support
All validation rules support internationalization through Laravel's localization system:

- **RTL Language Support**: Error messages adapt to right-to-left languages
- **Cultural Context**: Date formats, currency symbols, and numeric formats respect user locale
- **Dynamic Translation**: Error messages use translation keys for multi-language support
- **Fallback Handling**: Graceful fallback to default language for missing translations

---

## File Validation Rules

### app/Rules/ValidCourseFile.php

**Purpose**: Validates uploaded course files for format compliance, security, and structural integrity according to platform requirements.

**Integration Dependencies**:
```php
use App\Enums\FileType;
use App\Services\File\FileValidatorService;
use App\Models\Course\DocuCourse;
```

**Validation Criteria**:
- **Allowed Formats**: ZIP, RAR, TAR.GZ archives containing course structure
- **Size Limits**: Maximum 500MB with user-level quota integration
- **Structure Validation**: Required course metadata, episode organization, asset hierarchy
- **Security Scanning**: Malware detection and content validation
- **Encoding Verification**: UTF-8 encoding for text content, proper media encoding

**Methods**:
```php
public function passes($attribute, $value): bool
public function message(): string
protected function validateFileType(UploadedFile $file): bool
protected function validateFileSize(UploadedFile $file): bool  
protected function validateCourseStructure(UploadedFile $file): bool
protected function scanForMalware(UploadedFile $file): bool
protected function validateMetadata(array $metadata): bool
```

**Business Logic Integration**:
- **Course Creation Workflow**: Integrates with CourseService for content validation
- **User Level Requirements**: Minimum level 7 required for course file uploads
- **Storage Quota**: Validates against user storage allocation
- **Content Standards**: Enforces platform content quality standards

### app/Rules/ValidImageDimensions.php

**Purpose**: Validates image uploads for dimension requirements, quality standards, and platform compatibility.

**Spatie Integration**:
```php
use Intervention\Image\Facades\Image;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
```

**Validation Criteria**:
- **Dimension Requirements**: Minimum 800x600, maximum 4000x3000 pixels
- **Aspect Ratio Control**: Allowed ratios 16:9, 4:3, 1:1 for platform consistency
- **Quality Assessment**: Image clarity, compression artifacts, color depth validation
- **Format Support**: JPEG, PNG, WebP with automatic optimization
- **File Size Optimization**: Automatic compression for performance

**Methods**:
```php
public function passes($attribute, $value): bool
public function message(): string
protected function validateDimensions(UploadedFile $file): bool
protected function validateAspectRatio(int $width, int $height): bool
protected function validateImageQuality(UploadedFile $file): bool
protected function optimizeForPlatform(UploadedFile $file): array
```

**Media Integration**:
- **Spatie MediaLibrary**: Automatic media processing and conversion generation
- **Thumbnail Creation**: Automatic thumbnail generation for platform use
- **Storage Optimization**: Efficient storage with multiple format variants

### app/Rules/ValidVideoDuration.php

**Purpose**: Validates video content for duration limits, format compliance, and quality standards suitable for educational content.

**Technical Dependencies**:
```php
use FFMpeg\FFMpeg;
use App\Services\Media\VideoProcessingService;
```

**Validation Criteria**:
- **Duration Limits**: 30 seconds minimum, 4 hours maximum for educational content
- **Format Requirements**: MP4, AVI, MOV, MKV with H.264 encoding preference
- **Quality Standards**: Minimum resolution, bitrate requirements, audio quality
- **Codec Validation**: Supported codecs for cross-platform compatibility
- **Accessibility**: Closed caption support and audio description compatibility

**Methods**:
```php
public function passes($attribute, $value): bool
public function message(): string
protected function getVideoDuration(UploadedFile $file): int
protected function validateDurationLimits(int $duration): bool
protected function validateVideoFormat(UploadedFile $file): bool
protected function validateCodecSupport(UploadedFile $file): bool
protected function extractVideoMetadata(UploadedFile $file): array
```

### app/Rules/ValidDocumentType.php

**Purpose**: Validates document uploads for security, format compliance, and content integrity with comprehensive scanning.

**Security Integration**:
```php
use App\Services\Security\DocumentScannerService;
use App\Enums\DocumentType;
```

**Validation Criteria**:
- **Allowed Types**: PDF, DOCX, TXT, MD with MIME type verification
- **Size Restrictions**: Maximum 50MB per document
- **Content Scanning**: Embedded script detection, malicious content prevention
- **Structure Validation**: Document integrity, proper formatting verification
- **Text Encoding**: UTF-8 encoding validation for international content

**Methods**:
```php
public function passes($attribute, $value): bool
public function message(): string
protected function validateDocumentType(UploadedFile $file): bool
protected function scanDocumentContent(UploadedFile $file): bool
protected function validateFileIntegrity(UploadedFile $file): bool
protected function extractDocumentText(UploadedFile $file): string
```

### app/Rules/FileSizeLimit.php

**Purpose**: Enforces user-level file size restrictions based on progression system and subscription status.

**User Level Integration**:
```php
use App\Enums\UserLevel;
use App\Services\User\LevelService;
use App\Models\User;
```

**Size Allocation Matrix**:
- **Levels 1-3 (Novice)**: 100MB per file, 1GB total storage
- **Levels 4-6 (Intermediate)**: 250MB per file, 5GB total storage  
- **Levels 7-10 (Advanced)**: 500MB per file, 20GB total storage
- **Levels 11-14 (Expert/VIP)**: 1GB per file, 100GB total storage
- **Premium Subscribers**: Enhanced quotas regardless of level
- **Instructors**: Unlimited for course content uploads

**Methods**:
```php
public function passes($attribute, $value): bool
public function message(): string
protected function getSizeLimitForUser(User $user, string $fileType): int
protected function validateTotalStorage(User $user, int $fileSize): bool
protected function formatFileSize(int $bytes): string
protected function getStorageUsage(User $user): int
protected function canUpgradeQuota(User $user): bool
```

**Progressive Enhancement**:
- **Dynamic Limits**: Quotas automatically update with user level progression
- **Overflow Handling**: Graceful handling when approaching storage limits
- **Upgrade Suggestions**: Helpful suggestions for quota increases

---

## Security Validation Rules

### app/Rules/StrongPassword.php

**Purpose**: Enforces comprehensive password security policies with user context awareness and breach detection.

**Security Integration**:
```php
use Illuminate\Validation\Rules\Password;
use App\Services\Security\PasswordBreachService;
use App\Models\User;
```

**Security Requirements**:
- **Length Policy**: Minimum 8 characters with complexity scaling
- **Character Requirements**: Uppercase, lowercase, numbers, special characters
- **Breach Detection**: Integration with HaveIBeenPwned API for compromised passwords
- **User Context**: Prevention of passwords similar to user data
- **Dictionary Attacks**: Protection against common password lists
- **Historical Validation**: Prevention of password reuse

**Methods**:
```php
public function passes($attribute, $value): bool
public function message(): string
protected function validateLength(string $password): bool
protected function validateComplexity(string $password): bool
protected function checkCommonPasswords(string $password): bool
protected function validateAgainstUserData(string $password, User $user): bool
protected function checkPasswordBreaches(string $password): bool
protected function validatePasswordHistory(string $password, User $user): bool
```

**Contextual Validation**:
- **User-Specific Rules**: Adapted requirements based on user role and access level
- **Progressive Requirements**: Enhanced requirements for administrative accounts
- **Localization Support**: Culturally appropriate character requirements

### app/Rules/UniqueSlug.php

**Purpose**: Ensures slug uniqueness across multiple content types with intelligent collision resolution and SEO optimization.

**Database Integration**:
```php
use Illuminate\Database\Eloquent\Model;
use App\Services\Content\SlugGenerationService;
```

**Validation Logic**:
- **Format Requirements**: Lowercase letters, numbers, hyphens only
- **Pattern Restrictions**: No consecutive hyphens, proper boundaries
- **Length Limits**: Maximum 100 characters for SEO optimization
- **Cross-Table Uniqueness**: Ensures uniqueness across specified tables
- **Collision Resolution**: Intelligent alternative suggestions
- **SEO Optimization**: Search engine friendly slug generation

**Methods**:
```php
public function passes($attribute, $value): bool
public function message(): string
protected function checkSlugUniqueness(string $slug, string $table, ?int $excludeId): bool
protected function generateAlternativeSlug(string $slug): string
protected function validateSlugFormat(string $slug): bool
protected function optimizeForSEO(string $slug): string
protected function suggestAlternatives(string $slug): array
```

**SEO Integration**:
- **Search Optimization**: SEO-friendly slug generation patterns
- **Redirect Management**: Automatic redirect creation for slug changes
- **Analytics Integration**: Slug performance tracking and optimization

---

## Business Logic Validation Rules

### app/Rules/ValidUserLevel.php

**Purpose**: Validates user level progression requirements and access control based on the 14-level system.

**Level System Integration**:
```php
use App\Enums\UserLevel;
use App\Services\User\LevelService;
use App\Models\User;
```

**Validation Scope**:
- **Level Progression**: Validates legitimate level advancement
- **Point Requirements**: Ensures sufficient points for level claims
- **Access Validation**: Verifies level-based feature access
- **Achievement Verification**: Confirms required achievements are met
- **Time Constraints**: Validates minimum time requirements between levels

**Methods**:
```php
public function passes($attribute, $value): bool
public function message(): string
protected function validateLevelProgression(User $user, UserLevel $targetLevel): bool
protected function checkPointRequirements(User $user, UserLevel $level): bool
protected function validateAchievements(User $user, UserLevel $level): bool
protected function checkTimeConstraints(User $user, UserLevel $level): bool
```

### app/Rules/ValidCourseAccess.php

**Purpose**: Validates complex course access requirements including enrollment, payment, and prerequisites.

**Course System Integration**:
```php
use App\Models\Course\DocuCourse;
use App\Services\Course\EnrollmentService;
use App\Services\Payment\PaymentService;
```

**Access Validation Matrix**:
- **Enrollment Status**: Active enrollment verification
- **Payment Validation**: Premium content payment verification  
- **Prerequisite Checking**: Required course completion validation
- **Level Requirements**: User progression level verification
- **Geographic Restrictions**: Region-based access control
- **Time-Based Access**: Scheduled content availability

**Methods**:
```php
public function passes($attribute, $value): bool
public function message(): string
protected function validateEnrollment(User $user, DocuCourse $course): bool
protected function validatePayment(User $user, DocuCourse $course): bool
protected function checkPrerequisites(User $user, DocuCourse $course): bool
protected function validateLevelRequirement(User $user, DocuCourse $course): bool
protected function checkGeographicAccess(User $user, DocuCourse $course): bool
```

---

## Financial Validation Rules

### app/Rules/ValidPaymentAmount.php

**Purpose**: Validates payment amounts with currency precision, limits, and fraud detection integration.

**Financial Integration**:
```php
use Money\Money;
use App\Services\Payment\CurrencyService;
use App\Services\Security\FraudDetectionService;
```

**Validation Criteria**:
- **Currency Precision**: Proper decimal handling for different currencies
- **Amount Limits**: Minimum and maximum transaction amounts
- **Fraud Detection**: Suspicious amount pattern recognition
- **Exchange Rate Validation**: Real-time rate verification for conversions
- **Regional Compliance**: Country-specific payment regulations

**Methods**:
```php
public function passes($attribute, $value): bool
public function message(): string
protected function validateAmountPrecision(Money $amount): bool
protected function checkAmountLimits(Money $amount, User $user): bool
protected function validateExchangeRate(Money $amount, string $currency): bool
protected function detectFraudPatterns(Money $amount, User $user): bool
```

### app/Rules/ValidCommissionRate.php

**Purpose**: Validates affiliate commission rates based on user level, performance, and program rules.

**Affiliate Integration**:
```php
use App\Models\Affiliate\Affiliate;
use App\Services\Affiliate\CommissionService;
```

**Rate Validation Logic**:
- **Level-Based Rates**: Commission rates based on user progression
- **Performance Tiers**: Enhanced rates for top performers
- **Program Limits**: Maximum allowable commission rates
- **Regulatory Compliance**: Legal commission rate restrictions
- **Historical Validation**: Rate change approval workflows

**Methods**:
```php
public function passes($attribute, $value): bool
public function message(): string
protected function validateRateForLevel(float $rate, UserLevel $level): bool
protected function checkPerformanceTier(Affiliate $affiliate, float $rate): bool
protected function validateRegulatoryCompliance(float $rate, string $region): bool
```

---

## Conditional Validation Rules

### app/Rules/ConditionalRequired.php

**Purpose**: Implements context-aware required field validation based on dynamic conditions.

**Conditional Logic**:
```php
use App\Services\Validation\ConditionEvaluator;
```

**Validation Patterns**:
- **Field Dependencies**: Required based on other field values
- **User Context**: Required based on user roles or levels
- **Business Rules**: Required based on complex business logic
- **Temporal Conditions**: Required based on time-based conditions

**Methods**:
```php
public function passes($attribute, $value): bool
public function message(): string
protected function evaluateCondition(array $data, string $condition): bool
protected function getContextualRequirement(array $data, User $user): bool
```

### app/Rules/ValidateEnumValue.php

**Purpose**: Validates values against PHP enum definitions with comprehensive enum support.

**Enum Integration**:
```php
use App\Enums\BaseEnum;
use ReflectionEnum;
```

**Validation Features**:
- **Type Safety**: Strict enum value validation
- **Localization**: Multi-language enum value support
- **Backwards Compatibility**: Legacy value mapping
- **Custom Validation**: Enum-specific validation rules

**Methods**:
```php
public function passes($attribute, $value): bool
public function message(): string
protected function validateEnumValue($value, string $enumClass): bool
protected function getLocalizedEnumValues(string $enumClass, string $locale): array
```

---

## Rule Composition & Advanced Patterns

### Composite Rule Implementation

**Rule Chaining Pattern**:
```php
// Complex validation rule composition
$rules = [
    'course_file' => [
        'required',
        'file',
        new ValidCourseFile(),
        new FileSizeLimit($user),
        Rule::when($user->level < 7, [
            new RequireAdminApproval()
        ])
    ]
];
```

**Conditional Rule Application**:
```php
// Context-aware rule application
public function rules(): array
{
    return [
        'payment_amount' => [
            'required',
            'numeric',
            new ValidPaymentAmount($this->currency),
            Rule::when($this->isInternational(), [
                new ValidateExchangeRate(),
                new CheckRegionalCompliance()
            ])
        ]
    ];
}
```

### Performance optimization Patterns

**Caching Integration**:
- **Rule Result Caching**: Cache expensive validation results
- **Database Query Optimization**: Minimize database calls in validation
- **Memory Management**: Efficient memory usage for large file validation
- **Parallel Validation**: Concurrent validation for independent rules

**Lazy Loading Patterns**:
- **Service Injection**: Lazy loading of validation services
- **Resource Loading**: On-demand loading of validation resources
- **Context Building**: Efficient validation context construction

---

## Error Handling & User Experience

### Multi-Language Error Messages

**Localization Integration**:
```php
// Multi-language error message support
public function message(): string
{
    return __('validation.custom.strong_password', [
        'min_length' => $this->minLength,
        'requirements' => $this->getLocalizedRequirements()
    ]);
}
```

**RTL Language Support**:
- **Text Direction**: Proper RTL text handling in error messages
- **Cultural Context**: Culturally appropriate error messaging
- **Numeric Formatting**: Locale-specific number and date formatting

### User-Friendly Validation

**Progressive Disclosure**:
- **Helpful Suggestions**: Specific improvement suggestions in error messages
- **Context-Aware Help**: Validation help based on user context
- **Recovery Actions**: Clear actions users can take to fix validation errors

**Accessibility Integration**:
- **Screen Reader Support**: ARIA-compliant error messaging
- **Keyboard Navigation**: Accessible validation error handling
- **Color Independence**: Error indication independent of color

---

## Testing & Quality Assurance

### Validation Rule Testing

**Test Coverage Requirements**:
- **Boundary Testing**: Edge cases and limit validation
- **Security Testing**: Malicious input and injection attempts
- **Performance Testing**: Validation performance under load
- **Localization Testing**: Multi-language validation accuracy

**Integration Testing**:
- **FormRequest Testing**: Complete validation workflow testing
- **Service Integration**: Validation integration with business services
- **Database Consistency**: Validation rule and database constraint alignment
- **Error Handling**: Comprehensive error scenario testing

### Quality Gates

**Code Quality Standards**:
- **Static Analysis**: PHPStan level 8 compliance for type safety
- **Security Scanning**: Regular security audit for validation vulnerabilities
- **Performance Benchmarking**: Validation performance monitoring
- **Documentation Coverage**: Complete documentation for all validation rules
