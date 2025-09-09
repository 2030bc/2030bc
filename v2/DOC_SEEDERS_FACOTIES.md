# DOC_SEEDERS_FACOTIES.md

## Overview
Database seeders and factories for populating initial data, test data, and demo content with proper relationships and realistic fake data generation.

## Architecture Principles
- **Dependency Order**: Seeders respect foreign key constraints and dependencies
- **Environment Awareness**: Different seed data for testing, staging, and production
- **Factory Integration**: Leverage model factories for consistent fake data
- **Relationship Integrity**: Maintain proper model relationships in seeded data
- **Idempotency**: Seeders can be run multiple times safely

---

## Database Seeders

### database/seeders/DatabaseSeeder.php
**Purpose**: Main seeder orchestrator that calls all other seeders in proper dependency order.

**Dependencies**:
```php
use Illuminate\Database\Seeder;
use Database\Seeders\LanguageSeeder;
use Database\Seeders\CurrencySeeder;
use Database\Seeders\UserLevelSeeder;
use Database\Seeders\CategorySeeder;
use Database\Seeders\RoleAndPermissionSeeder;
use Database\Seeders\ConfigurationSeeder;
use Database\Seeders\AdminUserSeeder;
use Database\Seeders\TestSeeder;
use Database\Seeders\DemoContentSeeder;
```

**Methods**:
- run(): void
- seedProductionData(): void
- seedTestData(): void
- seedDemoData(): void

**Do**:
- Maintain proper seeding order based on dependencies
- Check environment before running specific seeders
- Implement rollback mechanisms for failed seeding
- Use transactions for data consistency

**Don't**:
- Seed in random order causing constraint violations
- Run demo data in production environment
- Skip environment validation

---

### database/seeders/LanguageSeeder.php
**Purpose**: Seed supported languages with proper locale configuration and RTL support.

**Dependencies**:
```php
use Illuminate\Database\Seeder;
use App\Models\System\Language;
use App\Enums\LanguageDirection;
```

**Methods**:
- run(): void
- getLanguageData(): array
- seedDefaultLanguages(): void
- seedRTLLanguages(): void

**Do**:
- Include comprehensive language metadata
- Set proper RTL flags for Arabic languages
- Use standard ISO language codes
- Mark default language as active

**Don't**:
- Seed languages without proper locale data
- Skip RTL configuration for supported languages
- Use non-standard language codes

---

### database/seeders/CurrencySeeder.php
**Purpose**: Seed system currencies including fiat, crypto, and platform-specific currencies.

**Dependencies**:
```php
use Illuminate\Database\Seeder;
use App\Models\Payment\Currency;
use App\Enums\CurrencyType;
```

**Methods**:
- run(): void
- seedFiatCurrencies(): void
- seedCryptoCurrencies(): void
- seedPlatformCurrencies(): void
- getExchangeRates(): array

**Do**:
- Include major fiat currencies (USD, EUR, GBP, JPY)
- Add popular cryptocurrencies (BTC, ETH, USDT, BNB)
- Seed custom platform currencies (CTC, EVO, NOOR, etc.)
- Set realistic exchange rates

**Don't**:
- Seed currencies without exchange rate data
- Use hardcoded exchange rates for production
- Skip validation for currency symbols

---

### database/seeders/UserLevelSeeder.php
**Purpose**: Seed the 14-level user progression system with point requirements and benefits.

**Dependencies**:
```php
use Illuminate\Database\Seeder;
use App\Models\User\UserLevel;
```

**Methods**:
- run(): void
- getLevelData(): array
- calculatePointRequirements(): array
- getBenefitsForLevel(): array

**Do**:
- Use algorithmic point progression (exponential growth)
- Define meaningful benefits for each level
- Include badge images for level visualization
- Implement proper level naming convention

**Don't**:
- Use linear point progression
- Leave levels without defined benefits
- Skip level validation rules

---

### database/seeders/CategorySeeder.php
**Purpose**: Seed course categories with hierarchical structure and proper nesting.

**Dependencies**:
```php
use Illuminate\Database\Seeder;
use App\Models\Course\Category;
```

**Methods**:
- run(): void
- getCategoryTree(): array
- seedRootCategories(): void
- seedSubCategories(): void
- generateSlug(): string

**Do**:
- Create logical category hierarchies
- Generate SEO-friendly category slugs
- Set proper sort orders for category display
- Include category descriptions

**Don't**:
- Create circular category references
- Use duplicate category slugs
- Skip parent-child relationship validation

---

### database/seeders/RoleAndPermissionSeeder.php
**Purpose**: Seed Spatie roles and permissions with proper hierarchical permission structure.

**Dependencies**:
```php
use Illuminate\Database\Seeder;
use Spatie\Permission\Models\Role;
use Spatie\Permission\Models\Permission;
use App\Enums\RoleType;
use App\Enums\PermissionType;
```

**Methods**:
- run(): void
- createRoles(): void
- createPermissions(): void
- assignPermissionsToRoles(): void
- getPermissionHierarchy(): array

**Do**:
- Create hierarchical permission structure
- Assign permissions based on role responsibilities
- Use descriptive permission names
- Implement proper permission inheritance

**Don't**:
- Create overlapping permission scopes
- Assign excessive permissions to basic roles
- Skip permission validation

---

### database/seeders/ConfigurationSeeder.php
**Purpose**: Seed system configuration values with proper type casting and grouping.

**Dependencies**:
```php
use Illuminate\Database\Seeder;
use App\Models\System\Configuration;
use App\Enums\ConfigType;
```

**Methods**:
- run(): void
- getDefaultConfigurations(): array
- seedPaymentConfig(): void
- seedCourseConfig(): void
- seedAffiliateConfig(): void

**Do**:
- Group related configurations logically
- Use proper type casting for configuration values
- Set sensible default values
- Mark public vs. private configurations

**Don't**:
- Store sensitive data in public configurations
- Use hardcoded values without environment consideration
- Skip configuration validation

---

### database/seeders/AdminUserSeeder.php
**Purpose**: Create initial admin user with full permissions for system management.

**Dependencies**:
```php
use Illuminate\Database\Seeder;
use App\Models\User;
use App\Models\User\UserProfile;
use Illuminate\Support\Facades\Hash;
use Spatie\Permission\Models\Role;
```

**Methods**:
- run(): void
- createSuperAdmin(): User
- createAdminUsers(): void
- assignPermissions(): void

**Do**:
- Use strong default passwords
- Create complete user profiles
- Assign appropriate admin roles
- Log admin user creation

**Don't**:
- Use weak default passwords
- Skip email verification for admin users
- Create admin users without proper role assignment

---

### database/seeders/TestSeeder.php
**Purpose**: Generate comprehensive test data for development and testing environments.

**Dependencies**:
```php
use Illuminate\Database\Seeder;
use App\Models\User;
use App\Models\Course\DocuCourse;
use App\Models\Course\Episode;
use App\Models\Affiliate\Affiliate;
use App\Models\Payment\Transaction;
```

**Methods**:
- run(): void
- createTestUsers(): void
- createTestCourses(): void
- createTestTransactions(): void
- createTestAffiliates(): void

**Do**:
- Create realistic test scenarios
- Generate data for all user levels
- Include edge cases and boundary conditions
- Create proper relationship chains

**Don't**:
- Generate unrealistic test data
- Skip relationship integrity
- Create test data in production environment

---

### database/seeders/DemoContentSeeder.php
**Purpose**: Create demo courses and content for platform demonstration and onboarding.

**Dependencies**:
```php
use Illuminate\Database\Seeder;
use App\Models\Course\DocuCourse;
use App\Models\Course\Episode;
use App\Models\Course\Category;
use App\Models\User;
```

**Methods**:
- run(): void
- createDemoCourses(): void
- createDemoEpisodes(): void
- createDemoUsers(): void
- assignDemoProgress(): void

**Do**:
- Create engaging demo content
- Include courses of varying difficulty levels
- Generate realistic user progress data
- Create demo affiliate relationships

**Don't**:
- Use copyrighted content for demos
- Create demo content without proper categorization
- Skip demo user progress tracking

---

## Model Factories

### database/factories/UserFactory.php
**Purpose**: Generate realistic user data with proper relationships and varying user levels.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Factories\Factory;
use App\Models\User;
use App\Models\User\UserLevel;
use App\Enums\UserStatus;
use App\Enums\Gender;
use App\Enums\TextDirection;
```

**Methods**:
- definition(): array
- active(): static
- inactive(): static
- withLevel(): static
- withProfile(): static
- withReferrer(): static

**Do**:
- Generate diverse, realistic user data
- Create users across all levels
- Include proper referral relationships
- Generate realistic point distributions

**Don't**:
- Use predictable or sequential data
- Generate users without proper status
- Skip relationship factory methods

---

### database/factories/DocuCourseFactory.php
**Purpose**: Generate comprehensive course data with proper categorization and pricing.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Factories\Factory;
use App\Models\Course\DocuCourse;
use App\Models\Course\Category;
use App\Models\User;
use App\Models\System\Language;
use App\Models\Payment\Currency;
use App\Enums\CourseStatus;
use App\Enums\ContentAccessLevel;
use App\Enums\DifficultyLevel;
```

**Methods**:
- definition(): array
- published(): static
- draft(): static
- free(): static
- premium(): static
- withCategory(): static
- withAuthor(): static

**Do**:
- Generate realistic course metadata
- Create courses across all access levels
- Include proper SEO metadata
- Generate realistic pricing

**Don't**:
- Create courses without proper categorization
- Generate invalid price/currency combinations
- Skip course status validation

---

### database/factories/EpisodeFactory.php
**Purpose**: Generate episode content with proper course relationships and access control.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Factories\Factory;
use App\Models\Course\Episode;
use App\Models\Course\DocuCourse;
use App\Enums\CourseStatus;
use App\Enums\ContentAccessLevel;
```

**Methods**:
- definition(): array
- forCourse(): static
- published(): static
- withParts(): static
- longEpisode(): static
- shortEpisode(): static

**Do**:
- Generate realistic reading time estimates
- Create proper sort order sequences
- Include varied episode lengths
- Generate realistic file paths

**Don't**:
- Create episodes without course association
- Generate invalid sort orders
- Skip access level inheritance validation

---

### database/factories/AffiliateFactory.php
**Purpose**: Generate affiliate data with proper user relationships and commission structures.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Factories\Factory;
use App\Models\Affiliate\Affiliate;
use App\Models\User;
use App\Enums\AffiliateStatus;
```

**Methods**:
- definition(): array
- active(): static
- withReferralCodes(): static
- withCommissions(): static
- highPerformer(): static

**Do**:
- Generate realistic commission rates
- Create proper user relationships
- Include varied performance levels
- Generate realistic earnings data

**Don't**:
- Create affiliates without user association
- Generate unrealistic commission rates
- Skip affiliate status validation

---

### database/factories/TransactionFactory.php
**Purpose**: Generate financial transaction data with proper provider integration and status workflows.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Factories\Factory;
use App\Models\Payment\Transaction;
use App\Models\User;
use App\Models\Payment\Currency;
use App\Enums\TransactionType;
use App\Enums\PaymentStatus;
use App\Enums\PaymentProvider;
```

**Methods**:
- definition(): array
- completed(): static
- pending(): static
- failed(): static
- forProvider(): static
- withMetadata(): static

**Do**:
- Generate realistic transaction amounts
- Include proper provider metadata
- Create realistic status progressions
- Generate valid provider transaction IDs

**Don't**:
- Generate transactions without proper currency
- Use unrealistic transaction amounts
- Skip provider-specific metadata

---

### database/factories/MediaFactory.php
**Purpose**: Generate media file data for Spatie MediaLibrary integration with proper file associations.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Factories\Factory;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use App\Enums\MediaCollection;
use App\Enums\FileType;
```

**Methods**:
- definition(): array
- image(): static
- video(): static
- document(): static
- forCollection(): static

**Do**:
- Generate realistic file metadata
- Include proper MIME type associations
- Create varied file sizes
- Generate realistic file paths

**Don't**:
- Generate media without proper collection assignment
- Use invalid MIME types
- Skip file size validation

---

### database/factories/CategoryFactory.php
**Purpose**: Generate hierarchical category data with proper parent-child relationships.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Factories\Factory;
use App\Models\Course\Category;
```

**Methods**:
- definition(): array
- root(): static
- withParent(): static
- withChildren(): static
- active(): static

**Do**:
- Generate SEO-friendly category slugs
- Create realistic category hierarchies
- Include proper sort orders
- Generate descriptive category content

**Don't**:
- Create circular category references
- Generate duplicate slugs
- Skip hierarchy validation

---

### database/factories/CurrencyFactory.php
**Purpose**: Generate currency data for multi-currency testing with proper exchange rates.

**Dependencies**:
```php
use Illuminate\Database\Eloquent\Factories\Factory;
use App\Models\Payment\Currency;
use App\Enums\CurrencyType;
```

**Methods**:
- definition(): array
- fiat(): static
- crypto(): static
- platform(): static
- active(): static

**Do**:
- Generate realistic exchange rates
- Include proper currency symbols
- Create varied decimal precision
- Generate valid currency codes

**Don't**:
- Generate invalid currency codes
- Use unrealistic exchange rates
- Skip currency type validation

---

## Best Practices Summary

### Model Development Guidelines
1. **Always implement UUID+ID dual strategy** for external security and internal performance
2. **Use soft deletes** for critical business entities (users, courses, transactions)
3. **Implement proper enum casting** for TINYINT status fields
4. **Use Spatie traits** for permissions, media, and activity logging
5. **Define comprehensive relationships** with proper foreign key constraints
6. **Implement proper scopes** for common query patterns
7. **Use accessors and mutators** for data formatting and validation

### Seeder Development Guidelines
1. **Maintain proper dependency order** in seeding operations
2. **Use environment-aware seeding** for different deployment contexts
3. **Leverage model factories** for consistent fake data generation
4. **Implement idempotent seeders** that can be run multiple times
5. **Create realistic test scenarios** with proper edge cases
6. **Maintain relationship integrity** in seeded data
7. **Use transactions** for data consistency during seeding
