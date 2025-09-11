# DOC_POLICIES.md

## Overview
The Policies component implements Laravel's authorization system with seamless Spatie Permission package integration, providing comprehensive access control for all application resources. This component ensures secure, role-based, and permission-driven authorization throughout the docu-course platform while maintaining tight integration with the 14-level user progression system. Updated.

---

## Authorization Architecture Integration

### Spatie Permission Integration
All policies leverage the Spatie Permission package for role and permission-based access control:

- **HasRoles trait**: Integrated in User model for role management
- **HasPermissions trait**: Provides permission checking capabilities
- **Role hierarchy**: Support for hierarchical role structures
- **Permission inheritance**: Users inherit permissions through roles
- **Guard contexts**: Multi-guard support for web and API authentication

### Middleware Integration
Policies work in conjunction with authorization middleware from DOC_MIDDLEWARE.md:

- **CheckPermission middleware**: Validates specific permissions before controller actions
- **CheckRole middleware**: Enforces role-based access control
- **CheckUserLevel middleware**: Validates user progression level requirements
- **ValidateCourseAccess middleware**: Course-specific access validation
- **ValidateOwnership middleware**: Resource ownership verification

### Service Layer Authorization
Policies integrate with service layer authorization from DOC_SERVICES.md:

- **PermissionService**: Creates and manages permissions
- **RoleService**: Handles role assignment and hierarchy
- **LevelService**: Manages user progression and level-based access
- **AuthService**: Authentication context for authorization decisions

### Model Integration
All policies correspond to models documented in DOC_MODELS.md with authorization features:

- **User model**: HasRoles trait, permission checking methods
- **DocuCourse model**: Access level and enrollment validation
- **Transaction model**: Financial data protection
- **Affiliate model**: Commission and payout access control

---

## User Management Policies

### app/Policies/UserPolicy.php

**Purpose**: Defines comprehensive authorization rules for user management operations including profile access, administrative actions, and permission management.

**Spatie Integration**:
```php
use Spatie\Permission\Models\Role;
use Spatie\Permission\Models\Permission;
use App\Enums\RoleType;
use App\Enums\PermissionType;
```

**Authorization Methods**:
- **viewAny()**: Allows user listing for administrators and moderators with 'users.viewAny' permission
- **view()**: Profile viewing with privacy settings and ownership validation
- **create()**: User creation restricted to super administrators with 'users.create' permission
- **update()**: Profile updates with ownership verification and admin override capabilities
- **delete()**: User deletion with role hierarchy checks and data retention policies
- **restore()**: Soft delete restoration for administrators with 'users.restore' permission
- **forceDelete()**: Permanent deletion limited to super administrators
- **manageRoles()**: Role assignment restricted to users with 'roles.assign' permission
- **viewProfile()**: Profile access with privacy settings and friendship status

**Permission Matrix**:
- **Public**: Basic user registration and authentication
- **Authenticated**: Own profile management and public profile viewing
- **Level 4+**: Enhanced profile features and advanced privacy controls
- **Moderator Role**: User moderation capabilities and basic administration
- **Administrator Role**: Full user management including role assignments
- **Super Administrator**: All permissions including permanent user deletion

**Multi-Level Access Control**:
- User level progression affects available profile features and management capabilities
- Role inheritance allows hierarchical permission structures
- Ownership validation ensures users can only modify their own data unless authorized

### app/Policies/DocuCoursePolicy.php

**Purpose**: Controls comprehensive course access including creation, management, enrollment, and content viewing based on user levels, payments, and permissions.

**User Level Integration**:
```php
use App\Enums\ContentAccessLevel;
use App\Enums\UserLevel;
use App\Services\Course\EnrollmentService;
```

**Authorization Methods**:
- **viewAny()**: Course browsing with access level filtering and published status validation
- **view()**: Course access with enrollment verification, payment validation, and level requirements
- **create()**: Course creation for instructors with minimum level 7 and 'courses.create' permission
- **update()**: Course editing for authors and administrators with ownership validation
- **delete()**: Course deletion with enrollment checks and data preservation requirements
- **publish()**: Course publishing with content validation and quality requirements
- **enroll()**: Enrollment validation including payment status, prerequisites, and level requirements
- **viewContent()**: Episode access with enrollment verification and progressive unlock
- **manageEpisodes()**: Episode management for course authors and content administrators

**Access Level Matrix**:
- **Public Courses**: Freely accessible content for all users
- **Level-Restricted**: Content requiring minimum user progression levels
- **Premium Courses**: Paid content with transaction validation
- **VIP Courses**: Exclusive content for high-level users (Level 11+)
- **Instructor Content**: Teaching materials for qualified instructors

**Progressive Access System**:
- Course content unlocks based on user level progression
- Prerequisites enforce learning path dependencies
- Payment validation ensures legitimate access to premium content

---

## Content Management Policies

### app/Policies/EpisodePolicy.php

**Purpose**: Manages granular episode access control including viewing permissions, progress tracking, note creation, and completion recording based on course enrollment and user advancement.

**Enrollment Integration**:
```php
use App\Services\Course\EnrollmentService;
```

**Authorization Methods**:
- **view()**: Episode access with enrollment verification and sequential unlocking
- **createNote()**: Note creation for enrolled users with progress tracking
- **updateProgress()**: Progress updates with validation and achievement unlocking
- **markComplete()**: Episode completion with requirements validation
- **accessParts()**: Granular part access within episodes
- **viewAnalytics()**: Episode analytics for instructors and administrators

**Progressive Unlock System**:
- Episodes unlock sequentially based on course progress
- Completion requirements must be met before advancing
- Notes and progress are linked to user advancement

### app/Policies/CategoryPolicy.php

**Purpose**: Controls category management operations including creation, modification, and course assignment with hierarchical permission support.

**Role-Based Control**:
```php
use App\Enums\RoleType;
```

**Authorization Methods**:
- **viewAny()**: Category browsing for content organization
- **view()**: Category access with course filtering
- **create()**: Category creation for content administrators
- **update()**: Category modification with hierarchy validation
- **delete()**: Category deletion with course reassignment checks
- **manageCourses()**: Course assignment to categories
- **reorder()**: Category hierarchy management for administrators

---

## Financial & Transaction Policies

### app/Policies/TransactionPolicy.php

**Purpose**: Manages strict transaction access control including payment history, refund processing, and financial reporting with comprehensive security measures.

**Security Features**:
```php
use App\Enums\TransactionType;
use App\Services\Payment\PaymentService;
```

**Authorization Methods**:
- **viewAny()**: Transaction listing for administrators and financial auditors
- **view()**: Individual transaction access with ownership validation
- **refund()**: Refund processing with approval workflow and time limits
- **generateInvoice()**: Invoice generation for transaction owners and administrators
- **viewFinancials()**: Financial reporting for authorized personnel only
- **processDispute()**: Dispute handling for customer service and administrators

**Financial Security Matrix**:
- **User Access**: Own transaction history and invoice generation
- **Moderator Access**: Basic transaction support and dispute viewing
- **Financial Administrator**: Refund processing and financial reporting
- **Super Administrator**: All financial operations including dispute resolution

### app/Policies/InvoicePolicy.php

**Purpose**: Controls invoice management including generation, viewing, downloading, and archiving with tax compliance and financial reporting authorization.

**Compliance Integration**:
```php
use App\Services\Compliance\TaxComplianceService;
```

**Authorization Methods**:
- **viewAny()**: Invoice listing for financial administrators
- **view()**: Invoice access with ownership and administrative overrides
- **download()**: PDF invoice download with access logging
- **regenerate()**: Invoice regeneration for corrections and updates
- **archive()**: Invoice archiving for compliance and storage management
- **viewTaxInfo()**: Tax information access for compliance officers

---

## Affiliate System Policies

### app/Policies/AffiliatePolicy.php

**Purpose**: Manages affiliate program access including registration, commission tracking, payout requests, and performance analytics with fraud prevention.

**Fraud Prevention Integration**:
```php
use App\Services\Affiliate\FraudDetectionService;
```

**Authorization Methods**:
- **view()**: Affiliate dashboard access with identity verification
- **update()**: Profile updates with fraud detection and validation
- **viewCommissions()**: Commission history with accuracy verification
- **requestPayout()**: Payout requests with minimum thresholds and validation
- **viewAnalytics()**: Performance analytics for affiliate optimization
- **manageReferrals()**: Referral management with tracking and attribution

**Affiliate Progression System**:
- Affiliate capabilities expand with user level progression
- Higher levels unlock advanced analytics and increased commission rates
- Fraud prevention measures protect program integrity

### app/Policies/CommissionPolicy.php

**Purpose**: Controls commission management including earnings tracking, payout processing, dispute resolution, and financial reporting with anti-fraud measures.

**Compliance Integration**:
```php
use App\Services\Compliance\FinancialComplianceService;
```

**Authorization Methods**:
- **view()**: Commission access with ownership validation and audit trails
- **requestPayout()**: Payout initiation with compliance checks and validation
- **dispute()**: Dispute filing with evidence requirements and approval workflows
- **viewHistory()**: Historical commission data for analysis and reporting
- **generateReport()**: Commission reporting for tax and compliance purposes

---

## System & Media Policies

### app/Policies/MediaPolicy.php

**Purpose**: Manages comprehensive media file access including upload permissions, viewing rights, download authorization, and file management with storage quota enforcement.

**Spatie MediaLibrary Integration**:
```php
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use App\Services\Media\MediaService;
```

**Authorization Methods**:
- **view()**: Media access with ownership and sharing permissions
- **upload()**: File upload with quota validation and security scanning
- **download()**: File download with access logging and bandwidth management
- **delete()**: Media deletion with ownership verification and cascade protection
- **optimize()**: Media optimization for performance and storage efficiency
- **manageCollections()**: Media organization and collection management

**Storage Quota System**:
- Upload limits based on user progression levels
- Premium users receive expanded storage quotas
- Administrators have unlimited storage access

### app/Policies/ConfigurationPolicy.php

**Purpose**: Controls system configuration access including settings management, feature toggles, payment configurations, and system maintenance with administrator-level security.

**Super Administrator Restriction**:
```php
use App\Enums\RoleType;
```

**Authorization Methods**:
- **viewAny()**: Configuration listing for system administrators
- **view()**: Individual setting access with role validation
- **update()**: Configuration updates with change logging and validation
- **create()**: New configuration creation for system expansion
- **delete()**: Configuration removal with dependency checks
- **manageSystem()**: System-level operations for super administrators only

---

## Testing & Quality Assurance Integration

### Policy Testing Requirements
All policies integrate with testing requirements from TESTING_GUIDE.md:

- **Permission Testing**: Verification of Spatie Permission integration
- **Role Testing**: Role-based access control validation
- **Level Testing**: User progression access control testing
- **Ownership Testing**: Resource ownership validation
- **Edge Case Testing**: Boundary condition and error scenario testing

### Quality Gates
Policy implementations follow project quality standards:

- **Code Coverage**: Minimum 90% test coverage for all authorization logic
- **Static Analysis**: PHPStan level 8 compliance for type safety
- **Security Scanning**: Regular security audits for authorization vulnerabilities
- **Performance Testing**: Authorization decision performance benchmarking

---

## Authorization Best Practices

### Security Implementation
- **Principle of Least Privilege**: Users granted minimum necessary permissions
- **Defense in Depth**: Multiple authorization layers for sensitive operations
- **Audit Logging**: All authorization decisions logged for compliance
- **Permission Caching**: Efficient permission checking with cache invalidation

### Integration Standards
- **Consistent Authorization**: Uniform authorization patterns across all policies
- **Service Integration**: Proper integration with service layer authorization
- **Middleware Coordination**: Seamless coordination with authorization middleware
- **Event Integration**: Authorization events properly integrated with domain events

### Performance Optimization
- **Permission Caching**: Strategic caching of permission checks
- **Lazy Loading**: Efficient relationship loading for authorization decisions
- **Query Optimization**: Optimized database queries for permission verification
- **Memory Management**: Efficient memory usage during authorization processing
