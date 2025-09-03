# TESTING_GUIDE.md - Laravel Docu-Course Platform Testing Guide

## Overview

This document defines comprehensive testing standards for the Laravel 12.24.0 docu-course platform with modules marketplace. All AI-generated code must pass these testing requirements.

## Testing Philosophy

- **Test-Driven Development (TDD)** for all new features
- **Minimum 90% Code Coverage** across all modules
- **Automated Testing Pipeline** with continuous integration
- **Performance Testing** for scalability validation
- **Security Testing** for vulnerability assessment

## Testing Framework Updates

Enhanced testing with PHPUnit, Pest, and Laravel Dusk

## PHPUnit + Pest Configuration (New)

```
// phpunit.xml configuration
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="./vendor/phpunit/phpunit/phpunit.xsd"
         bootstrap="vendor/autoload.php"
         colors="true">
    <testsuites>
        <testsuite name="Unit">
            <directory suffix="Test.php">./tests/Unit</directory>
        </testsuite>
        <testsuite name="Feature">
            <directory suffix="Test.php">./tests/Feature</directory>
        </testsuite>
    </testsuites>
    <coverage>
        <include>
            <directory suffix=".php">./app</directory>
        </include>
    </coverage>
</phpunit>
```

```
// Pest configuration (tests/Pest.php)
<?php

uses(Tests\TestCase::class)->in('Feature');
uses(Tests\TestCase::class)->in('Unit');

// Custom Pest functions
function createUserWithLevel(int $level): User
{
    return User::factory()->create(['level' => $level]);
}

function createCourseWithEpisodes(int $episodeCount = 3): DocuCourse
{
    return DocuCourse::factory()
        ->has(Episode::factory()->count($episodeCount))
        ->create();
}

// Laravel Breeze authentication helpers
function actingAsUser($user = null)
{
    return test()->actingAs($user ?? User::factory()->create());
}
```

## Pest Test Examples (New)

### User Authentication Tests
```
// tests/Feature/Auth/AuthenticationTest.php
it('allows user to login with valid credentials', function () {
    $user = User::factory()->create([
        'email' => 'test@example.com',
        'password' => Hash::make('password123'),
    ]);

    $response = $this->post('/login', [
        'email' => 'test@example.com',
        'password' => 'password123',
    ]);

    $response->assertRedirect('/dashboard');
    $this->assertAuthenticatedAs($user);
});

it('redirects guest users to login', function () {
    $response = $this->get('/dashboard');
    
    $response->assertRedirect('/login');
});
```

### Sanctum API Tests
```
// tests/Feature/Api/CourseApiTest.php
it('returns courses for authenticated users', function () {
    $user = User::factory()->create();
    $courses = DocuCourse::factory()->count(5)->create();

    $response = $this->actingAs($user, 'sanctum')
        ->getJson('/api/v1/courses');

    $response->assertOk()
        ->assertJsonStructure([
            'data' => [
                '*' => ['id', 'title', 'description']
            ]
        ]);
});

it('requires authentication for premium courses', function () {
    $response = $this->getJson('/api/v1/courses/premium');
    
    $response->assertUnauthorized();
});
```

## Laravel Dusk Browser Testing (New)

```
// tests/Browser/CourseUploadTest.php
class CourseUploadTest extends DuskTestCase
{
    /** @test */
    public function admin_can_upload_course_zip_file()
    {
        $admin = User::factory()->admin()->create();
        
        $this->browse(function (Browser $browser) use ($admin) {
            $browser->loginAs($admin)
                    ->visit('/admin/courses/upload')
                    ->attach('course_file', storage_path('testing/sample-course.zip'))
                    ->type('title', 'Test Course')
                    ->select('category_id', '1')
                    ->select('language_id', '1')
                    ->press('Upload Course')
                    ->waitForText('Course uploaded successfully')
                    ->assertSee('Course uploaded successfully');
        });
    }

    /** @test */
    public function system_processes_episodes_fragmentation()
    {
        $author = User::factory()->create(['level' => 7]);
        
        $this->browse(function (Browser $browser) use ($author) {
            $browser->loginAs($author)
                    ->visit('/courses/create')
                    ->type('title', 'New Course')
                    ->type('description', 'Course description')
                    ->press('Create Course')
                    ->waitForLocation('/courses/*/episodes/create')
                    ->type('episode_title', 'Episode 1')
                    ->attach('content_file', storage_path('testing/episode.html'))
                    ->press('Process Episode')
                    ->waitForText('Episode processed and fragmented')
                    ->assertSee('Parts created: 3');
        });
    }
}
```

**Dusk Environment Setup**
- Install Chrome/Chromium for headless testing
- Configure .env.dusk.local for isolated test environment
- Run: `php artisan dusk:install`
- Execute: `php artisan dusk`

## Testing Structure (Updated)

```
tests/
├── Feature/               # Integration & API tests
│   ├── Auth/             # Laravel Breeze authentication
│   │   ├── LoginTest.php
│   │   ├── RegisterTest.php
│   │   └── PasswordResetTest.php
│   ├── Course/           # Course management
│   ├── Payment/          # Stripe & Binance payments
│   │   ├── StripePaymentTest.php
│   │   └── BinancePaymentTest.php
│   ├── User/             # User management
│   └── Affiliate/        # Affiliate system
├── Unit/                 # Isolated component tests
│   ├── Models/           # Eloquent model tests
│   │   ├── UserTest.php
│   │   └── DocuCourseTest.php
│   ├── Services/         # Business logic tests
│   │   ├── PaymentServiceTest.php
│   │   └── CourseServiceTest.php
│   └── Traits/           # Trait functionality tests
├── Browser/              # Laravel Dusk end-to-end tests
│   ├── CourseUploadTest.php
│   ├── PaymentFlowTest.php
│   └── UserJourneyTest.php
├── Integration/          # Third-party API tests
│   ├── BinanceApiTest.php
│   └── StripeWebhookTest.php
└── Benchmark/            # Performance tests
    └── PerformanceTest.php
```

## Spatie Package Testing (New)

```
// Testing Media Library
it('can upload and convert images', function () {
    $user = User::factory()->create();
    
    $file = UploadedFile::fake()->image('avatar.jpg', 1000, 1000);
    
    $user->addMediaFromRequest('avatar')
         ->toMediaCollection('avatars');
    
    expect($user->getMedia('avatars'))->toHaveCount(1);
    expect($user->getFirstMediaUrl('avatars', 'thumb'))->toContain('thumb');
});

// Testing Activity Log
it('logs user activities', function () {
    $user = User::factory()->create();
    
    activity()
        ->causedBy($user)
        ->log('User logged in');
    
    expect(\Spatie\Activitylog\Models\Activity::count())->toBe(1);
    expect(\Spatie\Activitylog\Models\Activity::first()->description)
        ->toBe('User logged in');
});

// Testing Permissions
it('can assign permissions to users', function () {
    $user = User::factory()->create();
    $permission = Permission::create(['name' => 'create courses']);
    
    $user->givePermissionTo('create courses');
    
    expect($user->hasPermissionTo('create courses'))->toBeTrue();
});
```

## Test Environment Setup (Enhanced)

### .env.testing Configuration
```
DB_CONNECTION=sqlite
DB_DATABASE=:memory:

CACHE_DRIVER=array
SESSION_DRIVER=array
QUEUE_CONNECTION=sync

# For Dusk tests
APP_URL=http://localhost:8000

# Disable external services
STRIPE_KEY=sk_test_fake
BINANCE_API_KEY=test_key
BINANCE_SECRET_KEY=test_secret

# Mail testing
MAIL_MAILER=array
```

### Database Seeding for Tests
```
// DatabaseSeeder for tests
public function run()
{
    // Essential data for testing
    Language::factory()->create([
        'code' => 'en', 
        'is_default' => true
    ]);
    
    Currency::factory()->create([
        'code' => 'USD', 
        'is_default' => true
    ]);
    
    UserLevel::factory()->count(14)->sequence(
        fn ($sequence) => ['level' => $sequence->index + 1]
    )->create();
    
    // Create permissions for testing
    Permission::create(['name' => 'create courses']);
    Permission::create(['name' => 'manage users']);
}
```

## Continuous Integration (Enhanced)

```
# .github/workflows/tests.yml
name: Tests

on: [push, pull_request]

jobs:
  tests:
    runs-on: ubuntu-latest
    
    services:
      mysql:
        image: mysql:8.0
        env:
          MYSQL_ROOT_PASSWORD: password
          MYSQL_DATABASE: testing
        options: --health-cmd="mysqladmin ping" --health-interval=10s
      
      redis:
        image: redis:6.0
        options: --health-cmd="redis-cli ping" --health-interval=10s

    steps:
      - uses: actions/checkout@v3
      
      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: 8.2
          extensions: mbstring, pdo, mysql, redis
          
      - name: Install dependencies
        run: |
          composer install --no-interaction --prefer-dist
          npm install
          npm run build
          
      - name: Run PHPUnit tests
        run: php artisan test --coverage --min=90
        
      - name: Run Pest tests
        run: ./vendor/bin/pest --coverage
        
      - name: Run Dusk tests
        run: php artisan dusk
        
      - name: Static Analysis
        run: ./vendor/bin/phpstan analyse
```

## Quality Gates (Enhanced)

### Automated Checks
- ✓ All tests must pass (100% success rate)
- ✓ Coverage above threshold (90% minimum)
- ✓ No critical security vulnerabilities
- ✓ Performance benchmarks met
- ✓ Code style compliance (Laravel Pint)
- ✓ Static analysis passes (PHPStan Level 8)

### Testing Commands
- `php artisan test` - PHPUnit tests
- `./vendor/bin/pest` - Pest tests
- `php artisan dusk` - Browser tests
- `./vendor/bin/phpstan analyse` - Static analysis
- `./vendor/bin/pint --test` - Code style

**Note:** All other sections from the original TESTING_GUIDE.md remain unchanged, including Coverage Requirements, Testing Standards, Security Testing, and Testing Best Practices. The updates focus on integrating the newly installed testing frameworks and packages.