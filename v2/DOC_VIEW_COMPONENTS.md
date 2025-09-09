# DOC_VIEW_COMPONENTS.md

## 2030b/app/View/Components/AppLayout.php

**Purpose**: Main application layout component providing authenticated user interface structure with navigation, sidebar, and content areas.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Facades\Auth;
use App\Models\User;
use App\Services\Notification\NotificationService;
use Spatie\Permission\Traits\HasRoles;
```

**Methods**:
- __construct(): void
- render(): View
- getUser(): ?User
- getNotifications(): array
- getNavigationItems(): array
- getUserPermissions(): array

**Do**:
- Provide consistent layout structure across application
- Include navigation based on user permissions
- Display user-specific notifications and alerts
- Implement responsive design patterns
- Cache navigation data for performance

**Don't**:
- Don't include business logic in layout components
- Don't expose sensitive user data in templates
- Don't ignore mobile responsiveness
- Don't hardcode navigation items

---

## 2030b/app/View/Components/GuestLayout.php

**Purpose**: Guest layout component for unauthenticated users providing clean interface for authentication and public pages.

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
- getPublicNavigationItems(): array
- getAvailableLocales(): array
- getMetaTags(): array

**Do**:
- Provide clean, minimal layout for guests
- Include language switching functionality
- Implement proper SEO meta tags
- Ensure accessibility compliance
- Support RTL languages properly

**Don't**:
- Don't include authenticated user features
- Don't display private navigation items
- Don't ignore SEO requirements
- Don't skip accessibility features

---

## 2030b/app/View/Components/Button.php

**Purpose**: Reusable button component with consistent styling, variants, and interactive states.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
```

**Methods**:
- __construct(string $type = 'button', string $variant = 'primary', string $size = 'md', bool $disabled = false): void
- render(): View
- getButtonClasses(): string
- getVariantClasses(): string
- getSizeClasses(): string

**Do**:
- Provide consistent button styling across application
- Support multiple button variants and sizes
- Include proper accessibility attributes
- Handle loading and disabled states
- Support icon placement within buttons

**Don't**:
- Don't create buttons without proper semantic meaning
- Don't ignore accessibility requirements
- Don't hardcode styling in templates
- Don't skip keyboard navigation support

---

## 2030b/app/View/Components/Input.php

**Purpose**: Form input component with validation states, error handling, and consistent styling.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\ViewErrorBag;
```

**Methods**:
- __construct(string $name, string $type = 'text', ?string $value = null, bool $required = false): void
- render(): View
- hasError(): bool
- getErrorMessage(): ?string
- getInputClasses(): string

**Do**:
- Display validation errors appropriately
- Support various input types and attributes
- Include proper form labels and descriptions
- Implement consistent input styling
- Handle value binding correctly

**Don't**:
- Don't ignore validation error display
- Don't skip accessibility labels
- Don't hardcode input attributes
- Don't forget to sanitize input values

---

## 2030b/app/View/Components/Label.php

**Purpose**: Form label component ensuring proper form accessibility and consistent label styling.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
```

**Methods**:
- __construct(string $for, bool $required = false): void
- render(): View
- getLabelClasses(): string
- getRequiredIndicator(): string

**Do**:
- Associate labels with form inputs properly
- Indicate required fields clearly
- Provide consistent label styling
- Support internationalization
- Include accessibility attributes

**Don't**:
- Don't create labels without proper 'for' attributes
- Don't ignore required field indicators
- Don't skip internationalization support
- Don't use ambiguous label text

---

## 2030b/app/View/Components/Dropdown.php

**Purpose**: Dropdown menu component for navigation menus, action lists, and selection interfaces.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Collection;
```

**Methods**:
- __construct(array $items = [], string $trigger = 'click', string $position = 'bottom-left'): void
- render(): View
- getDropdownItems(): Collection
- getPositionClasses(): string
- getTriggerAttributes(): array

**Do**:
- Support keyboard navigation
- Handle click outside to close
- Position dropdowns properly to stay in viewport
- Include proper ARIA attributes
- Support nested dropdown items

**Don't**:
- Don't ignore keyboard accessibility
- Don't position dropdowns outside viewport
- Don't skip click outside handling
- Don't forget ARIA attributes

---

## 2030b/app/View/Components/Modal.php

**Purpose**: Modal dialog component for overlays, confirmations, and form dialogs with proper accessibility.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
```

**Methods**:
- __construct(string $id, string $size = 'md', bool $closable = true, bool $backdrop = true): void
- render(): View
- getModalClasses(): string
- getSizeClasses(): string
- getBackdropAttributes(): array

**Do**:
- Implement proper modal accessibility (ARIA, focus management)
- Support different modal sizes
- Handle escape key to close
- Prevent background scrolling when open
- Include proper modal headers and actions

**Don't**:
- Don't ignore focus management
- Don't skip escape key handling
- Don't forget to prevent background scroll
- Don't create modals without proper headers

---

## 2030b/app/View/Components/Alert.php

**Purpose**: Alert notification component for displaying messages, warnings, errors, and success notifications.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
```

**Methods**:
- __construct(string $type = 'info', bool $dismissible = false, ?string $title = null): void
- render(): View
- getAlertClasses(): string
- getTypeClasses(): string
- getIconClass(): string

**Do**:
- Support different alert types (success, error, warning, info)
- Include appropriate icons for alert types
- Make alerts dismissible when appropriate
- Use semantic HTML for screen readers
- Support auto-dismiss functionality

**Don't**:
- Don't use alerts for critical error messages that need attention
- Don't make important alerts auto-dismissible
- Don't ignore accessibility requirements
- Don't use generic styling for different alert types

---

## 2030b/app/View/Components/Card.php

**Purpose**: Card component for content containers, course displays, and structured content presentation.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
```

**Methods**:
- __construct(?string $title = null, ?string $subtitle = null, bool $bordered = true, bool $shadow = false): void
- render(): View
- getCardClasses(): string
- hasHeader(): bool
- hasFooter(): bool

**Do**:
- Provide flexible card structure for various content types
- Support optional headers, footers, and actions
- Include proper spacing and typography
- Support card variants and styling options
- Make cards responsive and accessible

**Don't**:
- Don't create cards without proper semantic structure
- Don't ignore responsive design requirements
- Don't hardcode card content structure
- Don't skip accessibility considerations

---

## 2030b/app/View/Components/Badge.php

**Purpose**: Badge component for status indicators, labels, and notification counts.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
```

**Methods**:
- __construct(string $variant = 'default', string $size = 'sm', bool $rounded = false): void
- render(): View
- getBadgeClasses(): string
- getVariantClasses(): string
- getSizeClasses(): string

**Do**:
- Support different badge variants and colors
- Provide appropriate sizing options
- Use semantic meaning for badge colors
- Support both text and numeric badges
- Include proper contrast for accessibility

**Don't**:
- Don't use badges for interactive elements
- Don't ignore color contrast requirements
- Don't create badges without semantic meaning
- Don't make badges too large or prominent

---

## 2030b/app/View/Components/Form/Select.php

**Purpose**: Select dropdown component for form selections with validation and styling consistency.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Collection;
use Illuminate\Support\ViewErrorBag;
```

**Methods**:
- __construct(string $name, array $options = [], ?string $selected = null, bool $multiple = false): void
- render(): View
- getOptions(): Collection
- hasError(): bool
- getSelectClasses(): string
- isSelected(string $value): bool

**Do**:
- Support single and multiple selections
- Handle validation errors appropriately
- Include proper option grouping
- Support search functionality for large option lists
- Maintain selected state correctly

**Don't**:
- Don't create selects without proper option values
- Don't ignore validation error display
- Don't skip accessibility attributes
- Don't hardcode option lists

---

## 2030b/app/View/Components/Form/Checkbox.php

**Purpose**: Checkbox input component with proper labeling, validation, and styling consistency.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\ViewErrorBag;
```

**Methods**:
- __construct(string $name, ?string $value = null, bool $checked = false, ?string $label = null): void
- render(): View
- isChecked(): bool
- hasError(): bool
- getCheckboxClasses(): string

**Do**:
- Associate checkboxes with proper labels
- Handle checked state correctly
- Support indeterminate state when needed
- Display validation errors appropriately
- Include proper accessibility attributes

**Don't**:
- Don't create checkboxes without labels
- Don't ignore checked state handling
- Don't skip validation error display
- Don't forget accessibility requirements

---

## 2030b/app/View/Components/Form/Radio.php

**Purpose**: Radio button component for exclusive selections with proper grouping and validation.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\ViewErrorBag;
```

**Methods**:
- __construct(string $name, string $value, ?string $checked = null, ?string $label = null): void
- render(): View
- isChecked(): bool
- hasError(): bool
- getRadioClasses(): string

**Do**:
- Group radio buttons with same name attribute
- Handle checked state for radio groups
- Provide clear labels for each option
- Display validation errors for the group
- Support keyboard navigation between options

**Don't**:
- Don't create ungrouped radio buttons
- Don't ignore radio group validation
- Don't skip keyboard navigation support
- Don't use radio buttons for non-exclusive choices

---

## 2030b/app/View/Components/Form/Textarea.php

**Purpose**: Textarea component for multi-line text input with character counting and validation.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\ViewErrorBag;
```

**Methods**:
- __construct(string $name, ?string $value = null, int $rows = 4, ?int $maxlength = null): void
- render(): View
- hasError(): bool
- getTextareaClasses(): string
- shouldShowCharacterCount(): bool

**Do**:
- Support resizable textareas appropriately
- Include character counting when needed
- Display validation errors clearly
- Support auto-resize functionality
- Handle placeholder text properly

**Don't**:
- Don't ignore character limit validation
- Don't make textareas too small for content
- Don't skip validation error display
- Don't forget proper labeling

---

## 2030b/app/View/Components/Layout/Sidebar.php

**Purpose**: Sidebar navigation component for main application navigation with collapsible functionality.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Facades\Auth;
use App\Models\User;
use Spatie\Permission\Models\Permission;
```

**Methods**:
- __construct(bool $collapsible = true): void
- render(): View
- getNavigationItems(): array
- getUserPermissions(): array
- isActiveRoute(string $route): bool
- canAccessRoute(string $route): bool

**Do**:
- Filter navigation based on user permissions
- Highlight active navigation items
- Support collapsible sidebar functionality
- Group navigation items logically
- Include proper accessibility navigation

**Don't**:
- Don't show restricted navigation items
- Don't ignore active state highlighting
- Don't create deep navigation hierarchies
- Don't skip accessibility navigation attributes

---

## 2030b/app/View/Components/Layout/Header.php

**Purpose**: Application header component with user menu, notifications, and global navigation elements.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Facades\Auth;
use App\Services\Notification\NotificationService;
```

**Methods**:
- __construct(): void
- render(): View
- getUser(): ?User
- getUnreadNotifications(): int
- getUserMenu(): array
- getGlobalActions(): array

**Do**:
- Display user authentication status
- Show unread notification counts
- Provide user profile and settings access
- Include global search functionality
- Support responsive header layout

**Don't**:
- Don't expose sensitive user information
- Don't ignore notification count accuracy
- Don't make header too cluttered
- Don't skip mobile responsiveness

---

## 2030b/app/View/Components/Layout/Footer.php

**Purpose**: Application footer component with links, copyright, and supplementary information.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Facades\Config;
```

**Methods**:
- __construct(): void
- render(): View
- getFooterLinks(): array
- getCopyrightText(): string
- getAppVersion(): string
- getSocialLinks(): array

**Do**:
- Include relevant footer links and information
- Display copyright and legal information
- Provide social media and contact links
- Support multi-language footer content
- Keep footer content up to date

**Don't**:
- Don't include too many footer links
- Don't ignore copyright date updates
- Don't skip accessibility in footer navigation
- Don't make footer content too prominent

---

## 2030b/app/View/Components/UI/LoadingSpinner.php

**Purpose**: Loading spinner component for indicating processing states and async operations.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
```

**Methods**:
- __construct(string $size = 'md', string $variant = 'primary', ?string $text = null): void
- render(): View
- getSpinnerClasses(): string
- getSizeClasses(): string
- shouldShowText(): bool

**Do**:
- Provide different spinner sizes and styles
- Include accessible loading text
- Support overlay and inline spinner modes
- Use consistent animation timing
- Hide spinner when loading completes

**Don't**:
- Don't use spinners without accessible text
- Don't make spinners too prominent
- Don't forget to hide spinners after loading
- Don't use spinners for instant operations

---

## 2030b/app/View/Components/UI/ProgressBar.php

**Purpose**: Progress bar component for displaying completion status, upload progress, and step indicators.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
```

**Methods**:
- __construct(float $progress = 0, string $variant = 'primary', bool $striped = false, bool $animated = false): void
- render(): View
- getProgressClasses(): string
- getProgressWidth(): string
- isComplete(): bool

**Do**:
- Display accurate progress percentages
- Support different progress bar styles
- Include accessible progress text
- Handle progress bar animations smoothly
- Indicate completion states clearly

**Don't**:
- Don't display inaccurate progress values
- Don't ignore accessibility requirements
- Don't animate progress bars unnecessarily
- Don't make progress bars too small to see

---

## 2030b/app/View/Components/UI/ToastNotification.php

**Purpose**: Toast notification component for temporary messages and user feedback.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
```

**Methods**:
- __construct(string $type = 'info', int $duration = 5000, bool $closable = true): void
- render(): View
- getToastClasses(): string
- getTypeClasses(): string
- getAutoCloseAttributes(): array

**Do**:
- Support different notification types
- Include auto-dismiss functionality
- Allow manual dismissal
- Stack multiple notifications properly
- Use appropriate timing for auto-dismiss

**Don't**:
- Don't auto-dismiss critical notifications
- Don't stack too many notifications
- Don't ignore accessibility announcements
- Don't use toasts for persistent information

---

## 2030b/app/View/Components/UI/Tooltip.php

**Purpose**: Tooltip component for providing contextual help and additional information on hover or focus.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
```

**Methods**:
- __construct(string $content, string $position = 'top', string $trigger = 'hover'): void
- render(): View
- getTooltipClasses(): string
- getPositionClasses(): string
- getTriggerAttributes(): array

**Do**:
- Support keyboard focus for accessibility
- Position tooltips to stay in viewport
- Include proper ARIA attributes
- Support both hover and click triggers
- Keep tooltip content concise

**Don't**:
- Don't put essential information only in tooltips
- Don't ignore keyboard accessibility
- Don't position tooltips outside viewport
- Don't use tooltips for lengthy content

---

## 2030b/app/View/Components/Charts/BarChart.php

**Purpose**: Bar chart component for displaying statistical data and metrics visualization.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Collection;
```

**Methods**:
- __construct(array $data = [], array $labels = [], array $options = []): void
- render(): View
- prepareChartData(): array
- getChartOptions(): array
- formatDataForChart(): array

**Do**:
- Support responsive chart rendering
- Include proper chart legends and labels
- Handle empty data states gracefully
- Provide chart accessibility features
- Support multiple data series

**Don't**:
- Don't render charts without proper data validation
- Don't ignore accessibility requirements
- Don't make charts too complex to understand
- Don't skip responsive design considerations

---

## 2030b/app/View/Components/Charts/LineChart.php

**Purpose**: Line chart component for displaying trends and time-series data visualization.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Collection;
```

**Methods**:
- __construct(array $datasets = [], array $labels = [], array $options = []): void
- render(): View
- prepareTimeSeriesData(): array
- getLineChartOptions(): array
- formatTrendData(): array

**Do**:
- Support time-based data visualization
- Include trend line calculations
- Handle missing data points appropriately
- Provide interactive chart features
- Support multiple data series comparison

**Don't**:
- Don't connect disconnected data points
- Don't ignore time axis formatting
- Don't make trend lines too complex
- Don't skip data point validation

---

## 2030b/app/View/Components/Charts/PieChart.php

**Purpose**: Pie chart component for displaying proportional data and percentage breakdowns.

**Dependencies**:
```php
use Illuminate\View\Component;
use Illuminate\View\View;
use Illuminate\Support\Collection;
```

**Methods**:
- __construct(array $data = [], array $labels = [], array $colors = []): void
- render(): View
- calculatePercentages(): array
- generateColors(): array
- preparePieData(): array

**Do**:
- Calculate and display accurate percentages
- Use distinguishable colors for segments
- Include interactive legend functionality
- Handle small segments appropriately
- Support donut chart variations

**Don't**:
- Don't use too many pie segments
- Don't use similar colors for adjacent segments
- Don't ignore percentage calculation accuracy
- Don't make pie charts for non-proportional data

---

## 2030b/app/View/Components/Course/Card.php

**Purpose**: Course card component for displaying course information in grid layouts with thumbnail, title, description, rating, and enrollment data.

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

**Do**:
- Cache course thumbnail URLs for performance
- Use CourseService for business logic calculations
- Implement responsive card sizes (small, medium, large)
- Show appropriate access badges based on user permissions
- Display localized content based on user language preferences
- Use proper image optimization for thumbnails
- Include accessibility attributes for screen readers

**Don't**:
- Don't query the database directly in the component
- Don't hardcode card dimensions or styling
- Don't expose sensitive course data to unauthorized users
- Don't bypass course access level validation
- Don't render incomplete or corrupted course data

---

## 2030b/app/View/Components/Course/Grid.php

**Purpose**: Course grid container component that manages layout and pagination for course card collections with filtering and sorting capabilities.

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

**Do**:
- Implement lazy loading for large course collections
- Use efficient pagination with proper indexing
- Cache filtered results when appropriate
- Support multiple grid layouts (2, 3, 4 columns)
- Implement proper search functionality with full-text search
- Use responsive design for mobile compatibility
- Include filter persistence across page loads

**Don't**:
- Don't load all courses at once without pagination
- Don't ignore user access permissions in filtering
- Don't bypass course status validation
- Don't render courses with missing required data
- Don't use inefficient database queries for filtering

---

## 2030b/app/View/Components/Course/Progress.php

**Purpose**: Course progress visualization component showing completion percentage, time spent, and milestone achievements for enrolled users.

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

**Do**:
- Calculate progress based on actual episode completion
- Include time tracking for accurate progress metrics
- Show relevant milestones and achievements
- Use progress caching for frequently accessed data
- Display estimated completion time based on user pace
- Include visual progress indicators and animations
- Track and display learning streaks

**Don't**:
- Don't show progress for unenrolled users
- Don't calculate progress without proper validation
- Don't ignore episode dependencies and prerequisites
- Don't display inaccurate time estimates
- Don't bypass user privacy settings for progress sharing

---

## 2030b/app/View/Components/Course/Rating.php

**Purpose**: Course rating display and submission component with star ratings, review counts, and user rating submission interface.

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

**Do**:
- Only allow authenticated users to submit ratings
- Prevent multiple ratings from the same user
- Validate rating values (1-5 stars)
- Cache average ratings for performance
- Display rating distribution with visual bars
- Include anti-spam measures for rating submission
- Show helpful rating statistics and trends

**Don't**:
- Don't allow rating manipulation or fake reviews
- Don't display ratings for unpublished courses
- Don't ignore rating validation rules
- Don't show inappropriate or flagged reviews
- Don't bypass course access requirements for rating

---

## 2030b/app/View/Components/Course/Player.php

**Purpose**: Course episode player component with video/audio playback, transcript display, note-taking, and progress tracking functionality.

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

**Do**:
- Implement secure media streaming with access control
- Track playback position for resume functionality
- Support multiple video qualities and playback speeds
- Include subtitle/caption support for accessibility
- Implement note-taking with timestamp synchronization
- Use progressive loading for large media files
- Include keyboard shortcuts for player control

**Don't**:
- Don't expose media URLs without proper authentication
- Don't allow access to restricted content
- Don't ignore prerequisite episode requirements
- Don't bypass user enrollment validation
- Don't store unencrypted media URLs in client-side code

---

## 2030b/app/View/Composers/UserComposer.php

**Purpose**: View composer that provides user-related data to multiple views including authentication status, profile information, and user preferences.

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

**Do**:
- Cache user data for frequently accessed information
- Include user authentication and authorization status
- Provide user preferences for UI customization
- Include notification counts and recent activity
- Use efficient queries for user statistics
- Handle guest users and unauthenticated states
- Implement proper cache invalidation strategies

**Don't**:
- Don't expose sensitive user information to views
- Don't query database on every view render
- Don't ignore user privacy settings
- Don't include unnecessary user data in view context
- Don't bypass authentication checks

---

## 2030b/app/View/Composers/CourseComposer.php

**Purpose**: View composer that provides course-related data including categories, featured courses, user enrollments, and course statistics for navigation and dashboard views.

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

**Do**:
- Cache course listings and statistics for performance
- Filter courses based on user access levels
- Include course enrollment and progress information
- Provide category hierarchy for navigation
- Implement course recommendation logic
- Use efficient database queries with proper indexing
- Include localized course content

**Don't**:
- Don't load all courses without pagination limits
- Don't expose restricted course information
- Don't ignore course publication status
- Don't bypass user access level validation
- Don't include deprecated or archived courses

---

## 2030b/app/View/Composers/NotificationComposer.php

**Purpose**: View composer that manages notification data including unread counts, recent notifications, and notification preferences for display in layouts and navigation.

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

**Do**:
- Cache notification counts to reduce database queries
- Group notifications by type and priority
- Implement real-time notification updates
- Respect user notification preferences
- Include notification read/unread status tracking
- Provide notification filtering and search
- Handle notification cleanup and archiving

**Don't**:
- Don't send notifications to users who opted out
- Don't expose other users' private notifications
- Don't ignore notification delivery preferences
- Don't overwhelm users with excessive notifications
- Don't store notifications indefinitely without cleanup

---

## 2030b/app/View/Composers/LayoutComposer.php

**Purpose**: Global layout view composer that provides application-wide data including navigation menus, footer content, meta information, and theme settings for all layout templates.

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

**Do**:
- Cache navigation and footer content for performance
- Include SEO meta information and structured data
- Support multi-language navigation and content
- Provide theme customization and user preferences
- Include application configuration and feature flags
- Implement breadcrumb navigation for all pages
- Use CDN URLs for static assets

**Don't**:
- Don't include sensitive configuration in views
- Don't ignore user language and locale preferences
- Don't load unnecessary layout data on every request
- Don't expose internal system information
- Don't hardcode navigation or footer content
