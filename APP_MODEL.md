# APP_MODEL.md - Eloquent Models for Laravel Docu-Course Platform

This document details all Eloquent models in the platform, grouped by domain. For each model, it includes a description, relationships, and key methods. Use this as a reference for AI agents to generate and maintain models consistently.

---

## 👤 User System Models

### **app/Models/User.php**
- **Description:** Main user entity. Handles authentication, profile, progression, and relationships.
- **Relationships:**
  - hasOne: UserProfile, Affiliate
  - hasMany: UserProgress, UserNote, UserReaction, PaymentMethod, Transaction, Invoice, SocialShare
  - belongsTo: UserLevel, Language
  - hasManyThrough: Affiliate, Commission
- **Key Methods:** getRouteKeyName (uuid), hasPurchased, levelUp, isActive

### **app/Models/User/UserProfile.php**
- **Description:** Stores extended user profile data.
- **Relationships:**
  - belongsTo: User
- **Key Methods:** isPublic, showEmail

### **app/Models/User/UserLevel.php**
- **Description:** User progression levels and benefits.
- **Relationships:**
  - hasMany: User
- **Key Methods:** getBenefits

### **app/Models/User/UserProgress.php**
- **Description:** Tracks user progress in episodes and courses.
- **Relationships:**
  - belongsTo: User, Episode
- **Key Methods:** markComplete, updateProgress

### **app/Models/User/UserNote.php**
- **Description:** User notes on episodes.
- **Relationships:**
  - belongsTo: User, Episode
- **Key Methods:** isPublic

### **app/Models/User/UserReaction.php**
- **Description:** User reactions to episodes.
- **Relationships:**
  - belongsTo: User, Episode
- **Key Methods:** getType

---

## 📚 Course System Models

### **app/Models/Course/DocuCourse.php**
- **Description:** Main course entity, with metadata, pricing, and access control.
- **Relationships:**
  - hasMany: Episode, CourseUpload
  - belongsTo: Category, User (author), Language
  - hasManyThrough: EpisodePart
- **Key Methods:** isPublished, isFree, getEpisodes, getAssets

### **app/Models/Course/Episode.php**
- **Description:** Course episode, stores metadata and file path.
- **Relationships:**
  - belongsTo: DocuCourse
  - hasMany: EpisodePart, UserProgress, UserNote, UserReaction
- **Key Methods:** getContentPath, isAccessibleBy

### **app/Models/Course/EpisodePart.php**
- **Description:** Fragment of an episode for progressive reading.
- **Relationships:**
  - belongsTo: Episode
- **Key Methods:** getPartPath

### **app/Models/Course/Category.php**
- **Description:** Course category, supports hierarchy.
- **Relationships:**
  - hasMany: DocuCourse
  - belongsTo: Category (parent)
- **Key Methods:** getChildren, isActive

### **app/Models/Course/CourseUpload.php**
- **Description:** Tracks course file uploads and processing.
- **Relationships:**
  - belongsTo: User, DocuCourse
- **Key Methods:** isProcessed, getMetadata

---

## 💰 Payment System Models

### **app/Models/Payment/Currency.php**
- **Description:** Supported currencies (fiat, crypto, local, platform).
- **Relationships:**
  - hasMany: Transaction, Invoice
- **Key Methods:** getExchangeRate, isActive

### **app/Models/Payment/Transaction.php**
- **Description:** Payment transactions (purchase, refund, commission, etc.).
- **Relationships:**
  - belongsTo: User, Currency
- **Key Methods:** isCompleted, getProvider

### **app/Models/Payment/PaymentMethod.php**
- **Description:** User payment methods (card, crypto, bank, wallet).
- **Relationships:**
  - belongsTo: User
- **Key Methods:** isVerified, getProvider

### **app/Models/Payment/Invoice.php**
- **Description:** Invoices for user transactions.
- **Relationships:**
  - belongsTo: User, Transaction, Currency
- **Key Methods:** isPaid, getItems

---

## 🔗 Affiliate System Models

### **app/Models/Affiliate/Affiliate.php**
- **Description:** Affiliate profile and commission tracking.
- **Relationships:**
  - belongsTo: User
  - hasMany: ReferralCode, AffiliateClick, Commission
- **Key Methods:** getTotalEarnings, isActive

### **app/Models/Affiliate/ReferralCode.php**
- **Description:** Referral codes for tracking signups and purchases.
- **Relationships:**
  - belongsTo: Affiliate
  - hasMany: AffiliateClick
- **Key Methods:** isActive, getUsageCount

### **app/Models/Affiliate/AffiliateClick.php**
- **Description:** Tracks clicks on affiliate links.
- **Relationships:**
  - belongsTo: Affiliate, ReferralCode
  - hasMany: Conversion
- **Key Methods:** getLandingPage

### **app/Models/Affiliate/Conversion.php**
- **Description:** Conversion events (signup, purchase, subscription).
- **Relationships:**
  - belongsTo: AffiliateClick, User, Currency
  - hasOne: Commission
- **Key Methods:** isApproved, getAmount

### **app/Models/Affiliate/Commission.php**
- **Description:** Commission records for affiliates.
- **Relationships:**
  - belongsTo: Affiliate, Conversion, Currency
- **Key Methods:** isPayable, isPaid

### **app/Models/Affiliate/SocialShare.php**
- **Description:** Social sharing events for affiliate content.
- **Relationships:**
  - belongsTo: User
- **Key Methods:** getPlatform, getShareUrl

---

## 🌐 System Configuration Models

### **app/Models/System/Language.php**
- **Description:** Supported languages and configuration.
- **Relationships:**
  - hasMany: DocuCourse, User
- **Key Methods:** isActive, isRtl

### **app/Models/System/Configuration.php**
- **Description:** System configuration key-value pairs.
- **Relationships:**
  - none
- **Key Methods:** getValue, isPublic

### **app/Models/System/ActivityLog.php**
- **Description:** User and system activity logs.
- **Relationships:**
  - belongsTo: User
- **Key Methods:** getType, getSubject

### **app/Models/System/SystemMetric.php**
- **Description:** System metrics for analytics and monitoring.
- **Relationships:**
  - none
- **Key Methods:** getMetricType, getPeriod

---

## 📊 Analytics & Tracking Models

### **app/Models/Analytics/UserSession.php**
- **Description:** Tracks user sessions and device info.
- **Relationships:**
  - belongsTo: User
- **Key Methods:** isActive, getDeviceType

### **app/Models/Analytics/ContentView.php**
- **Description:** Tracks content views (course, episode).
- **Relationships:**
  - belongsTo: User
- **Key Methods:** getContentType, getDuration

### **app/Models/Analytics/EngagementMetric.php**
- **Description:** User engagement metrics (activity, value, period).
- **Relationships:**
  - belongsTo: User
- **Key Methods:** getMetricType, getValue

### **app/Models/Analytics/ConversionTracking.php**
- **Description:** Tracks conversion events for analytics.
- **Relationships:**
  - belongsTo: User
- **Key Methods:** getConversionType, getSource
 Laravel Breeze & Spatie Integration Updates
---

## 🔧 Package Integration Model Updates

> **Update:** Model enhancements for Laravel Breeze authentication, Spatie packages, Sanctum API, and additional installed packages.

### Enhanced User Model (app/Models/User.php)

#### Laravel Breeze Authentication Methods
```php
/**
 * Laravel Breeze authentication methods
 */
public function emailVerificationRequired(): bool
{
    return !$this->hasVerifiedEmail();
}

public function markEmailAsVerified(): bool
{
    return $this->forceFill([
        'email_verified_at' => $this->freshTimestamp(),
    ])->save();
}

public function sendEmailVerificationNotification(): void
{
    $this->notify(new VerifyEmail);
}

public function getEmailForVerification(): string
{
    return $this->email;
}
```

#### Laravel Sanctum API Methods
```php
/**
 * Laravel Sanctum API token methods
 */
public function createApiToken(string $name, array $abilities = ['*']): NewAccessToken
{
    return $this->createToken($name, $abilities);
}

public function revokeApiTokens(): void
{
    $this->tokens()->delete();
}

public function hasApiTokenPermission(string $ability): bool
{
    return $this->currentAccessToken()?->can($ability) ?? false;
}
```

#### Spatie Permission Integration
```php
/**
 * Spatie Permission trait integration
 */
use HasRoles;

public function assignDefaultRole(): void
{
    $this->assignRole('student');
}

public function canAccessCourse(DocuCourse $course): bool
{
    return $this->hasPermissionTo("access-course-{$course->access_level}") 
           || $this->level >= $course->required_user_level;
}

public function canManageCourse(DocuCourse $course): bool
{
    return $this->hasRole('admin') 
           || ($this->hasRole('author') && $this->id === $course->author_id);
}
```

#### Spatie Media Library Integration
```php
/**
 * Spatie MediaLibrary trait integration
 */
use HasMedia;

public function registerMediaCollections(): void
{
    $this->addMediaCollection('avatar')
          ->singleFile()
          ->acceptsMimeTypes(['image/jpeg', 'image/png']);
          
    $this->addMediaCollection('documents')
          ->acceptsMimeTypes(['application/pdf']);
}

public function registerMediaConversions(Media $media = null): void
{
    $this->addMediaConversion('thumb')
          ->width(150)
          ->height(150)
          ->sharpen(10);
          
    $this->addMediaConversion('preview')
          ->width(500)
          ->height(500)
          ->nonOptimized();
}

public function getAvatarUrl(): string
{
    return $this->getFirstMediaUrl('avatar', 'thumb') 
           ?: asset('images/default-avatar.png');
}
```

#### Spatie Activity Log Integration
```php
/**
 * Spatie ActivityLog trait integration
 */
use LogsActivity;

protected static $logAttributes = [
    'first_name', 'last_name', 'email', 'level', 'points'
];

protected static $logOnlyDirty = true;

public function getDescriptionForEvent(string $eventName): string
{
    return "User has been {$eventName}";
}
```

### Enhanced DocuCourse Model (app/Models/Course/DocuCourse.php)

#### Spatie Media Library for Course Assets
```php
use HasMedia;

public function registerMediaCollections(): void
{
    $this->addMediaCollection('cover')
          ->singleFile()
          ->acceptsMimeTypes(['image/jpeg', 'image/png']);
          
    $this->addMediaCollection('assets')
          ->acceptsMimeTypes(['image/jpeg', 'image/png', 'application/pdf']);
}

public function getCoverImageUrl(): string
{
    return $this->getFirstMediaUrl('cover') 
           ?: asset('images/default-course-cover.jpg');
}
```

#### Spatie Translatable Integration
```php
use HasTranslations;

protected $translatable = ['title', 'description', 'short_description'];

public function getTitleInLanguage(string $locale): string
{
    return $this->getTranslation('title', $locale);
}
```

### Enhanced Transaction Model (app/Models/Payment/Transaction.php)

#### Stripe Integration Methods
```php
public function createStripePaymentIntent(): PaymentIntent
{
    $stripe = new StripeClient(config('services.stripe.secret'));
    
    return $stripe->paymentIntents->create([
        'amount' => $this->amount * 100, // Convert to cents
        'currency' => $this->currency->code,
        'metadata' => [
            'transaction_id' => $this->uuid,
            'user_id' => $this->user_id,
        ],
    ]);
}
```

#### Binance Integration Methods
```php
public function createBinanceOrder(): array
{
    $binanceService = app(BinanceService::class);
    
    return $binanceService->createOrder([
        'symbol' => $this->currency->code . 'USDT',
        'side' => 'BUY',
        'type' => 'MARKET',
        'quantity' => $this->amount,
    ]);
}
```

### New Package-Specific Models

#### **app/Models/System/PersonalAccessToken.php**
- **Description:** Laravel Sanctum personal access tokens
- **Relationships:**
  - morphsTo: tokenable (User)
- **Key Methods:** findToken, can, cant

#### **app/Models/System/Permission.php** (Spatie)
- **Description:** System permissions for role-based access
- **Relationships:**
  - belongsToMany: roles, users
- **Key Methods:** create, findByName

#### **app/Models/System/Role.php** (Spatie)
- **Description:** User roles with permissions
- **Relationships:**
  - belongsToMany: permissions, users
- **Key Methods:** givePermissionTo, hasPermissionTo

#### **app/Models/Media/Media.php** (Spatie)
- **Description:** Media files attached to models
- **Relationships:**
  - morphsTo: model
- **Key Methods:** getUrl, getPath, toHtml

### Model Factory Enhancements

#### Enhanced User Factory with Package Support
```php
public function definition(): array
{
    return [
        'uuid' => $this->faker->uuid(),
        'first_name' => $this->faker->firstName(),
        'last_name' => $this->faker->lastName(),
        'email' => $this->faker->unique()->safeEmail(),
        'email_verified_at' => now(),
        'password' => Hash::make('password'),
        'level' => $this->faker->numberBetween(1, 14),
        'points' => $this->faker->numberBetween(0, 10000),
        'preferred_language' => 'en',
        'referral_code' => strtoupper($this->faker->bothify('????####')),
        'remember_token' => Str::random(10),
    ];
}

public function unverified(): Factory
{
    return $this->state(fn (array $attributes) => [
        'email_verified_at' => null,
    ]);
}

public function withRole(string $role): Factory
{
    return $this->afterCreating(function (User $user) use ($role) {
        $user->assignRole($role);
    });
}

public function withAvatar(): Factory
{
    return $this->afterCreating(function (User $user) {
        $user->addMediaFromUrl('https://via.placeholder.com/300')
             ->toMediaCollection('avatar');
    });
}
```

---

> **Note:** All models now include proper integration with Laravel Breeze authentication, Sanctum API tokens, Spatie packages (Permissions, Media Library, Activity Log, Translatable), and payment gateway integrations (Stripe, Binance). Existing model functionality remains unchanged while being enhanced with package-specific features.