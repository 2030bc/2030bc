# PERFORMANCE_GUIDE.md - Laravel Docu-Course Platform Performance Guide

## Overview

This document establishes comprehensive performance optimization strategies for the Laravel 12.24.0 docu-course platform, ensuring scalability for millions of users and billions in revenue potential.

## Performance Philosophy

- **Speed First**: Sub-200ms page load times
- **Scalability**: Handle 10,000+ concurrent users
- **Efficiency**: Optimize resource utilization
- **Monitoring**: Real-time performance tracking
- **Progressive Enhancement**: Fast baseline, enhanced features

## Performance Targets

### Response Time Targets
- **Homepage**: < 150ms
- **Course List**: < 200ms
- **Episode Content**: < 100ms
- **API Endpoints**: < 50ms
- **Database Queries**: < 10ms average
- **File Serving**: < 50ms

### Scalability Targets
- **Concurrent Users**: 10,000+
- **Daily Active Users**: 100,000+
- **API Requests**: 1M+ per hour
- **Database Connections**: 500+ concurrent
- **Storage**: 10TB+ course content

## Package Integration Updates

Performance optimizations based on installed packages

## Redis Caching Strategy (Updated)

```
// config/cache.php - Redis configuration
'redis' => [
    'driver' => 'redis',
    'connection' => 'cache',
    'lock_connection' => 'default',
],

// .env configuration
CACHE_DRIVER=redis
SESSION_DRIVER=redis
QUEUE_CONNECTION=redis

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379
```

```
// Multi-Layer Caching Implementation
class CacheService
{
    // L1: Application Cache (Redis)
    public function getCourseList(array $filters = []): Collection
    {
        $cacheKey = 'courses:list:' . md5(serialize($filters));
        
        return Cache::tags(['courses'])->remember($cacheKey, 300, function () use ($filters) {
            return $this->courseService->getFilteredCourses($filters);
        });
    }

    // L2: HTTP Cache (CDN)
    public function getCourseContent(string $uuid): string
    {
        $cacheKey = "course:content:{$uuid}";
        
        return Cache::remember($cacheKey, 3600, function () use ($uuid) {
            return $this->courseService->renderCourseContent($uuid);
        });
    }

    // L3: Browser Cache (Static Assets)
    public function getOptimizedAsset(string $asset): Response
    {
        return response()
            ->file(storage_path("app/optimized/{$asset}"))
            ->header('Cache-Control', 'public, max-age=31536000') // 1 year
            ->header('Expires', now()->addYear()->toRfc7231String());
    }
}
```

**Cache Invalidation Strategy**
- User progress update → Clear user:* and stats:* caches
- Course content change → Clear course:* and episode:* caches
- Payment completion → Clear user:* and affiliate:* caches

## Spatie Package Optimizations (New)

### Media Library Optimization
```
// config/media-library.php
'queue_conversions_by_default' => true,
'queue_name' => 'media-conversions',

// Optimize image conversions
public function registerMediaConversions(Media $media = null): void
{
    $this->addMediaConversion('thumb')
        ->width(300)
        ->height(200)
        ->quality(85)
        ->format('webp')
        ->performOnCollections('thumbnails')
        ->queued();
}
```

### Query Builder Optimization
```
// Optimized API queries
public function index(Request $request)
{
    $courses = QueryBuilder::for(DocuCourse::class)
        ->allowedFilters(['title', 'category', 'level'])
        ->allowedSorts(['created_at', 'title', 'rating'])
        ->allowedIncludes(['author', 'category'])
        ->with(['author:id,name', 'category:id,name'])
        ->paginate(20);
        
    return $courses;
}
```

## Database Performance (Updated)

```
// Connection Pool with Predis
// config/database.php
'redis' => [
    'client' => env('REDIS_CLIENT', 'predis'),
    'options' => [
        'cluster' => env('REDIS_CLUSTER', 'redis'),
        'prefix' => env('REDIS_PREFIX', Str::slug(env('APP_NAME', 'laravel'), '_').'_database_'),
    ],
    'default' => [
        'url' => env('REDIS_URL'),
        'host' => env('REDIS_HOST', '127.0.0.1'),
        'password' => env('REDIS_PASSWORD', null),
        'port' => env('REDIS_PORT', '6379'),
        'database' => env('REDIS_DB', '0'),
        'options' => [
            'parameters' => [
                'tcp_keepalive' => 1,
            ],
        ],
    ],
]
```

**Note:** Database indexing strategy and query optimization remain as documented in the original PERFORMANCE_GUIDE.md

## File & Asset Optimization (Enhanced)

### Intervention Image + Spatie Image Optimizer
```
// Enhanced image processing
class ImageOptimizationService
{
    public function processAndOptimize(UploadedFile $image): array
    {
        $variants = [];
        $sizes = [
            'thumbnail' => [300, 200, 'webp', 85],
            'medium' => [600, 400, 'webp', 90],
            'large' => [1200, 800, 'webp', 95],
        ];

        foreach ($sizes as $variant => [$width, $height, $format, $quality]) {
            $processed = Image::make($image->getPathname());
            
            if ($width && $height) {
                $processed->fit($width, $height);
            }

            $filename = $this->generateFilename($image, $variant, $format);
            $path = storage_path("app/public/images/{$variant}/{$filename}");
            
            $processed->encode($format, $quality)->save($path);
            
            // Apply Spatie Image Optimizer
            ImageOptimizer::optimize($path);
            
            $variants[$variant] = $filename;
        }

        return $variants;
    }
}
```

## Performance Monitoring (Enhanced)

```
// Real-time Performance Tracking with Activity Log
class PerformanceMonitor
{
    public function trackRequest(): void
    {
        $start = microtime(true);
        $memory = memory_get_usage(true);

        app()->terminating(function () use ($start, $memory) {
            $duration = (microtime(true) - $start) * 1000; // ms
            $memoryUsed = memory_get_peak_usage(true) - $memory;

            // Log with Spatie Activity Log
            activity()
                ->causedBy(auth()->user())
                ->withProperties([
                    'duration_ms' => round($duration, 2),
                    'memory_mb' => round($memoryUsed / 1024 / 1024, 2),
                    'query_count' => count(DB::getQueryLog()),
                    'url' => request()->url(),
                    'method' => request()->method(),
                ])
                ->log('performance_metric');

            // Alert on slow requests
            if ($duration > 1000) { // > 1 second
                $this->alertSlowRequest($duration);
            }
        });
    }
}
```

**Note:** All other sections from the original PERFORMANCE_GUIDE.md remain unchanged, including Memory Management, Application Performance, Scalability Architecture, Performance Testing, and Optimization Checklist.

