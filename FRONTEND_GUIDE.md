# FRONTEND_GUIDE.md
# Laravel Docu-Course Platform Frontend Guide

## Overview
This document defines comprehensive frontend architecture and component standards for the Laravel 12.24.0 docu-course platform, focusing on multi-language support, RTL/LTR layouts, and responsive design.

## Frontend Architecture

### Technology Stack
- **Framework**: Laravel Blade Templates
- **CSS Framework**: Tailwind CSS 3.x with RTL support
- **JavaScript**: Vanilla JS + Alpine.js for reactivity
- **Icons**: Font Awesome 6.x
- **Fonts**: Inter (primary), JetBrains Mono (code)
- **Build Tool**: Laravel Mix / Vite
- **Multi-language**: Laravel Localization + Custom RTL handling

### Project Structure
```
resources/
├── css/
│   ├── app.css           # Main stylesheet
│   ├── rtl.css           # RTL-specific styles
│   └── components.css    # Component-specific styles
├── js/
│   ├── app.js           # Main JavaScript
│   ├── components/      # Component scripts
│   ├── utils/           # Utility functions
│   └── plugins/         # Third-party integrations
├── views/
│   ├── layouts/         # Base layouts
│   ├── components/      # Reusable components
│   ├── pages/           # Page-specific views
│   └── partials/        # Small reusable parts
└── lang/
    ├── en/              # English translations
    ├── ar/              # Arabic translations (RTL)
    ├── fr/              # French translations
    └── es/              # Spanish translations
```

## Multi-Language & RTL Support

### Language Detection & Switching
```php
// Language detection middleware
class DetectLanguageMiddleware
{
    public function handle($request, Closure $next)
    {
        $language = $this->detectLanguage($request);
        
        App::setLocale($language->code);
        
        // Set RTL direction for supported languages
        $rtlLanguages = ['ar', 'he', 'fa', 'ur'];
        view()->share('isRTL', in_array($language->code, $rtlLanguages));
        view()->share('currentLanguage', $language);
        
        return $next($request);
    }
    
    private function detectLanguage($request)
    {
        // Priority: URL parameter > User preference > Browser > Default
        $languageCode = $request->get('lang') 
                       ?? auth()->user()?->preferred_language 
                       ?? $this->getBrowserLanguage($request)
                       ?? config('app.locale');
        
        return Language::where('code', $languageCode)
                      ->where('is_active', true)
                      ->firstOr(fn() => Language::where('is_default', true)->first());
    }
}
```

### Package Integration Notes

> **Note:** Frontend architecture extended with Laravel Breeze Blade components:

#### Laravel Breeze Integration
- **Authentication Views**: `/resources/views/auth/` - Login, register, forgot password forms
- **Dashboard Layout**: `/resources/views/dashboard.blade.php` - Authenticated user dashboard
- **Profile Management**: `/resources/views/profile/` - User profile editing components
- **Blade Components**: `/resources/views/components/` - Reusable form elements

#### Alpine.js Components
```javascript
// resources/js/app.js - Extended with Alpine.js
import Alpine from 'alpinejs'
window.Alpine = Alpine
Alpine.start()

// Custom Alpine.js components for course platform
document.addEventListener('alpine:init', () => {
    Alpine.data('coursePlayer', () => ({
        currentEpisode: null,
        progress: 0,
        isPlaying: false,
        // Course player functionality
    }))
    
    Alpine.data('paymentModal', () => ({
        open: false,
        provider: 'stripe',
        // Payment modal functionality
    }))
})
```

#### Tailwind CSS Configuration
```javascript
// tailwind.config.js - Updated for platform needs
module.exports = {
  content: [
    './resources/**/*.blade.php',
    './resources/**/*.js',
    './resources/**/*.vue',
  ],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
        }
      }
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
  ],
}
```

### Course Player Component
```php
// resources/views/components/course/player.blade.php
@props(['episode', 'currentPart' => null])

<div x-data="episodePlayer()" class="bg-white rounded-xl shadow-lg overflow-hidden">
    <!-- Episode Header -->
    <div class="bg-gradient-to-r from-blue-600 to-purple-600 text-white p-6">
        <div class="flex items-center justify-between">
            <div class="{{ $isRTL ? 'text-right' : 'text-left' }}">
                <h1 class="text-2xl font-bold mb-2">{{ $episode->title }}</h1>
                <p class="text-blue-100">{{ $episode->course->title }}</p>
            </div>
            
            <!-- Reading Progress -->
            <div class="text-center">
                <div class="text-3xl font-bold" x-text="readingProgress + '%'">0%</div>
                <div class="text-xs text-blue-200">{{ __('Progress') }}</div>
            </div>
        </div>
        
        <!-- Progress Bar -->
        <div class="mt-4">
            <div class="w-full bg-blue-500 bg-opacity-30 rounded-full h-2">
                <div class="bg-white h-2 rounded-full transition-all duration-300" 
                     :style="`width: ${readingProgress}%`"></div>
            </div>
        </div>
    </div>
    
    <!-- Episode Content -->
    <div class="relative">
        <div x-show="!loading" 
             class="prose max-w-none p-8 {{ $isRTL ? 'prose-rtl' : '' }}"
             :class="{ 'dark:prose-invert bg-gray-900 text-white': darkMode }"
             x-html="currentPartContent">
        </div>
    </div>
</div>
```

### Spatie Media Library Integration
```php
// Media upload component with Spatie Media Library
// resources/views/components/media/upload.blade.php
@props(['model', 'collection' => 'default'])

<div x-data="mediaUpload()" class="border-2 border-dashed border-gray-300 rounded-lg p-6">
    <input type="file" 
           x-ref="fileInput" 
           @change="handleFiles($event.target.files)"
           multiple 
           accept="image/*,video/*,audio/*,application/pdf"
           class="hidden">
    
    <div class="text-center">
        <svg class="mx-auto h-12 w-12 text-gray-400" stroke="currentColor" fill="none" viewBox="0 0 48 48">
            <path d="M28 8H12a4 4 0 00-4 4v20m32-12v8m0 0v8a4 4 0 01-4 4H12a4 4 0 01-4-4v-4m32-4l-3.172-3.172a4 4 0 00-5.656 0L28 28M8 32l9.172-9.172a4 4 0 015.656 0L28 28m0 0l4 4m4-24h8m-4-4v8m-12 4h.02" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
        </svg>
        <div class="mt-4">
            <button @click="$refs.fileInput.click()" 
                    class="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700">
                {{ __('Choose Files') }}
            </button>
            <p class="mt-2 text-sm text-gray-500">{{ __('or drag and drop files here') }}</p>
        </div>
    </div>
    
    <!-- File Preview -->
    <div x-show="files.length > 0" class="mt-4 grid grid-cols-2 md:grid-cols-4 gap-4">
        <template x-for="file in files" :key="file.id">
            <div class="relative bg-gray-100 rounded-lg p-2">
                <img :src="file.preview" :alt="file.name" class="w-full h-20 object-cover rounded">
                <p class="text-xs text-gray-600 mt-1 truncate" x-text="file.name"></p>
                <button @click="removeFile(file.id)" 
                        class="absolute -top-2 -right-2 bg-red-500 text-white rounded-full w-5 h-5 text-xs">
                    ×
                </button>
            </div>
        </template>
    </div>
</div>

<script>
function mediaUpload() {
    return {
        files: [],
        
        handleFiles(fileList) {
            Array.from(fileList).forEach(file => {
                const fileObj = {
                    id: Math.random().toString(36).substring(7),
                    name: file.name,
                    file: file,
                    preview: URL.createObjectURL(file)
                }
                this.files.push(fileObj)
            })
        },
        
        removeFile(id) {
            this.files = this.files.filter(file => file.id !== id)
        }
    }
}
</script>
```

### Package-Specific Frontend Components

#### Spatie Permission Integration
```php
// resources/views/components/permission/role-selector.blade.php
@props(['user', 'roles'])

<div x-data="{ selectedRoles: {{ json_encode($user->roles->pluck('name')) }} }">
    <h3 class="text-lg font-medium mb-4">{{ __('User Roles') }}</h3>
    
    @foreach($roles as $role)
        <label class="flex items-center mb-2">
            <input type="checkbox" 
                   value="{{ $role->name }}"
                   x-model="selectedRoles"
                   class="rounded border-gray-300 text-blue-600 shadow-sm focus:border-blue-300 focus:ring focus:ring-blue-200">
            <span class="ml-2">{{ $role->name }}</span>
            <span class="ml-auto text-sm text-gray-500">{{ $role->permissions->count() }} permissions</span>
        </label>
    @endforeach
</div>
```

## Performance Optimization

### Image Optimization with Spatie
```php
// Image optimization service integration
class ImageOptimizationService
{
    public function optimizeImage(UploadedFile $image): string
    {
        $intervention = Image::make($image->getPathname());
        
        // Resize for different screen sizes
        $sizes = [
            'thumbnail' => [300, 200],
            'medium' => [600, 400],
            'large' => [1200, 800],
        ];
        
        foreach ($sizes as $size => [$width, $height]) {
            $resized = $intervention->fit($width, $height, function ($constraint) {
                $constraint->upsize();
            });
            
            $filename = $this->generateFilename($image, $size);
            $path = storage_path("app/public/images/{$size}/{$filename}");
            
            // Optimize and save with Spatie Image Optimizer
            $resized->encode('webp', 85)->save($path);
        }
        
        return $filename;
    }
}
```

### Lazy Loading Implementation
```javascript
// Intersection Observer for lazy loading
class LazyLoader {
    constructor() {
        this.imageObserver = new IntersectionObserver(this.onIntersection.bind(this));
        this.init();
    }
    
    init() {
        const lazyImages = document.querySelectorAll('img[data-src]');
        lazyImages.forEach(img => this.imageObserver.observe(img));
    }
    
    onIntersection(entries) {
        entries.forEach(entry => {
            if (entry.isIntersecting) {
                const img = entry.target;
                img.src = img.dataset.src;
                img.classList.remove('lazy');
                img.classList.add('loaded');
                this.imageObserver.unobserve(img);
            }
        });
    }
}

document.addEventListener('DOMContentLoaded', () => {
    new LazyLoader();
});
```

### Laravel Breeze Authentication Components

#### Login Form Component
```php
// resources/views/components/auth/login-form.blade.php
<form method="POST" action="{{ route('login') }}" class="space-y-6">
    @csrf
    
    <!-- Email Address -->
    <div>
        <x-input-label for="email" :value="__('Email')" />
        <x-text-input id="email" 
                      class="block mt-1 w-full" 
                      type="email" 
                      name="email" 
                      :value="old('email')" 
                      required 
                      autofocus 
                      autocomplete="username" />
        <x-input-error :messages="$errors->get('email')" class="mt-2" />
    </div>

    <!-- Password -->
    <div>
        <x-input-label for="password" :value="__('Password')" />
        <x-text-input id="password" 
                      class="block mt-1 w-full"
                      type="password"
                      name="password"
                      required 
                      autocomplete="current-password" />
        <x-input-error :messages="$errors->get('password')" class="mt-2" />
    </div>

    <!-- Remember Me -->
    <div class="block mt-4">
        <label for="remember_me" class="inline-flex items-center">
            <input id="remember_me" 
                   type="checkbox" 
                   class="rounded border-gray-300 text-indigo-600 shadow-sm focus:ring-indigo-500" 
                   name="remember">
            <span class="ml-2 text-sm text-gray-600">{{ __('Remember me') }}</span>
        </label>
    </div>

    <div class="flex items-center justify-end mt-4">
        @if (Route::has('password.request'))
            <a class="underline text-sm text-gray-600 hover:text-gray-900 rounded-md focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" 
               href="{{ route('password.request') }}">
                {{ __('Forgot your password?') }}
            </a>
        @endif

        <x-primary-button class="ml-3">
            {{ __('Log in') }}
        </x-primary-button>
    </div>
</form>
```

### Payment Integration Frontend

#### Stripe Payment Component
```php
// resources/views/components/payment/stripe-form.blade.php
@props(['intent'])

<div x-data="stripePayment()" class="bg-white p-6 rounded-lg shadow">
    <h3 class="text-lg font-medium mb-4">{{ __('Payment Details') }}</h3>
    
    <form @submit.prevent="submitPayment">
        <!-- Card Element -->
        <div id="card-element" class="p-3 border border-gray-300 rounded-md mb-4">
            <!-- Stripe Elements will create form elements here -->
        </div>
        
        <!-- Card Errors -->
        <div id="card-errors" role="alert" class="text-red-600 text-sm mb-4"></div>
        
        <!-- Submit Button -->
        <button type="submit" 
                :disabled="processing"
                class="w-full bg-blue-600 text-white py-2 px-4 rounded-md hover:bg-blue-700 disabled:opacity-50">
            <span x-show="!processing">{{ __('Complete Payment') }}</span>
            <span x-show="processing">{{ __('Processing...') }}</span>
        </button>
    </form>
</div>

<script src="https://js.stripe.com/v3/"></script>
<script>
function stripePayment() {
    return {
        processing: false,
        stripe: null,
        elements: null,
        cardElement: null,
        
        init() {
            this.stripe = Stripe('{{ config('services.stripe.key') }}');
            this.elements = this.stripe.elements();
            this.cardElement = this.elements.create('card');
            this.cardElement.mount('#card-element');
        },
        
        async submitPayment() {
            this.processing = true;
            
            const { token, error } = await this.stripe.createToken(this.cardElement);
            
            if (error) {
                document.getElementById('card-errors').textContent = error.message;
                this.processing = false;
            } else {
                // Submit token to server
                const form = document.createElement('form');
                form.method = 'POST';
                form.action = '{{ route('payment.process') }}';
                
                const csrfInput = document.createElement('input');
                csrfInput.type = 'hidden';
                csrfInput.name = '_token';
                csrfInput.value = '{{ csrf_token() }}';
                form.appendChild(csrfInput);
                
                const tokenInput = document.createElement('input');
                tokenInput.type = 'hidden';
                tokenInput.name = 'stripeToken';
                tokenInput.value = token.id;
                form.appendChild(tokenInput);
                
                document.body.appendChild(form);
                form.submit();
            }
        }
    }
}
</script>
```

### Update: Asset Bundling with Vite
```javascript
// vite.config.js - Updated configuration
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            input: [
                'resources/css/app.css',
                'resources/js/app.js',
            ],
            refresh: true,
        }),
    ],
    build: {
        rollupOptions: {
            output: {
                manualChunks: {
                    vendor: ['alpinejs'],
                    utils: ['axios', 'lodash'],
                }
            }
        }
    }
});
```

---

Last Updated: January 2025
Version: 2.0.0 (Extended with Laravel Breeze and Package Integration)
Frontend Team: frontend@2030b.com
