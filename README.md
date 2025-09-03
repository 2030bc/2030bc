# AI Agent Team Directive: Laravel Docu-Course Platform (2030b.com)

**Project:** 2030b.com  
**Directive Version:** 2.0  
**Status:** ACTIVE - STRICT PROTOCOL ENFORCEMENT

## 1. Executive Summary & Operational Protocol

This document is the single source of truth for all AI agents generating code for the `2030b.com` Laravel codebase. The objective is to produce a **production-ready, error-free (0 errors), and perfectly consistent** system.

**🔒 Absolute Rule: Hallucination is Forbidden.** All generated code **MUST** be derived directly from the specifications in the linked MD files. If a requirement is not explicitly stated, the agent **MUST NOT** invent it. Instead, the agent **MUST** signal a requirement gap using the mandated comment format: '// REQUIREMENT_GAP: [Clear description of the missing specification]'.

**Protocol for All Agents:**
1. **RECEIVE TASK:** (e.g., "Generate Models for Step 5").
2. **CONSULT THIS INDEX:** Locate all required specification files for the task.
3. **DOWNLOAD & PARSE:** Access and read all linked MD files. Cross-reference them.
4. **GENERATE CODE:** Output code that **strictly adheres** to the downloaded specifications.
5. **VALIDATE OUTPUT:** Review generated code against this directive's rules before final submission. Ensure tests are included.

> **Update: Laravel Breeze & Package Integration**
> 
> The platform now uses Laravel Breeze with Blade templates for authentication, along with comprehensive Spatie package integration, payment processing via Stripe and Binance, and robust testing frameworks including PHPUnit and Pest.

## 2. Authoritative Specification Files (DOWNLOAD & PARSE)

The following files contain the absolute requirements. They are listed in order of precedence. **You must download and parse these URLs.**

### 2.1. Core Architecture & Process (Highest Precedence)

* **`PROJECT_GUIDE.md`** | **[DOWNLOAD](https://2030b.com/md/PROJECT_GUIDE.md)**
  * **Purpose:** The constitution of the project. Defines coding standards, naming conventions, and architectural patterns.
  * **Agent Action:** This file **OVERRULES** all others in matters of style and structure. **MUST** be consulted first for every task.

* **`PROJECT_PROCESS.md`** | **[DOWNLOAD](https://2030b.com/md/PROJECT_PROCESS.md)**  
  * **Purpose:** The master workflow and chronological playbook for code generation.
  * **Agent Action:** Refer to this to understand the current step's context, dependencies, and required output files.

* **`ARCHITECTURE_DECISIONS.md`** | **[DOWNLOAD](https://2030b.com/md/ARCHITECTURE_DECISIONS.md)**
  * **Purpose:** Rationale for key technical choices (Laravel, Packages, API design). Provides context.
  * **Agent Action:** Consult to understand the *intent* behind the architecture. Ensure generated code aligns with this philosophy.

### 2.2. Data Layer Specifications (Non-Negotiable)

* **`DATABASE_SCHEMA_V2.md`** | **[DOWNLOAD](https://2030b.com/md/DATABASE_SCHEMA_V2.md)**
  * **Purpose:** The single source of truth for all database structures (tables, columns, types, relationships).
  * **Agent Action:** **MANDATORY** for generating Migrations, Models, and any data access logic. Column names, types, and relationships defined here are **FINAL**.

* **`APP_MODEL.md`** | **[DOWNLOAD](https://2030b.com/md/APP_MODEL.md)**
  * **Purpose:** Specifies the exact properties, relationships, methods, and accessors for each Eloquent Model.
  * **Agent Action:** Generate Model classes **DIRECTLY** from this spec. **CROSS-REFERENCE** with `DATABASE_SCHEMA_V2.md`.

* **`APP_ENUM.md`** | **[DOWNLOAD](https://2030b.com/md/APP_ENUM.md)**
  * **Purpose:** Defines all type-safe PHP Enumerations (e.g., statuses, types, categories).
  * **Agent Action:** Generate PHP Enums exactly as listed. Use these enums in Models, DTOs, Services, and Policies where referenced.

* **`APP_DTO.md`** | **[DOWNLOAD](https://2030b.com/md/APP_DTO.md)**
  * **Purpose:** Defines all Data Transfer Objects (DTOs) for strict, validated data contracts between application layers.
  * **Agent Action:** Generate DTO classes with properties, types, and validation rules as specified. These are immutable data objects.

### 2.3. Implementation & Interface Guides

* **`PACKAGES.md`** | **[DOWNLOAD](https://2030b.com/md/PACKAGES.md)**
  * **Purpose:** Authoritative list of all approved Composer and NPM dependencies.
  * **Agent Action:** **ONLY** use classes, methods, and patterns from these approved packages. Do not assume a package is included.

> **Note: Installed Package Stack**
> 
> The following packages are confirmed installed and must be utilized:
> - Laravel Breeze (Blade templates with Tailwind CSS)
> - Laravel Sanctum (API authentication)
> - Spatie Permission (roles and permissions)
> - Spatie Media Library (file handling)
> - Spatie Activity Log (audit trails)
> - Stripe PHP SDK (traditional payments)
> - Binance Connector PHP (cryptocurrency payments)
> - Intervention Image (image processing)
> - Maatwebsite Excel (spreadsheet processing)
> - Barryvdh DomPDF (PDF generation)
> - Laravel Telescope (debugging)
> - PHPUnit & Pest (testing frameworks)

* **`API_GUIDE.md`** | **[DOWNLOAD](https://2030b.com/md/API_GUIDE.md)**
  * **Purpose:** The contract for all API endpoints (URLs, methods, request/response formats).
  * **Agent Action:** Generate Controllers, Form Requests, and API Resources to implement this contract **exactly**.

* **`FRONTEND_GUIDE.md`** | **[DOWNLOAD](https://2030b.com/md/FRONTEND_GUIDE.md)**
  * **Purpose:** Defines the frontend technology stack (e.g., Blade, Livewire, Inertia.js) and component structure.
  * **Agent Action:** Generate Blade components, Livewire classes, or Vue/React components strictly according to this spec.

### 2.4. Quality, Security, & Deployment

* **`TESTING_GUIDE.md`** | **[DOWNLOAD](https://2030b.com/md/TESTING_GUIDE.md)**
  * **Purpose:** Mandates the testing strategy, tools, and coverage requirements.
  * **Agent Action:** For every generated PHP class (Model, Service, Controller, etc.), you **MUST** also generate a corresponding **Unit or Feature test file**.

> **Update: Testing Framework Integration**
> 
> The platform includes both PHPUnit and Pest testing frameworks. All generated code must include comprehensive test coverage using these frameworks with proper factory and seeder integration.

* **`SECURITY_GUIDE.md`** | **[DOWNLOAD](https://2030b.com/md/SECURITY_GUIDE.md)**
  * **Purpose:** Security protocols and best practices (auth, validation, injection protection).
  * **Agent Action:** This is a **CHECKLIST**. Review all generated code against this guide. Implement mass assignment protection, auth checks, and input sanitization.

* **`PERFORMANCE_GUIDE.md`** | **[DOWNLOAD](https://2030b.com/md/PERFORMANCE_GUIDE.md)**
  * **Purpose:** Performance optimization rules (caching, eager loading, indexing).
  * **Agent Action:** Implement eager loading, caching, and indexing strategies as specified in all database-related code.

* **`MIGRATION_GUIDE.md`** | **[DOWNLOAD](https://2030b.com/md/MIGRATION_GUIDE.md)**
  * **Purpose:** Instructions for local (Windows) and production (2030b.com) environment setup.
  * **Agent Action:** Ensure generated code is compatible with both environments (e.g., file paths, filesystem considerations).

* **`DIRECTORY_STRUCTURE.md`** | **[DOWNLOAD](https://2030b.com/md/DIRECTORY_STRUCTURE.md)**
  * **Purpose:** Defines the local development project structure.

* **`PRODUCTION_DIRECTORY_STRUCTURE.md`** | **[DOWNLOAD](https://2030b.com/md/PRODUCTION_DIRECTORY_STRUCTURE.md)**
  * **Purpose:** Defines the production server (2030b.com) directory structure and permissions.
  * **Agent Action:** Place all generated files in the **exact paths** specified in these documents.

### 2.5. Business Logic & User Experience Context

* **`USER_JOURNEY_ANALYSIS.md`** | **[DOWNLOAD](https://2030b.com/md/USER_JOURNEY_ANALYSIS.md)**
  * **Purpose:** Defines user personas, their goals, and the flow of interactions with the platform. Provides crucial business context.
  * **Agent Action:** **CONSULT FOR CONTROLLERS, SERVICES, AND POLICIES.** This file provides the "why" behind the code. Use it to inform method logic, access control rules (`Policies`), and the sequence of operations within `Services`. If a journey requires a "preview" step, ensure the corresponding Service and Policy have the required methods.

## 3. Agent Output Requirements

1. **Consistency:** Code style **MUST** be unified according to `PROJECT_GUIDE.md` (naming, formatting, patterns).
2. **Completeness:** Generate all related files for a task (e.g., Model + Migration + Factory + Seeder + Unit Test).
3. **Correctness:** Code must be syntactically perfect, compile-ready, and must implement the specs with **0 errors**.
4. **Validation:** Integrate validation logic from `APP_DTO.md` and `SECURITY_GUIDE.md` into all relevant classes (Form Requests, DTOs).
5. **Tests:** For every generated PHP class, you **MUST** generate a corresponding test file as mandated by `TESTING_GUIDE.md`.
6. **Business Logic Alignment:** Cross-reference `USER_JOURNEY_ANALYSIS.md` to ensure generated code facilitates the intended user flows.

> **Update: Laravel Breeze Integration Requirements**
> 
> All authentication-related code must integrate with Laravel Breeze's Blade templates. Use Breeze's authentication controllers as base classes and extend with custom functionality. Ensure Tailwind CSS classes are compatible with the existing Breeze styling.

## 4. Error Handling & Reporting Protocol

All agents must implement a standardized, silent error handling approach to ensure robustness on `2030b.com`.

**In `App\Exceptions\Handler.php`:**
```php
public function register(): void
{
    $this->reportable(function (Throwable $e) {
        // Silently log all exceptions with full context for debugging on 2030b.com
        \Log::error('System Error: ' . $e->getMessage(), [
            'exception' => $e,
            'url' => request()?->fullUrl(),
            'user_id' => auth()?->id()
        ]);
    });

    // Render user-friendly responses for common errors
    $this->renderable(function (NotFoundHttpException $e, $request) {
        if ($request->expectsJson()) {
            return response()->json(['message' => 'The requested resource was not found.'], 404);
        }
    });

    $this->renderable(function (AuthenticationException $e, $request) {
        if ($request->expectsJson()) {
            return response()->json(['message' => 'Unauthenticated.'], 401);
        }
    });
}
```

**In Application Code:**
- Use try-catch blocks for predictable, recoverable errors (e.g., third-party API failures).
- Use validation failures for user input errors.
- Use policy denials for authorization errors.
- Let all other exceptions be handled by the global handler.

**// REQUIREMENT_GAP Protocol:**
**Any deviation from the linked specifications, or any identified missing requirement, must be reported immediately using the following comment format. Do not invent solutions.**

```php
// REQUIREMENT_GAP: [APP_DTO.md] does not specify the validation rule for the `user_profile.bio` field length (max: 500?).
// REQUIREMENT_GAP: [USER_JOURNEY_ANALYSIS.md] describes a refund process but no `RefundService` is defined in [APP_MODEL.md].
// REQUIREMENT_GAP: [APP_MODEL.md] defines a `Course::is_published` attribute but no matching column is in [DATABASE_SCHEMA_V2.md].
```

## 5. Task Assignment & Output Framework

Agents will be assigned tasks based on the phases in `PROJECT_PROCESS.md`. Each task will require generating a bundle of 10-30 interconnected files.

**Example Task Bundle: "Generate Authentication Layer"**
* **Input:** Phase from `PROJECT_PROCESS.md`
* **Specs Consulted:** `APP_MODEL.md` (User), `APP_ENUM.md` (UserRole), `DATABASE_SCHEMA_V2.md` (users table), `SECURITY_GUIDE.md`, `API_GUIDE.md` (/auth endpoints)
* **Expected Output Bundle:**
  * `app/Models/User.php`
  * `app/Enums/UserRole.php`
  * `app/Http/Controllers/AuthController.php`
  * `app/Http/Requests/Auth/LoginRequest.php`
  * `app/Http/Requests/Auth/RegisterRequest.php`
  * `app/Http/Resources/UserResource.php`
  * `app/Policies/UserPolicy.php`
  * `app/Services/AuthService.php`
  * `tests/Feature/Auth/LoginTest.php`
  * `tests/Feature/Auth/RegisterTest.php`
  * `database/factories/UserFactory.php`
  * `database/seeders/UserSeeder.php`

> **Update: Executed Migration Integration**
> 
> The following migrations have been executed and must be considered when generating related models and services:
> - `0001_01_01_000000_create_users_table.php` (Laravel/Breeze)
> - `0001_01_01_000001_create_cache_table.php` (Laravel)
> - `0001_01_01_000002_create_jobs_table.php` (Laravel)
> - `2025_08_29_211228_create_media_table.php` (Spatie MediaLibrary)
> - `2025_08_29_211330_create_permission_tables.php` (Spatie Permission)
> - `2025_08_29_211413_create_personal_access_tokens_table.php` (Laravel Sanctum)
> - `2025_08_29_211552_create_telescope_entries_table.php` (Laravel Telescope)
> - `2025_08_29_211842_create_activity_log_table.php` (Spatie ActivityLog)
> - All related Spatie package tables as documented in composer.json

## 6. Inter-Agent Communication Protocol

To maintain consistency when generating interconnected bundles, agents must adhere to these communication standards:

**1. Public Method Signatures:** When a service method is used across domains, its signature must be consistent.
```php
// In App\Services\PaymentService.php
/**
 * @param \App\DTOs\CreatePaymentData $paymentData
 * @return \App\Models\Payment
 * @throws \App\Exceptions\PaymentFailedException
 */
public function processSubscriptionPayment(CreatePaymentData $paymentData): Payment
{
    // Implementation...
}
```

**2. Shared DTOs:** DTOs defined in `APP_DTO.md` are immutable contracts. All agents must use them exactly as defined.

**3. Event Hooking:** Agents must leave clear hooks for future features based on `USER_JOURNEY_ANALYSIS.md`.
```php
// In App\Services\CourseService.php
public function publishCourse(Course $course): void
{
    $course->update(['status' => CourseStatus::PUBLISHED]);
    
    // HOOK: For future analytics integration
    // Event::dispatch(new CoursePublished($course));
    
    // REQUIREMENT_GAP: [USER_JOURNEY_ANALYSIS.md] mentions email notifications but no spec exists for NotificationService
}
```

## 7. Validation Checklist Before Submission

Every agent MUST run this checklist before submitting generated code:

- [ ] **✓ All generated files reference the correct specifications** (e.g., "Based on DATABASE_SCHEMA_V2.md v1.2")
- [ ] **✓ No coding style violations** (PSR-12, project-specific rules from `PROJECT_GUIDE.md`)
- [ ] **✓ All database columns from `DATABASE_SCHEMA_V2.md` are represented** in models/migrations
- [ ] **✓ All relationships from `APP_MODEL.md` are implemented** with correct types (`hasMany`, `belongsToMany`, etc.)
- [ ] **✓ All validation rules from `APP_DTO.md` are implemented** in Form Requests or DTOs
- [ ] **✓ All enums from `APP_ENUM.md` are implemented** and used correctly
- [ ] **✓ Security policies from `SECURITY_GUIDE.md` are implemented** in Policies and middleware
- [ ] **✓ Corresponding test files exist** for each generated class (`TESTING_GUIDE.md` compliance)
- [ ] **✓ Error handling follows the protocol** in Section 4
- [ ] **✓ All `REQUIREMENT_GAP` comments are properly formatted** and identify missing specs clearly
- [ ] **✓ Code compiles without syntax errors** (0 errors requirement)
- [ ] **✓ Directory structure matches `PRODUCTION_DIRECTORY_STRUCTURE.md`**
- [ ] **✓ Laravel Breeze integration is properly implemented**
- [ ] **✓ Spatie package integration follows best practices**
- [ ] **✓ Payment processing includes both Stripe and Binance support**

## 8. Deployment Readiness Verification

After all phases are complete, the final output must pass these production checks:

- [ ] **✓ Environment configurations work** for both local (Windows) and production (2030b.com) (`MIGRATION_GUIDE.md`)
- [ ] **✓ All database migrations are reversible** (rollback works without errors)
- [ ] **✓ Seeders create production-valid data** (no test emails in production seeders)
- [ ] **✓ Storage links are configured** for production (`PRODUCTION_DIRECTORY_STRUCTURE.md`)
- [ ] **✓ Cron job entries are defined** for scheduled tasks (`PROJECT_GUIDE.md`)
- [ ] **✓ Queue workers are configured** for asynchronous jobs
- [ ] **✓ API responses match `API_GUIDE.md` exactly** (status codes, JSON structure)
- [ ] **✓ All tests pass** (PHPUnit, Pest, Dusk as applicable)
- [ ] **✓ Performance benchmarks meet `PERFORMANCE_GUIDE.md` standards**
- [ ] **✓ Security scan shows no vulnerabilities** (`SECURITY_GUIDE.md` compliance)
- [ ] **✓ Laravel Breeze authentication flows work correctly**
- [ ] **✓ Spatie packages are properly configured and functional**
- [ ] **✓ Payment processing with Stripe and Binance is tested**

---

**🚀 DEPLOYMENT AUTHORIZED**

All agents have successfully completed their tasks according to the specifications. The codebase is now production-ready for deployment to **2030b.com**.

**Next Steps:**
1. Run final quality assurance: `scripts/quality-check.sh`
2. Execute deployment script: `scripts/deploy.sh`
3. Verify live functionality at https://2030b.com
4. Monitor error logs for any runtime issues
