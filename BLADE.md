# BLADE.md - UI/UX Architecture & Component System

**Project:** 2030b.com
**Status:** ACTIVE
**Based On:** `PROJECT_GUIDE.md`, `FRONTEND_GUIDE.md`, `USER_JOURNEY_ANALYSIS.md`

## 1. Overview & Philosophy

This document outlines the complete Blade component and view architecture for the Docu-Course Platform. The system is built on Laravel Blade components, prioritizing reusability, accessibility, performance, and a seamless user experience across all user journeys defined in `USER_JOURNEY_ANALYSIS.md`.

### Update: Laravel Breeze Integration
The system now builds on Laravel Breeze Blade as the foundation:
- **Authentication Views:** Pre-built login, registration, password reset
- **Layout System:** App layout, guest layout, and navigation components
- **Form Components:** Enhanced form inputs with validation
- **Tailwind CSS:** Utility-first styling framework
- **Alpine.js:** Lightweight JavaScript framework for interactivity

## 2. Technology Stack & Conventions

- **Framework:** Laravel Blade with Components
- **Styling:** Tailwind CSS
- **Icons:** Heroicons (SVG)
- **JavaScript:** Alpine.js for interactivity
- **Build Tool:** Vite
- **File Naming:** PascalCase for components, kebab-case for views

### Update: Enhanced Technology Stack
Additional technologies from installed packages:
- **Font Awesome:** Comprehensive icon library (via npm)
- **Chart.js:** Data visualization capabilities
- **Flatpickr:** Date/time picker components
- **SweetAlert2:** Beautiful alert dialogs
- **Sortable.js:** Drag-and-drop functionality

## 3. Directory Structure (`resources/views/`)

```
resources/views/
├── components/          # Reusable Blade Components
│   ├── layout/         # Layout-level components
│   ├── ui/             # Base UI elements (buttons, forms, etc.)
│   ├── auth/           # Authentication-specific components
│   ├── course/         # Course-related components
│   └── payment/        # Payment-related components
├── layouts/            # Main layout wrappers
│   ├── app.blade.php
│   ├── guest.blade.php
│   └── admin.blade.php
├── partials/           # View partials (headers, footers, etc.)
├── auth/               # Authentication views
├── courses/            # Course-related views
├── dashboard/          # User dashboard views
├── admin/              # Admin panel views
└── errors/             # Error pages
```

### Update: Laravel Breeze View Structure
Laravel Breeze adds these view directories:
```
resources/views/
├── auth/                    # Breeze authentication views
│   ├── confirm-password.blade.php
│   ├── forgot-password.blade.php
│   ├── login.blade.php
│   ├── register.blade.php
│   ├── reset-password.blade.php
│   └── verify-email.blade.php
├── layouts/
│   ├── app.blade.php       # Main authenticated layout
│   ├── guest.blade.php     # Guest/unauthenticated layout
│   └── navigation.blade.php # Navigation component
├── profile/                # User profile management
│   ├── edit.blade.php
│   └── partials/
│       ├── delete-user-form.blade.php
│       ├── update-password-form.blade.php
│       └── update-profile-information-form.blade.php
└── dashboard.blade.php     # Main dashboard view
```

## 4. Core Layout Components (Highest Hierarchy)

### 4.1. `layouts.app`
- **File:** `resources/views/layouts/app.blade.php`
- **Description:** Main application layout for authenticated users
- **Slots:** `$slot`, `$title`, `$header`
- **Includes:**
  - `<x-layout.head />`
  - `<x-layout.navigation />`
  - `<x-layout.notification />`
  - `<x-layout.footer />`

### 4.2. `layouts.guest`
- **File:** `resources/views/layouts/guest.blade.php`
- **Description:** Layout for public/unauthenticated pages
- **Slots:** `$slot`, `$title`
- **Includes:**
  - `<x-layout.head />`
  - `<x-layout.guest-navigation />`
  - `<x-layout.footer />`

### 4.3. `layouts.admin`
- **File:** `resources/views/layouts/admin.blade.php`
- **Description:** Admin panel layout with specialized navigation
- **Slots:** `$slot`, `$title`, `$header`
- **Includes:**
  - `<x-layout.head />`
  - `<x-layout.admin-navigation />`
  - `<x-layout.footer />`

### Update: Enhanced Layout Features
Layouts enhanced with installed package features:
- **Multi-language support:** Dynamic language switching
- **RTL/LTR support:** Automatic direction detection
- **Activity logging:** User action tracking
- **Role-based navigation:** Permission-aware menus
- **Real-time notifications:** Live notification system

## 5. Layout Partial Components

### 5.1. `layout.head`
- **File:** `resources/views/components/layout/head.blade.php`
- **Description:** HTML head with meta tags, stylesheets, and scripts
- **Props:** `$title` (optional)

### 5.2. `layout.navigation`
- **File:** `resources/views/components/layout/navigation.blade.php`
- **Description:** Main navigation for authenticated users
- **Props:** `$user` (current authenticated user)

### 5.3. `layout.admin-navigation`
- **File:** `resources/views/components/layout/admin-navigation.blade.php`
- **Description:** Admin-specific navigation with quick access to admin tools
- **Props:** `$user` (current admin user)

### 5.4. `layout.guest-navigation`
- **File:** `resources/views/components/layout/guest-navigation.blade.php`
- **Description:** Simple navigation for public pages with login/register links

### 5.5. `layout.footer`
- **File:** `resources/views/components/layout/footer.blade.php`
- **Description:** Site footer with links, copyright, and social media
- **Props:** `$simple` (boolean for minimal footer)

### 5.6. `layout.notification`
- **File:** `resources/views/components/layout/notification.blade.php`
- **Description:** Global notification system for success/error messages
- **Uses:** Alpine.js for dismiss functionality

## 6. Base UI Components (Atomic Design)

### 6.1. `ui.button`
- **File:** `resources/views/components/ui/button.blade.php`
- **Description:** Versatile button component with multiple variants
- **Props:** `$type` (primary, secondary, danger, outline, ghost), `$size` (sm, md, lg), `$href` (optional for link buttons), `$disabled`

**Usage:**
```
<x-ui.button type="primary" size="lg">Create Course</x-ui.button>
<x-ui.button href="{{ route('courses.index') }}" type="outline">Back to Courses</x-ui.button>
```

### Update: Enhanced Button Component
Button component enhanced with:
- **Loading states:** Automatic spinner display during AJAX
- **Icon support:** Font Awesome icon integration
- **Permission checks:** Automatic show/hide based on user roles
- **CSRF protection:** Automatic token inclusion for forms

### 6.2. `ui.card`
- **File:** `resources/views/components/ui/card.blade.php`
- **Description:** Container component with consistent padding and shadow
- **Slots:** `$slot`, `$header`, `$footer`, `$title`, `$description`
- **Props:** `$padding` (sm, md, lg), `$hover` (adds hover effect)

### 6.3. `ui.input`
- **File:** `resources/views/components/ui/input.blade.php`
- **Description:** Form input with validation states and error messages
- **Props:** `$name`, `$label`, `$type` (text, email, password, etc.), `$value`, `$placeholder`, `$required`, `$disabled`, `$error`

**Usage:**
```
<x-ui.input name="email" type="email" label="Email Address" required />
```

### 6.4. `ui.textarea`
- **File:** `resources/views/components/ui/textarea.blade.php`
- **Description:** Textarea input with validation and error handling
- **Props:** `$name`, `$label`, `$rows`, `$placeholder`, `$required`, `$error`

### 6.5. `ui.select`
- **File:** `resources/views/components/ui/select.blade.php`
- **Description:** Select dropdown with options
- **Props:** `$name`, `$label`, `$options` (key-value array), `$selected`, `$placeholder`, `$required`, `$error`

### 6.6. `ui.checkbox`
- **File:** `resources/views/components/ui/checkbox.blade.php`
- **Description:** Checkbox input with label
- **Props:** `$name`, `$label`, `$checked`, `$value`, `$required`

### 6.7. `ui.radio`
- **File:** `resources/views/components/ui/radio.blade.php`
- **Description:** Radio button with label
- **Props:** `$name`, `$label`, `$value`, `$checked`, `$required`

### 6.8. `ui.modal`
- **File:** `resources/views/components/ui/modal.blade.php`
- **Description:** Accessible modal dialog with backdrop
- **Slots:** `$slot`, `$title`, `$footer`
- **Props:** `$id` (required for accessibility), `$maxWidth` (sm, md, lg, xl)
- **Uses:** Alpine.js for open/close functionality

### 6.9. `ui.loading`
- **File:** `resources/views/components/ui/loading.blade.php`
- **Description:** Animated loading spinner
- **Props:** `$size` (sm, md, lg), `$text` (optional loading text)

### 6.10. `ui.alert`
- **File:** `resources/views/components/ui/alert.blade.php`
- **Description:** Contextual alert messages
- **Props:** `$type` (success, error, warning, info), `$title`, `$dismissible`
- **Slots:** `$slot` (for message content)

### 6.11. `ui.badge`
- **File:** `resources/views/components/ui/badge.blade.php`
- **Description:** Small status indicators
- **Props:** `$variant` (primary, success, danger, warning, info, outline), `$pill` (rounded badge)

### 6.12. `ui.empty-state`
- **File:** `resources/views/components/ui/empty-state.blade.php`
- **Description:** Display when no content is available
- **Slots:** `$slot`, `$title`, `$action`
- **Props:** `$icon` (Heroicon name), `$description`

### Update: Additional UI Components
New components available with installed packages:
- **ui.file-upload:** Drag-and-drop file upload with Spatie Media Library
- **ui.date-picker:** Beautiful date picker with Flatpickr
- **ui.chart:** Data visualization with Chart.js integration
- **ui.toast:** Non-intrusive notifications with SweetAlert2
- **ui.sortable:** Drag-and-drop sorting with Sortable.js

## 7. Authentication Components

### 7.1. `auth.login-form`
- **File:** `resources/views/components/auth/login-form.blade.php`
- **Description:** Complete login form with validation
- **Uses:** `<x-ui.input>`, `<x-ui.button>`

### 7.2. `auth.register-form`
- **File:** `resources/views/components/auth/register-form.blade.php`
- **Description:** Complete registration form with validation
- **Uses:** `<x-ui.input>`, `<x-ui.button>`

### 7.3. `auth.forgot-password-form`
- **File:** `resources/views/components/auth/forgot-password-form.blade.php`
- **Description:** Password reset request form

### 7.4. `auth.reset-password-form`
- **File:** `resources/views/components/auth/reset-password-form.blade.php`
- **Description:** Password reset form with token validation

### Update: Laravel Breeze Authentication Components
Laravel Breeze provides these additional authentication components:
- **Email verification:** Built-in email verification flow
- **Password confirmation:** Secure action confirmation
- **Profile management:** User profile editing components
- **Two-factor authentication:** Integration with Google2FA package
- **Remember me:** Persistent login functionality

## 8. Course Components

### 8.1. `course.card`
- **File:** `resources/views/components/course/card.blade.php`
- **Description:** Course listing card with image, title, and metadata
- **Props:** `$course` (Course model), `$showAuthor` (boolean)

### 8.2. `course.grid`
- **File:** `resources/views/components/course/grid.blade.php`
- **Description:** Responsive grid of course cards
- **Props:** `$courses` (Course collection), `$title` (grid section title)

### 8.3. `course.progress`
- **File:** `resources/views/components/course/progress.blade.php`
- **Description:** Progress bar for course completion
- **Props:** `$progress` (percentage), `$size` (sm, md, lg)

### 8.4. `course.rating`
- **File:** `resources/views/components/course/rating.blade.php`
- **Description:** Star rating display and input
- **Props:** `$rating` (average rating), `$count` (number of reviews), `$interactive` (for user ratings)

### 8.5. `course.lesson-list`
- **File:** `resources/views/components/course/lesson-list.blade.php`
- **Description:** Interactive list of course lessons
- **Props:** `$lessons` (Lesson collection), `$currentLessonId`, `$completedLessons` (array of IDs)

### 8.6. `course.enrollment-button`
- **File:** `resources/views/components/course/enrollment-button.blade.php`
- **Description:** Smart button that handles enrollment status
- **Props:** `$course` (Course model), `$user` (current user)

### Update: Enhanced Course Components
Course components enhanced with installed packages:
- **Media integration:** Spatie Media Library for course images/videos
- **Permission checks:** Role-based course access control
- **Activity tracking:** Automatic logging of course interactions
- **Payment integration:** Stripe/Binance payment buttons
- **Progress tracking:** Real-time course completion updates

## 9. Payment Components

### 9.1. `payment.pricing-card`
- **File:** `resources/views/components/payment/pricing-card.blade.php`
- **Description:** Pricing plan card with features and action button
- **Props:** `$plan` (plan data), `$featured` (boolean for highlighted plan)

### 9.2. `payment.pricing-grid`
- **File:** `resources/views/components/payment/pricing-grid.blade.php`
- **Description:** Grid layout for pricing cards
- **Props:** `$plans` (array of plan data)

### 9.3. `payment.checkout-form`
- **File:** `resources/views/components/payment/checkout-form.blade.php`
- **Description:** Secure checkout form with payment method selection
- **Props:** `$course` (Course model), `$intent` (PaymentIntent for Stripe)

### Update: Payment Integration Components
Payment components enhanced with installed packages:
- **Stripe integration:** Credit card payment forms
- **Binance integration:** Cryptocurrency payment options
- **Laravel Money:** Multi-currency price display
- **Exchange rates:** Real-time currency conversion
- **Payment tracking:** Transaction status updates

## 10. Dashboard Components

### 10.1. `dashboard.sidebar`
- **File:** `resources/views/components/dashboard/sidebar.blade.php`
- **Description:** Dashboard navigation sidebar
- **Props:** `$active` (currently active section)

### 10.2. `dashboard.stat-card`
- **File:** `resources/views/components/dashboard/stat-card.blade.php`
- **Description:** Card displaying a statistic with icon and trend
- **Props:** `$title`, `$value`, `$icon`, `$trend` (percentage), `$trendDirection` (up/down)

### 10.3. `dashboard.activity-feed`
- **File:** `resources/views/components/dashboard/activity-feed.blade.php`
- **Description:** Timeline of recent user activity
- **Props:** `$activities` (Activity collection)

### Update: Enhanced Dashboard Components
Dashboard components enhanced with:
- **Chart.js integration:** Data visualization widgets
- **Real-time updates:** Live dashboard statistics
- **Activity logging:** Spatie Activity Log integration
- **Permission-based widgets:** Role-specific dashboard content
- **Backup status:** System health monitoring

## 11. View Templates Hierarchy

### 11.1. Authentication Views
- `auth/login.blade.php` → Uses: `layouts.guest`, `auth.login-form`
- `auth/register.blade.php` → Uses: `layouts.guest`, `auth.register-form`
- `auth/forgot-password.blade.php` → Uses: `layouts.guest`, `auth.forgot-password-form`
- `auth/reset-password.blade.php` → Uses: `layouts.guest`, `auth.reset-password-form`

### 11.2. Course Views
- `courses/index.blade.php` → Uses: `layouts.app`, `course.grid`, `ui.filter`
- `courses/show.blade.php` → Uses: `layouts.app`, `course.rating`, `course.enrollment-button`
- `courses/learn.blade.php` → Uses: `layouts.app`, `course.lesson-list`, `course.progress`
- `courses/create.blade.php` → Uses: `layouts.app`, various form components

### 11.3. Dashboard Views
- `dashboard/index.blade.php` → Uses: `layouts.app`, `dashboard.sidebar`, `dashboard.stat-card`, `dashboard.activity-feed`
- `dashboard/courses.blade.php` → Uses: `layouts.app`, `dashboard.sidebar`, `course.grid`
- `dashboard/profile.blade.php` → Uses: `layouts.app`, `dashboard.sidebar`, form components

### 11.4. Admin Views
- `admin/index.blade.php` → Uses: `layouts.admin`, `dashboard.stat-card`
- `admin/users.blade.php` → Uses: `layouts.admin`, `ui.table`
- `admin/courses.blade.php` → Uses: `layouts.admin`, `ui.table`, `ui.modal`

### Update: Laravel Breeze View Integration
Laravel Breeze provides these additional views:
- **Email verification:** `auth/verify-email.blade.php`
- **Password confirmation:** `auth/confirm-password.blade.php`
- **Profile management:** `profile/edit.blade.php` with partials
- **Dashboard home:** `dashboard.blade.php` as the main authenticated landing page

## 12. Performance & Accessibility Guidelines

1. **Lazy Loading:** Use `loading="lazy"` for images below the fold
2. **Image Optimization:** Serve WebP format with fallbacks
3. **ARIA Labels:** All interactive elements must have proper ARIA attributes
4. **Keyboard Navigation:** Ensure all components are keyboard accessible
5. **Focus Management:** Manage focus properly in modals and dynamic content
6. **Color Contrast:** Maintain WCAG AA compliance for text and UI elements
7. **Responsive Design:** Ensure components work on mobile, tablet, and desktop

### Update: Enhanced Performance & Accessibility
Performance and accessibility enhanced with:
- **Spatie Image Optimizer:** Automatic image compression
- **Lazy loading:** Built-in lazy loading for media components
- **Vite optimization:** Fast development and optimized production builds
- **Cache busting:** Automatic asset versioning
- **CDN integration:** S3 integration for static assets

## 13. JavaScript Interactivity

- Use Alpine.js for simple interactivity (toggles, modals, dropdowns)
- Use Livewire for complex state management and server communication
- Keep JavaScript minimal and focused on enhancing UX
- Ensure graceful degradation when JavaScript is disabled

### Update: Enhanced JavaScript Capabilities
JavaScript capabilities enhanced with npm packages:
- **Chart.js:** Interactive data visualization
- **Flatpickr:** Beautiful date/time pickers
- **SweetAlert2:** Beautiful alert dialogs
- **Sortable.js:** Drag-and-drop functionality
- **Alpine.js plugins:** Focus management and persistence

## 14. Component Development Workflow

1. **Create Component:** `php artisan make:component Ui/Button`
2. **Implement Design:** Create Blade template with Tailwind classes
3. **Add Props:** Define component properties in class file
4. **Test Accessibility:** Verify with screen readers and keyboard navigation
5. **Add Documentation:** Include examples in Storybook or component library
6. **Deploy:** Use Vite to compile and optimize assets

### Update: Enhanced Development Workflow
Development workflow enhanced with:
- **Hot module replacement:** Vite HMR for fast development
- **Component testing:** Pest for component testing
- **Code formatting:** Laravel Pint for consistent styling
- **Static analysis:** Larastan for type checking
- **Package integration:** Automatic Spatie package discovery

> **This component system provides a comprehensive foundation for building maintainable, accessible, and high-performance user interfaces for the 2030b.com platform.**
> 
> **Next Steps:**
> 1. Implement the core layout components
> 2. Build the base UI components
> 3. Create domain-specific components
> 4. Set up testing infrastructure
> 5. Develop component documentation