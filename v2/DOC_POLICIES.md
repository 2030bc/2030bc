# DOC_POLICIES.md

Policies define authorization logic for model access control, integrating with Laravel's authorization system and Spatie permissions for role-based access control.

## app/Policies/UserPolicy.php

**Purpose**
→ Defines authorization rules for user management operations including viewing profiles, updating information, managing permissions, and administrative actions.

**Dependencies**
```php
use Illuminate\Auth\Access\HandlesAuthorization;
use App\Models\User;
use App\Enums\RoleType;
use App\Enums\PermissionType;
use Spatie\Permission\Models\Role;
```

**Methods**
- viewAny()
- view()
- create()
- update()
- delete()
- restore()
- forceDelete()
- manageRoles()
- viewProfile()

**Do**
✅ Check user permissions and roles
✅ Validate user level requirements
✅ Protect sensitive operations
✅ Support hierarchical permissions
✅ Log authorization attempts

**Don't**
❌ Don't allow unauthorized access
❌ Don't ignore user level restrictions
❌ Don't skip permission validation
❌ Don't hardcode authorization logic

---

## app/Policies/DocuCoursePolicy.php

**Purpose**
→ Controls access to course management operations including creation, editing, publishing, enrollment, and content viewing based on user levels and permissions.

**Dependencies**
```php
use Illuminate\Auth\Access\HandlesAuthorization;
use App\Models\User;
use App\Models\Course\DocuCourse;
use App\Enums\ContentAccessLevel;
use App\Enums\UserLevel;
```

**Methods**
- viewAny()
- view()
- create()
- update()
- delete()
- publish()
- enroll()
- viewContent()
- manageEpisodes()

**Do**
✅ Check content access levels
✅ Validate user enrollment
✅ Verify payment requirements
✅ Enforce user level restrictions
✅ Support preview modes

**Don't**
❌ Don't allow unpaid access to premium content
❌ Don't ignore level requirements
❌ Don't skip enrollment validation
❌ Don't expose private courses

---

## app/Policies/EpisodePolicy.php

**Purpose**
→ Manages episode access control including viewing permissions, progress tracking, note creation, and completion recording based on course enrollment and user levels.

**Dependencies**
```php
use Illuminate\Auth\Access\HandlesAuthorization;
use App\Models\User;
use App\Models\Course\Episode;
use App\Services\Course\EnrollmentService;
```

**Methods**
- view()
- createNote()
- updateProgress()
- markComplete()
- accessParts()
- viewAnalytics()

**Do**
✅ Verify course enrollment
✅ Check episode prerequisites
✅ Validate access levels
✅ Track viewing permissions
✅ Support progressive disclosure

**Don't**
❌ Don't allow access without enrollment
❌ Don't ignore prerequisites
❌ Don't skip access validation
❌ Don't expose restricted content

---

## app/Policies/CategoryPolicy.php

**Purpose**
→ Controls category management operations including creation, modification, deletion, and course assignment with hierarchical permission support for administrators.

**Dependencies**
```php
use Illuminate\Auth\Access\HandlesAuthorization;
use App\Models\User;
use App\Models\Course\Category;
use App\Enums\RoleType;
```

**Methods**
- viewAny()
- view()
- create()
- update()
- delete()
- manageCourses()
- reorder()

**Do**
✅ Restrict to authorized administrators
✅ Validate hierarchical relationships
✅ Check course assignment permissions
✅ Support category reordering
✅ Maintain category integrity

**Don't**
❌ Don't allow unauthorized category changes
❌ Don't ignore hierarchy rules
❌ Don't skip relationship validation
❌ Don't break category structure

---

## app/Policies/TransactionPolicy.php

**Purpose**
→ Manages transaction access control including viewing payment history, processing refunds, generating invoices, and financial reporting with strict security measures.

**Dependencies**
```php
use Illuminate\Auth\Access\HandlesAuthorization;
use App\Models\User;
use App\Models\Payment\Transaction;
use App\Enums\TransactionType;
```

**Methods**
- viewAny()
- view()
- refund()
- generateInvoice()
- viewFinancials()
- processDispute()

**Do**
✅ Restrict to transaction owners
✅ Validate financial permissions
✅ Check refund eligibility
✅ Protect sensitive data
✅ Log financial access

**Don't**
❌ Don't expose other users' transactions
❌ Don't allow unauthorized refunds
❌ Don't skip security validation
❌ Don't ignore compliance rules

---

## app/Policies/InvoicePolicy.php

**Purpose**
→ Controls invoice management including generation, viewing, downloading, and archiving with tax compliance and financial reporting authorization checks.

**Dependencies**
```php
use Illuminate\Auth\Access\HandlesAuthorization;
use App\Models\User;
use App\Models\Payment\Invoice;
use App\Services\Compliance\TaxComplianceService;
```

**Methods**
- viewAny()
- view()
- download()
- regenerate()
- archive()
- viewTaxInfo()

**Do**
✅ Verify invoice ownership
✅ Check tax compliance
✅ Validate download permissions
✅ Protect financial data
✅ Support admin access

**Don't**
❌ Don't allow unauthorized downloads
❌ Don't ignore tax regulations
❌ Don't expose sensitive data
❌ Don't skip ownership validation

---

## app/Policies/AffiliatePolicy.php

**Purpose**
→ Manages affiliate program access including registration, commission viewing, payout requests, referral tracking, and performance analytics with fraud prevention.

**Dependencies**
```php
use Illuminate\Auth\Access\HandlesAuthorization;
use App\Models\User;
use App\Models\Affiliate\Affiliate;
use App\Services\Affiliate\FraudDetectionService;
```

**Methods**
- view()
- update()
- viewCommissions()
- requestPayout()
- viewAnalytics()
- manageReferrals()

**Do**
✅ Verify affiliate ownership
✅ Check program eligibility
✅ Validate commission access
✅ Detect fraudulent activity
✅ Support performance tracking

**Don't**
❌ Don't allow unauthorized access
❌ Don't ignore fraud detection
❌ Don't skip eligibility checks
❌ Don't expose other affiliates' data

---

## app/Policies/CommissionPolicy.php

**Purpose**
→ Controls commission management including viewing earnings, payout processing, dispute resolution, and financial reporting with anti-fraud measures and compliance.

**Dependencies**
```php
use Illuminate\Auth\Access\HandlesAuthorization;
use App\Models\User;
use App\Models\Affiliate\Commission;
use App\Services\Compliance\FinancialComplianceService;
```

**Methods**
- view()
- requestPayout()
- dispute()
- viewHistory()
- generateReport()

**Do**
✅ Validate commission ownership
✅ Check payout eligibility
✅ Support dispute resolution
✅ Maintain compliance records
✅ Prevent manipulation

**Don't**
❌ Don't allow unauthorized payouts
❌ Don't ignore compliance rules
❌ Don't skip fraud validation
❌ Don't expose private data

---

## app/Policies/MediaPolicy.php

**Purpose**
→ Manages media file access including upload permissions, viewing rights, download authorization, and file management with storage quota and security validation.

**Dependencies**
```php
use Illuminate\Auth\Access\HandlesAuthorization;
use App\Models\User;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use App\Services\Media\MediaService;
```

**Methods**
- view()
- upload()
- download()
- delete()
- optimize()
- manageCollections()

**Do**
✅ Check file ownership
✅ Validate upload permissions
✅ Enforce storage quotas
✅ Scan for malware
✅ Support media collections

**Don't**
❌ Don't allow unauthorized uploads
❌ Don't ignore file security
❌ Don't skip quota validation
❌ Don't expose private media

---

## app/Policies/ConfigurationPolicy.php

**Purpose**
→ Controls system configuration access including settings management, feature toggles, payment configurations, and system maintenance with administrator-level security.

**Dependencies**
```php
use Illuminate\Auth\Access\HandlesAuthorization;
use App\Models\User;
use App\Models\System\Configuration;
use App\Enums\RoleType;
```

**Methods**
- viewAny()
- view()
- update()
- create()
- delete()
- manageSystem()

**Do**
✅ Restrict to super administrators
✅ Validate critical changes
✅ Log configuration updates
✅ Support change rollback
✅ Maintain system integrity

**Don't**
❌ Don't allow unauthorized changes
❌ Don't ignore security implications
❌ Don't skip change logging
❌ Don't break system functionality