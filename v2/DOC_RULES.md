# DOC_RULES.md

## Overview
The Rules component provides custom validation rules for the Laravel docu-course platform. These rules extend Laravel's built-in validation system with domain-specific validation logic for files, content, security, and business requirements.

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