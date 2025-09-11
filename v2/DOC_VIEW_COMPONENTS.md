# DOC_VIEW_COMPONENTS.md

**Key Requirements Identified**:
1. Laravel Breeze integration with Blade templates for authentication
2. Multi-language support with RTL (Arabic) and LTR languages using mcamara/laravel-localization
3. Spatie Media Library for advanced file management and image optimization
4. Spatie Permission package for role-based access control
5. API resources for frontend-backend integration
6. Consistent component architecture and documentation standards

## 2030b/app/View/Components/AppLayout.php

**Purpose**: Main application layout component providing authenticated user interface structure with navigation, sidebar, and content areas, fully integrated with Laravel Breeze authentication system.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Facades\Auth;
use App\Models\User;
use App\Services\Notification\NotificationService;
use Spatie\Permission\Traits\HasRoles;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
```

**Methods**:
- __construct(): void
- render(): View
- getUser(): ?User
- getNotifications(): array
- getNavigationItems(): array
- getUserPermissions(): array
- getCurrentLocale(): string
- getLayoutDirection(): string
- getUserAvatar(): ?string

**Do**:
- Extend Laravel Breeze authentication layouts seamlessly
- Include navigation based on Spatie Permission user roles and permissions
- Display user-specific notifications and alerts with proper localization
- Implement responsive design patterns with RTL/LTR support for Arabic and other languages
- Cache navigation data for performance while respecting permission changes
- Integrate Spatie Media Library for user avatar display
- Support dynamic locale switching via mcamara/laravel-localization
- Include proper ARIA attributes for multilingual accessibility

**Don't**:
- Don't bypass Laravel Breeze authentication middleware checks
- Don't include business logic in layout components
- Don't expose sensitive user data in templates without permission validation
- Don't ignore mobile responsiveness or RTL layout requirements
- Don't hardcode navigation items without permission-based filtering
- Don't cache user permissions longer than session duration

---

## 2030b/app/View/Components/GuestLayout.php

**Purpose**: Guest layout component for unauthenticated users providing clean interface for Laravel Breeze authentication and public pages with full localization support.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Facades\Config;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
use Spatie\Translatable\HasTranslations;
```

**Methods**:
- __construct(): void
- render(): View
- getPublicNavigationItems(): array
- getAvailableLocales(): array
- getMetaTags(): array
- getLayoutDirection(): string
- getLocalizedRoute(string $route): string
- getCurrentLanguageCode(): string

**Do**:
- Provide clean, minimal layout compatible with Laravel Breeze auth views
- Include language switching functionality using mcamara/laravel-localization
- Implement proper SEO meta tags with localized content
- Ensure accessibility compliance for all supported languages (English, Arabic, Spanish, French, German)
- Support RTL languages properly with automatic CSS direction handling
- Display Laravel Breeze session status messages with proper localization
- Include CSRF protection for all authentication forms

**Don't**:
- Don't include authenticated user features or navigation
- Don't display private navigation items or restricted content
- Don't ignore SEO requirements for multilingual content
- Don't skip accessibility features for RTL languages
- Don't bypass Laravel Breeze authentication session handling
- Don't hardcode language-specific content

---

## 2030b/app/View/Components/Button.php

**Purpose**: Reusable button component with consistent styling, variants, and interactive states, supporting multilingual content and RTL layouts.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(string $type = 'button', string $variant = 'primary', string $size = 'md', bool $disabled = false): void
- render(): View
- getButtonClasses(): string
- getVariantClasses(): string
- getSizeClasses(): string
- getRTLAwareClasses(): string
- getLocalizedText(): string

**Do**:
- Provide consistent button styling across application with RTL support
- Support multiple button variants and sizes with direction-aware spacing
- Include proper accessibility attributes for multilingual screen readers
- Handle loading and disabled states with localized feedback
- Support icon placement within buttons with RTL icon rotation
- Integrate with Spatie Permission for conditional button visibility
- Use Laravel localization for button text content

**Don't**:
- Don't create buttons without proper semantic meaning or ARIA labels
- Don't ignore accessibility requirements for different languages
- Don't hardcode styling that breaks RTL layouts
- Don't skip keyboard navigation support
- Don't bypass permission checks for action buttons
- Don't use non-translatable button text

---

## 2030b/app/View/Components/Input.php

**Purpose**: Form input component with validation states, error handling, and consistent styling, fully integrated with Laravel Breeze authentication forms and multilingual validation.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\ViewErrorBag;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(string $name, string $type = 'text', ?string $value = null, bool $required = false): void
- render(): View
- hasError(): bool
- getErrorMessage(): ?string
- getInputClasses(): string
- getLocalizedPlaceholder(): string
- getRTLInputClasses(): string
- getValidationAttributes(): array

**Do**:
- Display validation errors appropriately with localized error messages
- Support various input types and attributes compatible with Laravel Breeze forms
- Include proper form labels and descriptions in multiple languages
- Implement consistent input styling with RTL text direction support
- Handle value binding correctly for multilingual content
- Integrate Laravel validation with localized error display
- Support autocomplete attributes for authentication forms

**Don't**:
- Don't ignore validation error display or bypass Laravel Breeze validation rules
- Don't skip accessibility labels for different languages
- Don't hardcode input attributes that break RTL layouts
- Don't forget to sanitize input values
- Don't use non-translatable placeholder text or labels
- Don't bypass CSRF protection in form contexts

---

## 2030b/app/View/Components/Label.php

**Purpose**: Form label component ensuring proper form accessibility and consistent label styling with full multilingual and RTL support.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(string $for, bool $required = false): void
- render(): View
- getLabelClasses(): string
- getRequiredIndicator(): string
- getLocalizedLabelText(): string
- getRTLLabelClasses(): string

**Do**:
- Associate labels with form inputs properly using 'for' attributes
- Indicate required fields clearly with localized required indicators
- Provide consistent label styling with RTL layout support
- Support internationalization for all label text content
- Include accessibility attributes for multilingual screen readers
- Integrate with Laravel Breeze form requirements

**Don't**:
- Don't create labels without proper 'for' attributes
- Don't ignore required field indicators or use non-translatable text
- Don't skip internationalization support
- Don't use ambiguous label text that doesn't translate well
- Don't hardcode label styling that breaks RTL layouts

---

## 2030b/app/View/Components/Dropdown.php

**Purpose**: Dropdown menu component for navigation menus, action lists, and selection interfaces with multilingual content and RTL-aware positioning.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Collection;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
use Spatie\Permission\Traits\HasPermissions;
```

**Methods**:
- __construct(array $items = [], string $trigger = 'click', string $position = 'bottom-left'): void
- render(): View
- getDropdownItems(): Collection
- getPositionClasses(): string
- getTriggerAttributes(): array
- getFilteredItems(): Collection
- getRTLPosition(): string

**Do**:
- Support keyboard navigation with multilingual accessibility
- Handle click outside to close with RTL-aware positioning
- Position dropdowns properly to stay in viewport for all languages
- Include proper ARIA attributes with localized labels
- Support nested dropdown items with permission-based filtering
- Filter dropdown items based on Spatie Permission user roles
- Use localized content for dropdown item labels

**Don't**:
- Don't ignore keyboard accessibility for different languages
- Don't position dropdowns outside viewport in RTL layouts
- Don't skip click outside handling
- Don't forget ARIA attributes or bypass permission checks
- Don't display menu items without proper authorization
- Don't use non-translatable menu item text

---

## 2030b/app/View/Components/Modal.php

**Purpose**: Modal dialog component for overlays, confirmations, and form dialogs with proper accessibility, multilingual support, and RTL layout compatibility.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(string $id, string $size = 'md', bool $closable = true, bool $backdrop = true): void
- render(): View
- getModalClasses(): string
- getSizeClasses(): string
- getBackdropAttributes(): array
- getLocalizedCloseText(): string
- getRTLModalClasses(): string

**Do**:
- Implement proper modal accessibility (ARIA, focus management) for all languages
- Support different modal sizes with RTL layout considerations
- Handle escape key to close with localized close button text
- Prevent background scrolling when open
- Include proper modal headers and actions with multilingual content
- Support RTL text direction and layout mirroring

**Don't**:
- Don't ignore focus management for multilingual screen readers
- Don't skip escape key handling
- Don't forget to prevent background scroll
- Don't create modals without proper headers or non-translatable content
- Don't hardcode modal positioning that breaks RTL layouts

---

## 2030b/app/View/Components/Alert.php

**Purpose**: Alert notification component for displaying messages, warnings, errors, and success notifications with full localization and RTL support.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(string $type = 'info', bool $dismissible = false, ?string $title = null): void
- render(): View
- getAlertClasses(): string
- getTypeClasses(): string
- getIconClass(): string
- getLocalizedMessage(): string
- getRTLAlertClasses(): string

**Do**:
- Support different alert types (success, error, warning, info) with localized messages
- Include appropriate icons for alert types with RTL icon positioning
- Make alerts dismissible when appropriate with localized dismiss text
- Use semantic HTML for multilingual screen readers
- Support auto-dismiss functionality with proper timing
- Display Laravel Breeze session status messages appropriately

**Don't**:
- Don't use alerts for critical error messages that need attention without localization
- Don't make important alerts auto-dismissible
- Don't ignore accessibility requirements for different languages
- Don't use generic styling for different alert types
- Don't display non-translatable alert content

---

## 2030b/app/View/Components/Card.php

**Purpose**: Card component for content containers, course displays, and structured content presentation with multilingual content and media integration.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(?string $title = null, ?string $subtitle = null, bool $bordered = true, bool $shadow = false): void
- render(): View
- getCardClasses(): string
- hasHeader(): bool
- hasFooter(): bool
- getLocalizedTitle(): string
- getOptimizedImage(): ?string
- getRTLCardClasses(): string

**Do**:
- Provide flexible card structure for various content types with multilingual support
- Support optional headers, footers, and actions with localized content
- Include proper spacing and typography for RTL/LTR layouts
- Support card variants and styling options with direction awareness
- Make cards responsive and accessible for all languages
- Integrate Spatie Media Library for optimized card images
- Display localized course information and pricing

**Don't**:
- Don't create cards without proper semantic structure
- Don't ignore responsive design requirements for RTL layouts
- Don't hardcode card content structure or non-translatable text
- Don't skip accessibility considerations for multilingual content
- Don't bypass media optimization for card images

---

## 2030b/app/View/Components/Badge.php

**Purpose**: Badge component for status indicators, labels, and notification counts with multilingual content and RTL layout support.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(string $variant = 'default', string $size = 'sm', bool $rounded = false): void
- render(): View
- getBadgeClasses(): string
- getVariantClasses(): string
- getSizeClasses(): string
- getLocalizedText(): string
- getRTLBadgeClasses(): string

**Do**:
- Support different badge variants and colors with semantic meaning
- Provide appropriate sizing options with RTL spacing considerations
- Use semantic meaning for badge colors consistently across languages
- Support both text and numeric badges with proper localization
- Include proper contrast for accessibility in all languages
- Display course difficulty levels and access badges with localized text

**Don't**:
- Don't use badges for interactive elements
- Don't ignore color contrast requirements for accessibility
- Don't create badges without semantic meaning or translatable content
- Don't make badges too large or prominent
- Don't use non-localized status text or labels

---

## 2030b/app/View/Components/Form/Select.php

**Purpose**: Select dropdown component for form selections with validation, styling consistency, and multilingual option support.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Collection;
use Illuminate\Support\ViewErrorBag;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(string $name, array $options = [], ?string $selected = null, bool $multiple = false): void
- render(): View
- getOptions(): Collection
- hasError(): bool
- getSelectClasses(): string
- isSelected(string $value): bool
- getLocalizedOptions(): Collection
- getRTLSelectClasses(): string

**Do**:
- Support single and multiple selections with localized option text
- Handle validation errors appropriately with multilingual error messages
- Include proper option grouping with RTL-aware styling
- Support search functionality for large option lists in multiple languages
- Maintain selected state correctly across language switches
- Integrate with Laravel Breeze form validation patterns

**Don't**:
- Don't create selects without proper option values or translations
- Don't ignore validation error display or bypass localization
- Don't skip accessibility attributes for multilingual users
- Don't hardcode option lists or use non-translatable option text

---

## 2030b/app/View/Components/Form/Checkbox.php

**Purpose**: Checkbox input component with proper labeling, validation, styling consistency, and multilingual label support.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\ViewErrorBag;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(string $name, ?string $value = null, bool $checked = false, ?string $label = null): void
- render(): View
- isChecked(): bool
- hasError(): bool
- getCheckboxClasses(): string
- getLocalizedLabel(): string
- getRTLCheckboxClasses(): string

**Do**:
- Associate checkboxes with proper labels in multiple languages
- Handle checked state correctly across form submissions
- Support indeterminate state when needed
- Display validation errors appropriately with localized messages
- Include proper accessibility attributes for multilingual screen readers
- Support RTL layout with proper label positioning

**Don't**:
- Don't create checkboxes without proper labels or translations
- Don't ignore checked state handling or validation requirements
- Don't skip validation error display
- Don't forget accessibility requirements for different languages
- Don't use non-translatable checkbox labels

---

## 2030b/app/View/Components/Form/Radio.php

**Purpose**: Radio button component for exclusive selections with proper grouping, validation, and multilingual label support.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\ViewErrorBag;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(string $name, string $value, ?string $checked = null, ?string $label = null): void
- render(): View
- isChecked(): bool
- hasError(): bool
- getRadioClasses(): string
- getLocalizedLabel(): string
- getRTLRadioClasses(): string

**Do**:
- Group radio buttons with same name attribute properly
- Handle checked state for radio groups across language switches
- Provide clear labels for each option in multiple languages
- Display validation errors for the group with localized messages
- Support keyboard navigation between options with RTL awareness

**Don't**:
- Don't create ungrouped radio buttons
- Don't ignore radio group validation or localization requirements
- Don't skip keyboard navigation support
- Don't use radio buttons for non-exclusive choices
- Don't use non-translatable radio button labels

---

## 2030b/app/View/Components/Form/Textarea.php

**Purpose**: Textarea component for multi-line text input with character counting, validation, and multilingual placeholder support.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\ViewErrorBag;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(string $name, ?string $value = null, int $rows = 4, ?int $maxlength = null): void
- render(): View
- hasError(): bool
- getTextareaClasses(): string
- shouldShowCharacterCount(): bool
- getLocalizedPlaceholder(): string
- getRTLTextareaClasses(): string

**Do**:
- Support resizable textareas appropriately with RTL text direction
- Include character counting when needed with localized count display
- Display validation errors clearly with multilingual error messages
- Support auto-resize functionality
- Handle placeholder text properly in multiple languages
- Support RTL text input and display

**Don't**:
- Don't ignore character limit validation or localization
- Don't make textareas too small for multilingual content
- Don't skip validation error display
- Don't forget proper labeling in multiple languages
- Don't use non-translatable placeholder text

---

## 2030b/app/View/Components/Layout/Sidebar.php

**Purpose**: Sidebar navigation component for main application navigation with collapsible functionality, permission-based menu filtering, and multilingual navigation items.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Facades\Auth;
use App\Models\User;
use Spatie\Permission\Models\Permission;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(bool $collapsible = true): void
- render(): View
- getNavigationItems(): array
- getUserPermissions(): array
- isActiveRoute(string $route): bool
- canAccessRoute(string $route): bool
- getLocalizedNavigation(): array
- getRTLSidebarClasses(): string

**Do**:
- Filter navigation based on Spatie Permission user roles and permissions
- Highlight active navigation items with proper RTL support
- Support collapsible sidebar functionality
- Group navigation items logically with multilingual labels
- Include proper accessibility navigation for all languages
- Integrate with Laravel Breeze authentication status
- Cache navigation permissions for performance

**Don't**:
- Don't show restricted navigation items or bypass permission checks
- Don't ignore active state highlighting
- Don't create deep navigation hierarchies without proper organization
- Don't skip accessibility navigation attributes
- Don't display non-translatable navigation labels
- Don't expose unauthorized routes in navigation

---

## 2030b/app/View/Components/Layout/Header.php

**Purpose**: Application header component with user menu, notifications, global navigation elements, and language switching functionality.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Facades\Auth;
use App\Services\Notification\NotificationService;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
```

**Methods**:
- __construct(): void
- render(): View
- getUser(): ?User
- getUnreadNotifications(): int
- getUserMenu(): array
- getGlobalActions(): array
- getAvailableLocales(): array
- getUserAvatar(): ?string
- getRTLHeaderClasses(): string

**Do**:
- Display user authentication status integrated with Laravel Breeze
- Show unread notification counts with proper localization
- Provide user profile and settings access with Spatie Permission checks
- Include global search functionality in multiple languages
- Support responsive header layout with RTL considerations
- Integrate language switching via mcamara/laravel-localization
- Display optimized user avatars via Spatie Media Library

**Don't**:
- Don't expose sensitive user information without permission validation
- Don't ignore notification count accuracy or localization
- Don't make header too cluttered with non-essential elements
- Don't skip mobile responsiveness or RTL layout support
- Don't bypass Laravel Breeze authentication middleware
- Don't display non-translatable header content

---

## 2030b/app/View/Components/Layout/Footer.php

**Purpose**: Application footer component with links, copyright, supplementary information, and multilingual content support.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Facades\Config;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(): void
- render(): View
- getFooterLinks(): array
- getCopyrightText(): string
- getAppVersion(): string
- getSocialLinks(): array
- getLocalizedFooterContent(): array
- getRTLFooterClasses(): string

**Do**:
- Include relevant footer links and information with multilingual support
- Display copyright and legal information in appropriate languages
- Provide social media and contact links
- Support multi-language footer content with RTL layout
- Keep footer content up to date and properly localized
- Include language selector in footer when appropriate

**Don't**:
- Don't include too many footer links without proper organization
- Don't ignore copyright date updates or translation requirements
- Don't skip accessibility in footer navigation
- Don't make footer content too prominent or non-translatable
- Don't hardcode footer content without localization support

---

## 2030b/app/View/Components/UI/LoadingSpinner.php

**Purpose**: Loading spinner component for indicating processing states and async operations with multilingual loading messages.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(string $size = 'md', string $variant = 'primary', ?string $text = null): void
- render(): View
- getSpinnerClasses(): string
- getSizeClasses(): string
- shouldShowText(): bool
- getLocalizedLoadingText(): string
- getRTLSpinnerClasses(): string

**Do**:
- Provide different spinner sizes and styles with RTL considerations
- Include accessible loading text in multiple languages
- Support overlay and inline spinner modes
- Use consistent animation timing
- Hide spinner when loading completes
- Display localized loading messages appropriately

**Don't**:
- Don't use spinners without accessible text for screen readers
- Don't make spinners too prominent or distracting
- Don't forget to hide spinners after loading completion
- Don't use spinners for instant operations
- Don't display non-translatable loading messages

---

## 2030b/app/View/Components/UI/ProgressBar.php

**Purpose**: Progress bar component for displaying completion status, upload progress, and step indicators with multilingual progress descriptions.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(float $progress = 0, string $variant = 'primary', bool $striped = false, bool $animated = false): void
- render(): View
- getProgressClasses(): string
- getProgressWidth(): string
- isComplete(): bool
- getLocalizedProgressText(): string
- getRTLProgressClasses(): string

**Do**:
- Display accurate progress percentages with localized percentage formatting
- Support different progress bar styles with RTL layout awareness
- Include accessible progress text in multiple languages
- Handle progress bar animations smoothly
- Indicate completion states clearly with localized completion messages
- Support course progress tracking with appropriate styling

**Don't**:
- Don't display inaccurate progress values
- Don't ignore accessibility requirements for different languages
- Don't animate progress bars unnecessarily
- Don't make progress bars too small to see
- Don't use non-localized progress descriptions

---

## 2030b/app/View/Components/UI/ToastNotification.php

**Purpose**: Toast notification component for temporary messages and user feedback with multilingual content and RTL positioning.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(string $type = 'info', int $duration = 5000, bool $closable = true): void
- render(): View
- getToastClasses(): string
- getTypeClasses(): string
- getAutoCloseAttributes(): array
- getLocalizedMessage(): string
- getRTLToastPosition(): string

**Do**:
- Support different notification types with localized messages
- Include auto-dismiss functionality with appropriate timing
- Allow manual dismissal with localized close buttons
- Stack multiple notifications properly with RTL positioning
- Use appropriate timing for auto-dismiss based on content length
- Display Laravel session flash messages appropriately

**Don't**:
- Don't auto-dismiss critical notifications without user acknowledgment
- Don't stack too many notifications or ignore RTL layout
- Don't ignore accessibility announcements for screen readers
- Don't use toasts for persistent information
- Don't display non-translatable notification content

---

## 2030b/app/View/Components/UI/Tooltip.php

**Purpose**: Tooltip component for providing contextual help and additional information with multilingual content and RTL-aware positioning.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(string $content, string $position = 'top', string $trigger = 'hover'): void
- render(): View
- getTooltipClasses(): string
- getPositionClasses(): string
- getTriggerAttributes(): array
- getLocalizedContent(): string
- getRTLPosition(): string

**Do**:
- Support keyboard focus for accessibility in all languages
- Position tooltips to stay in viewport with RTL considerations
- Include proper ARIA attributes with localized labels
- Support both hover and click triggers
- Keep tooltip content concise and properly localized
- Adjust tooltip positioning for RTL layouts automatically

**Don't**:
- Don't put essential information only in tooltips
- Don't ignore keyboard accessibility for multilingual users
- Don't position tooltips outside viewport in RTL layouts
- Don't use tooltips for lengthy content
- Don't display non-translatable tooltip content

---

## 2030b/app/View/Components/Charts/BarChart.php

**Purpose**: Bar chart component for displaying statistical data and metrics visualization with multilingual labels and RTL layout support.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Collection;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(array $data = [], array $labels = [], array $options = []): void
- render(): View
- prepareChartData(): array
- getChartOptions(): array
- formatDataForChart(): array
- getLocalizedLabels(): array
- getRTLChartOptions(): array

**Do**:
- Support responsive chart rendering with RTL layout considerations
- Include proper chart legends and labels in multiple languages
- Handle empty data states gracefully with localized messages
- Provide chart accessibility features for screen readers
- Support multiple data series with localized series names
- Format numbers and currencies according to locale preferences

**Don't**:
- Don't render charts without proper data validation
- Don't ignore accessibility requirements for multilingual users
- Don't make charts too complex to understand
- Don't skip responsive design considerations for RTL layouts
- Don't use non-localized chart labels or legends

---

## 2030b/app/View/Components/Charts/LineChart.php

**Purpose**: Line chart component for displaying trends and time-series data visualization with multilingual time formatting and RTL support.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Collection;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(array $datasets = [], array $labels = [], array $options = []): void
- render(): View
- prepareTimeSeriesData(): array
- getLineChartOptions(): array
- formatTrendData(): array
- getLocalizedTimeLabels(): array
- getRTLLineChartOptions(): array

**Do**:
- Support time-based data visualization with proper locale formatting
- Include trend line calculations with localized descriptions
- Handle missing data points appropriately
- Provide interactive chart features with multilingual tooltips
- Support multiple data series comparison with localized legends
- Format dates and times according to user locale

**Don't**:
- Don't connect disconnected data points inappropriately
- Don't ignore time axis formatting for different locales
- Don't make trend lines too complex to interpret
- Don't skip data point validation
- Don't use non-localized time and date labels

---

## 2030b/app/View/Components/Charts/PieChart.php

**Purpose**: Pie chart component for displaying proportional data and percentage breakdowns with multilingual segment labels and RTL layout.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Collection;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(array $data = [], array $labels = [], array $colors = []): void
- render(): View
- calculatePercentages(): array
- generateColors(): array
- preparePieData(): array
- getLocalizedSegmentLabels(): array
- getRTLPieChartOptions(): array

**Do**:
- Calculate and display accurate percentages with locale-appropriate formatting
- Use distinguishable colors for segments with RTL legend positioning
- Include interactive legend functionality with multilingual labels
- Handle small segments appropriately
- Support donut chart variations
- Format percentages according to locale conventions

**Don't**:
- Don't use too many pie segments that become unreadable
- Don't use similar colors for adjacent segments
- Don't ignore percentage calculation accuracy
- Don't make pie charts for non-proportional data
- Don't use non-localized segment labels

---

## 2030b/app/View/Components/Course/Card.php

**Purpose**: Course card component for displaying course information in grid layouts with thumbnail, title, description, rating, and enrollment data, fully integrated with Spatie Permission and Media Library.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use App\Models\Course\DocuCourse;
use App\DTOs\Course\CourseData;
use App\Enums\CourseStatus;
use App\Enums\ContentAccessLevel;
use App\Enums\DifficultyLevel;
use App\Services\Course\CourseService;
use App\Traits\HasMedia;
use App\Contracts\Services\CourseServiceInterface;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Collection;
use Spatie\Permission\Traits\HasPermissions;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(DocuCourse $course, ?string $size = 'medium'): void
- render(): View
- shouldShow(): bool
- getProgressPercentage(): int
- getThumbnailUrl(): string
- getFormattedPrice(): string
- getAccessBadge(): string
- getDifficultyBadge(): string
- getEnrollmentCount(): int
- isEnrolled(): bool
- canAccess(): bool
- getLocalizedCourseInfo(): array
- getOptimizedThumbnail(): ?string

**Do**:
- Cache course thumbnail URLs optimized via Spatie Media Library for performance
- Use CourseService for business logic calculations with permission checks
- Implement responsive card sizes (small, medium, large) with RTL layout support
- Show appropriate access badges based on Spatie Permission user permissions
- Display localized content based on user language preferences
- Use proper image optimization for thumbnails via Spatie Media Library
- Include accessibility attributes for screen readers in multiple languages
- Integrate with Laravel Breeze authentication for enrollment status
- Format pricing according to locale currency conventions

**Don't**:
- Don't query the database directly in the component
- Don't hardcode card dimensions or styling that breaks RTL layouts
- Don't expose sensitive course data to unauthorized users
- Don't bypass course access level validation via Spatie Permission
- Don't render incomplete or corrupted course data
- Don't use non-optimized images or non-translatable course content
- Don't display pricing without proper locale formatting

---

## 2030b/app/View/Components/Course/Grid.php

**Purpose**: Course grid container component that manages layout and pagination for course card collections with filtering, sorting capabilities, and permission-based course visibility.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Pagination\LengthAwarePaginator;
use App\Models\Course\DocuCourse;
use App\Models\Course\Category;
use App\DTOs\Course\CourseData;
use App\Services\Course\CourseService;
use App\Contracts\Services\CourseServiceInterface;
use App\Enums\CourseStatus;
use App\Enums\DifficultyLevel;
use Illuminate\Support\Collection;
use Illuminate\Database\Eloquent\Builder;
use Spatie\Permission\Traits\HasPermissions;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(Collection $courses, ?array $filters = [], ?string $layout = 'grid'): void
- render(): View
- getFilteredCourses(): Collection
- applyFilters(Builder $query): Builder
- getSortOptions(): array
- getLayoutClasses(): string
- getPaginationInfo(): array
- getGridColumns(): int
- hasActiveFilters(): bool
- getCategoryFilter(): ?Category
- getDifficultyFilter(): ?string
- getSearchQuery(): ?string
- getPermissionFilteredCourses(): Collection
- getLocalizedFilterOptions(): array

**Do**:
- Implement lazy loading for large course collections with proper caching
- Use efficient pagination with proper indexing and permission filtering
- Cache filtered results when appropriate while respecting permission changes
- Support multiple grid layouts (2, 3, 4 columns) with RTL considerations
- Implement proper search functionality with full-text search in multiple languages
- Use responsive design for mobile compatibility with RTL layout support
- Include filter persistence across page loads and language switches
- Filter courses based on Spatie Permission user access levels
- Display localized category names and difficulty levels

**Don't**:
- Don't load all courses at once without pagination limits
- Don't ignore user access permissions in filtering via Spatie Permission
- Don't bypass course status validation
- Don't render courses with missing required data
- Don't use inefficient database queries for filtering
- Don't display courses without proper authorization checks
- Don't use non-localized filter labels or search functionality

---

## 2030b/app/View/Components/Course/Progress.php

**Purpose**: Course progress visualization component showing completion percentage, time spent, and milestone achievements for enrolled users with localized progress descriptions.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use App\Models\User\UserProgress;
use App\Models\Course\DocuCourse;
use App\Models\User;
use App\DTOs\User\ProgressData;
use App\Services\User\UserService;
use App\Enums\UserLevel;
use App\Contracts\Services\UserServiceInterface;
use Illuminate\Support\Facades\Auth;
use Carbon\Carbon;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(DocuCourse $course, ?User $user = null): void
- render(): View
- getProgressPercentage(): float
- getCompletedEpisodes(): int
- getTotalEpisodes(): int
- getTimeSpent(): int
- getFormattedTimeSpent(): string
- getEstimatedTimeRemaining(): int
- getNextEpisode(): ?Episode
- getMilestones(): array
- getAchievements(): array
- isCompleted(): bool
- getCompletionDate(): ?Carbon
- getProgressBadge(): string
- getLocalizedProgressInfo(): array
- getLocalizedTimeEstimates(): array

**Do**:
- Calculate progress based on actual episode completion with proper validation
- Include time tracking for accurate progress metrics with locale formatting
- Show relevant milestones and achievements with localized descriptions
- Use progress caching for frequently accessed data while respecting real-time updates
- Display estimated completion time based on user pace with locale time formatting
- Include visual progress indicators and animations
- Track and display learning streaks with proper localization
- Integrate with Laravel Breeze authentication for user progress tracking

**Don't**:
- Don't show progress for unenrolled users or bypass enrollment validation
- Don't calculate progress without proper episode completion validation
- Don't ignore episode dependencies and prerequisites
- Don't display inaccurate time estimates or non-localized time formatting
- Don't bypass user privacy settings for progress sharing
- Don't use non-translatable progress descriptions or milestone names

---

## 2030b/app/View/Components/Course/Rating.php

**Purpose**: Course rating display and submission component with star ratings, review counts, and user rating submission interface, integrated with Spatie Permission and localization.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use App\Models\Course\DocuCourse;
use App\Models\User;
use App\Models\User\UserReaction;
use App\DTOs\User\ReactionData;
use App\Services\Course\CourseService;
use App\Enums\ContentType;
use App\Contracts\Services\CourseServiceInterface;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Collection;
use Spatie\Permission\Traits\HasPermissions;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(DocuCourse $course, bool $allowRating = true): void
- render(): View
- getAverageRating(): float
- getTotalRatings(): int
- getRatingDistribution(): array
- getUserRating(): ?int
- hasUserRated(): bool
- canUserRate(): bool
- getStarDisplay(): array
- getFormattedRating(): string
- getRatingBreakdown(): array
- getRecentReviews(): Collection
- submitRating(int $rating, ?string $comment = null): bool
- getLocalizedRatingLabels(): array
- getLocalizedReviews(): Collection

**Do**:
- Only allow authenticated users via Laravel Breeze to submit ratings
- Prevent multiple ratings from the same user with proper validation
- Validate rating values (1-5 stars) and sanitize review comments
- Cache average ratings for performance while maintaining accuracy
- Display rating distribution with visual bars and localized labels
- Include anti-spam measures for rating submission
- Show helpful rating statistics and trends with proper localization
- Integrate with Spatie Permission to control rating submission access
- Display reviews in user's preferred language when available

**Don't**:
- Don't allow rating manipulation or fake reviews
- Don't display ratings for unpublished courses or bypass access controls
- Don't ignore rating validation rules or permission checks
- Don't show inappropriate or flagged reviews without moderation
- Don't bypass course access requirements for rating submission
- Don't display non-localized rating labels or review content

---

## 2030b/app/View/Components/Course/Player.php

**Purpose**: Course episode player component with video/audio playback, transcript display, note-taking, and progress tracking functionality, integrated with Spatie Media Library and permission system.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use App\Models\Course\Episode;
use App\Models\Course\EpisodePart;
use App\Models\User\UserProgress;
use App\Models\User\UserNote;
use App\DTOs\Course\EpisodeData;
use App\Services\Course\CourseService;
use App\Services\Media\MediaService;
use App\Contracts\Services\MediaServiceInterface;
use App\Enums\ContentType;
use App\Enums\ContentAccessLevel;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Illuminate\Support\Facades\Auth;
use Spatie\Permission\Traits\HasPermissions;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- __construct(Episode $episode, ?EpisodePart $currentPart = null): void
- render(): View
- getMediaUrl(): string
- getTranscriptData(): ?array
- getUserNotes(): Collection
- getPlaybackPosition(): int
- canAccess(): bool
- hasPrerequisites(): bool
- getVideoQualityOptions(): array
- getPlaybackSpeed(): float
- getSubtitleTracks(): array
- saveProgress(int $position): bool
- addNote(string $content, int $timestamp): bool
- getChapterMarkers(): array
- getOptimizedMediaUrl(): string
- getLocalizedControls(): array
- getLocalizedTranscript(): ?array

**Do**:
- Implement secure media streaming via Spatie Media Library with access control
- Track playback position for resume functionality with proper validation
- Support multiple video qualities and playback speeds
- Include subtitle/caption support for accessibility in multiple languages
- Implement note-taking with timestamp synchronization and proper validation
- Use progressive loading for large media files via Spatie Media Library
- Include keyboard shortcuts for player control with localized help text
- Integrate with Spatie Permission for content access validation
- Display localized player controls and transcript content
- Integrate with Laravel Breeze authentication for user progress tracking

**Don't**:
- Don't expose media URLs without proper authentication and permission validation
- Don't allow access to restricted content or bypass Spatie Permission checks
- Don't ignore prerequisite episode requirements
- Don't bypass user enrollment validation via Laravel Breeze
- Don't store unencrypted media URLs in client-side code
- Don't display non-localized player interface or transcript content
- Don't allow note creation without proper user authentication

---

## 2030b/app/View/Composers/UserComposer.php

**Purpose**: View composer that provides user-related data to multiple views including authentication status, profile information, user preferences, and localized user data.

**Dependencies**:
```php
use Illuminate\View\View;
use Illuminate\Contracts\View\ViewComposer;
use App\Models\User;
use App\Models\User\UserProfile;
use App\DTOs\User\ProfileData;
use App\Services\User\UserService;
use App\Contracts\Services\UserServiceInterface;
use App\Enums\UserStatus;
use App\Enums\UserLevel;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Cache;
use Spatie\Permission\Traits\HasPermissions;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- compose(View $view): void
- getUserData(): ?array
- getAuthenticationStatus(): array
- getUserPreferences(): array
- getUserStatistics(): array
- getNotificationCount(): int
- getUserLevel(): array
- getProgressSummary(): array
- getRecentActivity(): array
- getUserPermissions(): array
- getCachedUserData(): ?array
- clearUserCache(): void
- getLocalizedUserData(): array
- getUserAvatarUrl(): ?string

**Do**:
- Cache user data for frequently accessed information with proper invalidation
- Include user authentication and authorization status via Laravel Breeze
- Provide user preferences for UI customization including language and RTL settings
- Include notification counts and recent activity with proper localization
- Use efficient queries for user statistics and progress summaries
- Handle guest users and unauthenticated states properly
- Implement proper cache invalidation strategies when user data changes
- Integrate user roles and permissions via Spatie Permission package
- Display optimized user avatars via Spatie Media Library
- Provide localized user interface elements based on user language preference

**Don't**:
- Don't expose sensitive user information to views without permission validation
- Don't query database on every view render without proper caching
- Don't ignore user privacy settings or bypass permission checks
- Don't include unnecessary user data in view context
- Don't bypass Laravel Breeze authentication checks
- Don't cache user permissions longer than session duration
- Don't display non-localized user interface elements

---

## 2030b/app/View/Composers/CourseComposer.php

**Purpose**: View composer that provides course-related data including categories, featured courses, user enrollments, and course statistics for navigation and dashboard views with permission filtering and localization.

**Dependencies**:
```php
use Illuminate\View\View;
use Illuminate\Contracts\View\ViewComposer;
use App\Models\Course\DocuCourse;
use App\Models\Course\Category;
use App\DTOs\Course\CourseData;
use App\Services\Course\CourseService;
use App\Contracts\Services\CourseServiceInterface;
use App\Enums\CourseStatus;
use App\Enums\ContentAccessLevel;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Collection;
use Spatie\Permission\Traits\HasPermissions;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- compose(View $view): void
- getFeaturedCourses(): Collection
- getCourseCategories(): Collection
- getUserEnrollments(): Collection
- getCourseStatistics(): array
- getPopularCourses(): Collection
- getRecentCourses(): Collection
- getRecommendedCourses(): Collection
- getCategoryNavigation(): array
- getEnrollmentProgress(): array
- getCachedCourseData(): ?array
- refreshCourseCache(): void
- getPermissionFilteredCourses(): Collection
- getLocalizedCourseData(): array

**Do**:
- Cache course listings and statistics for performance with proper invalidation
- Filter courses based on user access levels via Spatie Permission
- Include course enrollment and progress information for authenticated users
- Provide category hierarchy for navigation with multilingual support
- Implement course recommendation logic based on user preferences and permissions
- Use efficient database queries with proper indexing and permission filtering
- Include localized course content based on user language preferences
- Integrate with Laravel Breeze authentication for personalized course recommendations
- Display course thumbnails optimized via Spatie Media Library

**Don't**:
- Don't load all courses without pagination limits or permission filtering
- Don't expose restricted course information or bypass access level validation
- Don't ignore course publication status or permission requirements
- Don't bypass user access level validation via Spatie Permission
- Don't include deprecated or archived courses in active listings
- Don't cache course data without considering permission changes
- Don't display non-localized course content or category names

---

## 2030b/app/View/Composers/NotificationComposer.php

**Purpose**: View composer that manages notification data including unread counts, recent notifications, and notification preferences for display in layouts and navigation with localization support.

**Dependencies**:
```php
use Illuminate\View\View;
use Illuminate\Contracts\View\ViewComposer;
use App\Models\User;
use App\Services\Notification\NotificationService;
use App\Contracts\Services\NotificationServiceInterface;
use App\DTOs\System\ActivityLogData;
use Illuminate\Notifications\DatabaseNotification;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Collection;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
```

**Methods**:
- compose(View $view): void
- getUnreadCount(): int
- getRecentNotifications(): Collection
- getNotificationTypes(): array
- getNotificationPreferences(): array
- markAsRead(string $notificationId): bool
- markAllAsRead(): bool
- getSystemAlerts(): Collection
- getCourseNotifications(): Collection
- getPaymentNotifications(): Collection
- getAffiliateNotifications(): Collection
- getCachedNotifications(): ?Collection
- getLocalizedNotifications(): Collection

**Do**:
- Cache notification counts to reduce database queries with proper cache invalidation
- Group notifications by type and priority with localized content
- Implement real-time notification updates where appropriate
- Respect user notification preferences and privacy settings
- Include notification read/unread status tracking with proper validation
- Provide notification filtering and search functionality
- Handle notification cleanup and archiving appropriately
- Display notifications in user's preferred language
- Integrate with Laravel Breeze authentication for user-specific notifications

**Don't**:
- Don't send notifications to users who opted out or lack proper permissions
- Don't expose other users' private notifications or bypass privacy settings
- Don't ignore notification delivery preferences or user language settings
- Don't overwhelm users with excessive notifications
- Don't store notifications indefinitely without proper cleanup policies
- Don't display notifications without proper localization
- Don't cache notifications without considering privacy and permission changes

---

## 2030b/app/View/Composers/LayoutComposer.php

**Purpose**: Global layout view composer that provides application-wide data including navigation menus, footer content, meta information, theme settings, and multilingual layout configuration for all layout templates.

**Dependencies**:
```php
use Illuminate\View\View;
use Illuminate\Contracts\View\ViewComposer;
use App\Models\System\Configuration;
use App\Models\System\Language;
use App\Services\System\ConfigurationService;
use App\Contracts\Services\CacheServiceInterface;
use App\Enums\ConfigType;
use App\Enums\LanguageDirection;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\App;
use Illuminate\Support\Collection;
use Mcamara\LaravelLocalization\Facades\LaravelLocalization;
use Spatie\Permission\Traits\HasPermissions;
```

**Methods**:
- compose(View $view): void
- getNavigationMenu(): array
- getFooterContent(): array
- getMetaInformation(): array
- getThemeSettings(): array
- getLanguageOptions(): Collection
- getCurrentLanguage(): Language
- getBreadcrumbs(): array
- getSearchConfiguration(): array
- getSocialLinks(): array
- getApplicationSettings(): array
- getLayoutConfiguration(): array
- getCachedLayoutData(): ?array
- getPermissionBasedNavigation(): array
- getRTLLayoutSettings(): array

**Do**:
- Cache navigation and footer content for performance with proper cache invalidation
- Include SEO meta information and structured data with multilingual support
- Support multi-language navigation and content via mcamara/laravel-localization
- Provide theme customization and user preferences including RTL/LTR layouts
- Include application configuration and feature flags
- Implement breadcrumb navigation for all pages with localized navigation labels
- Use CDN URLs for static assets and optimized media via Spatie Media Library
- Filter navigation items based on Spatie Permission user roles and permissions
- Support automatic RTL layout switching for Arabic and other RTL languages
- Integrate with Laravel Breeze authentication status for layout customization

**Don't**:
- Don't include sensitive configuration in views or expose internal system information
- Don't ignore user language and locale preferences or RTL layout requirements
- Don't load unnecessary layout data on every request without proper caching
- Don't expose navigation items without proper permission validation
- Don't hardcode navigation or footer content without localization support
- Don't cache layout data without considering permission and language changes
- Don't display non-localized meta information or navigation labels
