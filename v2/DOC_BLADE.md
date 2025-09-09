# DOC_BLADE.md

## 2030b/resources/views/layouts/app.blade.php

**Purpose**: Main authenticated user layout providing the base structure for all authenticated pages with navigation, header, sidebar, and footer components.

**Dependencies**:
- @vite directive for asset compilation
- @csrf directive for CSRF protection
- @auth directive for authentication checks
- @include('components.layout.header')
- @include('components.layout.sidebar')
- @include('components.layout.footer')
- @include('components.ui.toast-notification')
- Tailwind CSS framework
- Alpine.js for interactive components

**Sections/Slots**:
- @section('title') - Page title
- @section('meta') - Additional meta tags
- @section('styles') - Page-specific CSS
- @section('content') - Main page content
- @section('scripts') - Page-specific JavaScript

**Variables Used**:
- $user - Current authenticated user
- $notifications - User notifications
- $breadcrumbs - Page breadcrumb navigation
- $pageTitle - Current page title

**Do**:
- Use consistent navigation structure
- Include CSRF protection on all forms
- Implement responsive design patterns
- Cache static assets appropriately
- Use semantic HTML structure

**Don't**:
- Don't hardcode user data in templates
- Don't bypass authentication checks
- Don't include heavy JavaScript in layout
- Don't ignore accessibility standards
- Don't mix layout logic with business logic

## 2030b/resources/views/layouts/guest.blade.php

**Purpose**: Layout for unauthenticated users including login, register, and public pages with minimal navigation and branding.

**Dependencies**:
- @vite directive for asset compilation
- @csrf directive for forms
- @guest directive for guest-only content
- @include('components.application-logo')
- @include('components.auth-session-status')
- Tailwind CSS framework

**Sections/Slots**:
- @section('title') - Page title
- @section('content') - Main content area
- @section('scripts') - Page-specific scripts

**Variables Used**:
- $status - Session status messages
- $errors - Validation errors
- $pageTitle - Page title for SEO

**Do**:
- Keep design clean and focused
- Ensure fast loading times
- Include proper SEO meta tags
- Use clear call-to-action buttons
- Implement proper form validation

**Don't**:
- Don't show authenticated user content
- Don't include unnecessary navigation
- Don't use complex JavaScript
- Don't ignore mobile responsiveness
- Don't bypass security measures

## 2030b/resources/views/layouts/admin.blade.php

**Purpose**: Administrative interface layout with enhanced navigation, system monitoring widgets, and admin-specific functionality.

**Dependencies**:
- @vite directive for admin assets
- @can directive for permission checks
- @include('components.layout.header')
- @include('components.layout.sidebar')
- @include('components.ui.loading-spinner')
- @include('components.charts.bar-chart')
- Admin-specific CSS and JavaScript

**Sections/Slots**:
- @section('title') - Admin page title
- @section('content') - Main admin content
- @section('sidebar-extra') - Additional sidebar items
- @section('admin-scripts') - Admin-specific scripts

**Variables Used**:
- $adminUser - Current admin user
- $systemStats - System statistics
- $recentActivity - Recent system activity
- $permissions - Admin permissions

**Do**:
- Implement strict permission checks
- Include system monitoring tools
- Use admin-specific styling
- Provide comprehensive navigation
- Log all admin activities

**Don't**:
- Don't show sensitive data without authorization
- Don't bypass permission checks
- Don't include user-facing content
- Don't ignore audit logging
- Don't use insecure admin functions

## 2030b/resources/views/layouts/api.blade.php

**Purpose**: Layout for API documentation pages with clean, developer-friendly interface and code examples.

**Dependencies**:
- @vite directive for documentation assets
- Syntax highlighting library
- @include('components.ui.tooltip')
- Documentation-specific CSS
- Code formatting libraries

**Sections/Slots**:
- @section('title') - API page title
- @section('content') - API documentation content
- @section('code-examples') - Code example blocks
- @section('nav-sidebar') - API navigation sidebar

**Variables Used**:
- $apiVersion - Current API version
- $endpoints - Available API endpoints
- $authMethods - Authentication methods
- $examples - Code examples

**Do**:
- Provide clear code examples
- Include comprehensive endpoint documentation
- Use proper syntax highlighting
- Implement search functionality
- Keep navigation intuitive

**Don't**:
- Don't expose sensitive API keys
- Don't include outdated examples
- Don't ignore mobile compatibility
- Don't provide incomplete documentation
- Don't use complex layouts

## 2030b/resources/views/layouts/error.blade.php

**Purpose**: Minimal layout for error pages (403, 404, 500, etc.) with consistent branding and helpful navigation options.

**Dependencies**:
- @vite directive for minimal assets
- @include('components.application-logo')
- Basic error page styling
- Minimal JavaScript for analytics

**Sections/Slots**:
- @section('title') - Error page title
- @section('content') - Error message content
- @section('error-code') - HTTP error code

**Variables Used**:
- $errorCode - HTTP error status code
- $errorMessage - Error description
- $backUrl - Previous page URL
- $homeUrl - Home page URL

**Do**:
- Keep design simple and clean
- Provide helpful navigation options
- Include contact information
- Log error occurrences
- Maintain consistent branding

**Don't**:
- Don't expose system information
- Don't use complex layouts
- Don't ignore SEO considerations
- Don't show debug information in production
- Don't create confusing error messages
2. Components (38 files)

## 2030b/resources/views/components/application-logo.blade.php

**Purpose**: Reusable application logo component with responsive sizing and proper branding display.

**Dependencies**:
- SVG or image assets
- Responsive CSS classes
- Brand color variables

**Sections/Slots**:
- Default slot for logo content
- $size slot for different logo sizes

**Variables Used**:
- $size - Logo size (sm, md, lg, xl)
- $class - Additional CSS classes
- $alt - Alt text for accessibility

**Do**:
- Use vector formats for scalability
- Include proper alt text
- Maintain brand consistency
- Optimize for different screen sizes
- Include proper loading attributes

**Don't**:
- Don't use low-resolution images
- Don't ignore accessibility
- Don't hardcode dimensions
- Don't use inconsistent branding
- Don't forget loading optimization

## 2030b/resources/views/components/application-mark.blade.php

**Purpose**: Simplified brand mark or icon version of the logo for compact spaces and mobile navigation.

**Dependencies**:
- Brand icon assets
- Responsive utilities
- Color scheme variables

**Sections/Slots**:
- Default slot for mark content

**Variables Used**:
- $size - Mark size
- $color - Brand color variant
- $class - Additional styling

**Do**:
- Keep design minimal and recognizable
- Ensure good contrast ratios
- Use consistent brand colors
- Optimize for small sizes
- Include accessibility features

**Don't**:
- Don't make it too complex
- Don't use inappropriate colors
- Don't ignore mobile optimization
- Don't forget vector formats
- Don't compromise brand recognition

## 2030b/resources/views/components/auth-card.blade.php

**Purpose**: Styled card container for authentication forms with consistent spacing and visual hierarchy.

**Dependencies**:
- Card styling components
- Form layout utilities
- Authentication assets

**Sections/Slots**:
- $title slot for card header
- Default slot for card content
- $footer slot for card footer

**Variables Used**:
- $title - Card title
- $maxWidth - Maximum card width
- $class - Additional CSS classes

**Do**:
- Maintain consistent spacing
- Use proper form accessibility
- Include loading states
- Implement proper validation display
- Keep design clean and focused

**Don't**:
- Don't overcomplicate the design
- Don't ignore form validation
- Don't use inconsistent spacing
- Don't forget mobile optimization
- Don't bypass security measures

## 2030b/resources/views/components/auth-session-status.blade.php

**Purpose**: Display authentication session status messages including success, error, and informational alerts.

**Dependencies**:
- @if directive for conditional display
- Alert styling components
- Session flash data

**Sections/Slots**:
- Default slot for status message

**Variables Used**:
- $status - Session status message
- $type - Message type (success, error, info)
- $class - Additional styling classes

**Do**:
- Display clear, actionable messages
- Use appropriate color coding
- Include proper ARIA labels
- Auto-dismiss when appropriate
- Handle multiple message types

**Don't**:
- Don't show confusing messages
- Don't ignore accessibility
- Don't persist messages too long
- Don't use inconsistent styling
- Don't expose sensitive information

## 2030b/resources/views/components/button.blade.php

**Purpose**: Reusable button component with consistent styling, sizes, and states across the application.

**Dependencies**:
- Button styling utilities
- Icon libraries (optional)
- Loading state components

**Sections/Slots**:
- Default slot for button content
- $icon slot for button icons

**Variables Used**:
- $type - Button type (primary, secondary, danger)
- $size - Button size (sm, md, lg)
- $disabled - Disabled state
- $loading - Loading state
- $href - Link destination (for link buttons)

**Do**:
- Maintain consistent button hierarchy
- Include proper ARIA attributes
- Handle loading and disabled states
- Use semantic HTML
- Implement proper focus states

**Don't**:
- Don't create too many button variants
- Don't ignore accessibility standards
- Don't use buttons for navigation
- Don't forget keyboard navigation
- Don't use inconsistent sizing

## 2030b/resources/views/components/dropdown.blade.php

**Purpose**: Interactive dropdown menu component with keyboard navigation and accessibility features.

**Dependencies**:
- Alpine.js for interactivity
- Dropdown positioning utilities
- @click and @clickaway directives

**Sections/Slots**:
- $trigger slot for dropdown trigger
- Default slot for dropdown content

**Variables Used**:
- $align - Dropdown alignment (left, right, center)
- $width - Dropdown width
- $contentClasses - Content styling classes

**Do**:
- Implement proper keyboard navigation
- Include ARIA attributes
- Handle click-outside behavior
- Use proper z-index stacking
- Provide clear visual feedback

**Don't**:
- Don't ignore keyboard accessibility
- Don't create overly complex dropdowns
- Don't forget mobile considerations
- Don't use inadequate contrast
- Don't bypass focus management

## 2030b/resources/views/components/input.blade.php

**Purpose**: Standardized input field component with validation states, labels, and error handling.

**Dependencies**:
- Form validation utilities
- Input styling components
- Error display components

**Sections/Slots**:
- Default slot for input field

**Variables Used**:
- $name - Input field name
- $type - Input type (text, email, password, etc.)
- $value - Default value
- $placeholder - Placeholder text
- $required - Required field indicator
- $disabled - Disabled state
- $class - Additional CSS classes

**Do**:
- Include proper validation attributes
- Use semantic input types
- Provide clear labels
- Handle error states visually
- Include autocomplete attributes

**Don't**:
- Don't ignore validation requirements
- Don't use generic input types
- Don't forget accessibility labels
- Don't bypass HTML5 validation
- Don't use confusing placeholders

## 2030b/resources/views/components/label.blade.php

**Purpose**: Accessible form label component with consistent styling and proper association with form controls.

**Dependencies**:
- Label styling utilities
- Accessibility attributes
- Required field indicators

**Sections/Slots**:
- Default slot for label content

**Variables Used**:
- $for - Associated form control ID
- $required - Required field indicator
- $class - Additional styling classes
- $value - Label text content

**Do**:
- Always associate with form controls
- Include required field indicators
- Use clear, descriptive text
- Maintain consistent styling
- Include proper ARIA attributes

**Don't**:
- Don't create orphaned labels
- Don't use vague label text
- Don't ignore required indicators
- Don't forget accessibility
- Don't use inconsistent styling

## 2030b/resources/views/components/nav-link.blade.php

**Purpose**: Navigation link component with active state detection and consistent styling for main navigation.

**Dependencies**:
- @if directive for active state
- Route helper functions
- Navigation styling utilities

**Sections/Slots**:
- Default slot for link content

**Variables Used**:
- $href - Link destination
- $active - Active state boolean
- $class - Additional CSS classes
- $icon - Optional icon component

**Do**:
- Highlight active navigation items
- Use semantic navigation markup
- Include proper ARIA attributes
- Handle hover and focus states
- Maintain consistent styling

**Don't**:
- Don't ignore active state indication
- Don't use generic link styling
- Don't forget keyboard navigation
- Don't bypass accessibility
- Don't create confusing navigation

## 2030b/resources/views/components/responsive-nav-link.blade.php

**Purpose**: Mobile-responsive navigation link component optimized for touch interfaces and smaller screens.

**Dependencies**:
- Responsive design utilities
- Mobile navigation patterns
- Touch-friendly styling

**Sections/Slots**:
- Default slot for link content

**Variables Used**:
- $href - Link destination
- $active - Active state
- $class - Responsive classes

**Do**:
- Optimize for touch interaction
- Use adequate touch target sizes
- Implement proper responsive behavior
- Include mobile-specific styling
- Handle orientation changes

**Don't**:
- Don't use desktop-only patterns
- Don't ignore touch accessibility
- Don't create tiny touch targets
- Don't forget landscape mode
- Don't use hover-dependent interactions

## 2030b/resources/views/components/modal.blade.php

**Purpose**: Reusable modal dialog component with backdrop, keyboard navigation, and accessibility features.

**Dependencies**:
- Alpine.js for modal behavior
- @keydown directive for keyboard handling
- Focus management utilities
- Modal backdrop styling

**Sections/Slots**:
- $title slot for modal header
- Default slot for modal content
- $footer slot for modal actions

**Variables Used**:
- $show - Modal visibility state
- $maxWidth - Modal maximum width
- $closeable - Whether modal can be closed
- $id - Unique modal identifier

**Do**:
- Implement proper focus management
- Include keyboard navigation support
- Use ARIA attributes correctly
- Handle backdrop clicks appropriately
- Prevent body scroll when open

**Don't**:
- Don't ignore keyboard accessibility
- Don't create nested modals
- Don't forget focus trapping
- Don't use inadequate contrast
- Don't bypass escape key handling

## 2030b/resources/views/components/alert.blade.php

**Purpose**: Alert message component for displaying success, error, warning, and informational messages.

**Dependencies**:
- Alert styling utilities
- Icon components (optional)
- Dismissible behavior (optional)

**Sections/Slots**:
- Default slot for alert content
- $icon slot for alert icons

**Variables Used**:
- $type - Alert type (success, error, warning, info)
- $dismissible - Whether alert can be dismissed
- $class - Additional styling classes

**Do**:
- Use appropriate colors for message types
- Include proper ARIA live regions
- Provide clear, actionable messages
- Include dismiss functionality when appropriate
- Use semantic HTML structure

**Don't**:
- Don't use confusing color schemes
- Don't ignore accessibility announcements
- Don't create overly verbose messages
- Don't forget mobile optimization
- Don't bypass proper semantics

## 2030b/resources/views/components/card.blade.php

**Purpose**: Flexible card container component with header, body, and footer sections for content organization.

**Dependencies**:
- Card styling utilities
- Responsive design classes
- Shadow and border utilities

**Sections/Slots**:
- $header slot for card header
- Default slot for card body
- $footer slot for card footer

**Variables Used**:
- $title - Card title
- $class - Additional CSS classes
- $shadow - Shadow intensity
- $padding - Internal padding

**Do**:
- Maintain consistent card spacing
- Use proper semantic structure
- Include responsive behavior
- Handle empty states gracefully
- Provide clear visual hierarchy

**Don't**:
- Don't overcomplicate card structure
- Don't ignore responsive design
- Don't use inconsistent spacing
- Don't forget accessibility
- Don't create overly complex layouts

## 2030b/resources/views/components/badge.blade.php

**Purpose**: Small badge component for displaying status, counts, or categorical information.

**Dependencies**:
- Badge styling utilities
- Color scheme variables
- Typography utilities

**Sections/Slots**:
- Default slot for badge content

**Variables Used**:
- $type - Badge type (primary, secondary, success, danger, warning, info)
- $size - Badge size (sm, md, lg)
- $rounded - Rounded corners
- $class - Additional classes

**Do**:
- Use consistent color coding
- Keep text short and clear
- Ensure adequate contrast
- Use appropriate sizing
- Include proper semantics

**Don't**:
- Don't use too many badge variants
- Don't create illegible text
- Don't ignore accessibility
- Don't overcomplicate designs
- Don't use inappropriate colors

## 2030b/resources/views/components/form/select.blade.php

**Purpose**: Styled select dropdown component with validation support and consistent form integration.

**Dependencies**:
- Select styling utilities
- Form validation components
- @error directive for validation

**Sections/Slots**:
- Default slot for select options

**Variables Used**:
- $name - Select field name
- $options - Available options array
- $selected - Selected value
- $placeholder - Placeholder option
- $required - Required field indicator
- $multiple - Multiple selection support

**Do**:
- Include proper validation attributes
- Provide clear option labels
- Handle empty states
- Use semantic HTML
- Include accessibility features

**Don't**:
- Don't create confusing option groups
- Don't ignore validation requirements
- Don't use generic placeholders
- Don't forget keyboard navigation
- Don't bypass proper labeling

## 2030b/resources/views/components/form/checkbox.blade.php

**Purpose**: Accessible checkbox component with custom styling and form integration support.

**Dependencies**:
- Checkbox styling utilities
- Form validation support
- Custom checkbox designs

**Sections/Slots**:
- Default slot for checkbox label

**Variables Used**:
- $name - Checkbox field name
- $value - Checkbox value
- $checked - Checked state
- $required - Required field
- $disabled - Disabled state
- $class - Additional classes

**Do**:
- Provide clear, clickable labels
- Use proper form associations
- Include validation support
- Maintain consistent styling
- Handle disabled states

**Don't**:
- Don't create unlabeled checkboxes
- Don't ignore accessibility
- Don't use confusing label text
- Don't forget validation
- Don't use inconsistent styling

## 2030b/resources/views/components/form/radio.blade.php

**Purpose**: Radio button component with proper grouping and form validation integration.

**Dependencies**:
- Radio button styling
- Form group utilities
- Validation support

**Sections/Slots**:
- Default slot for radio label

**Variables Used**:
- $name - Radio group name
- $value - Radio button value
- $checked - Selected state
- $required - Required field
- $disabled - Disabled state

**Do**:
- Group related radio buttons
- Provide clear option labels
- Include proper form associations
- Handle validation states
- Use consistent styling

**Don't**:
- Don't create single radio buttons
- Don't ignore grouping requirements
- Don't use confusing labels
- Don't forget accessibility
- Don't bypass validation

## 2030b/resources/views/components/form/textarea.blade.php

**Purpose**: Multi-line text input component with resizing, validation, and character count features.

**Dependencies**:
- Textarea styling utilities
- Form validation support
- Character counter (optional)

**Sections/Slots**:
- Default slot for textarea content

**Variables Used**:
- $name - Textarea field name
- $rows - Number of visible rows
- $placeholder - Placeholder text
- $required - Required field
- $maxlength - Maximum character limit
- $disabled - Disabled state

**Do**:
- Set appropriate default size
- Include character limits
- Provide clear placeholders
- Handle validation states
- Allow reasonable resizing

**Don't**:
- Don't make textareas too small
- Don't ignore character limits
- Don't use confusing placeholders
- Don't forget validation
- Don't prevent necessary resizing

## 2030b/resources/views/components/layout/sidebar.blade.php

**Purpose**: Main application sidebar with navigation menu, user info, and collapsible sections.

**Dependencies**:
- @auth directive for user checks
- @can directive for permissions
- @include for navigation components
- Alpine.js for collapse behavior
- Sidebar styling utilities

**Sections/Slots**:
- $header slot for sidebar header
- Default slot for navigation content
- $footer slot for sidebar footer

**Variables Used**:
- $user - Current authenticated user
- $menuItems - Navigation menu items
- $collapsed - Sidebar collapse state
- $permissions - User permissions

**Do**:
- Implement responsive collapse behavior
- Include proper navigation hierarchy
- Show relevant user information
- Handle permission-based display
- Provide clear visual feedback

**Don't**:
- Don't show unauthorized menu items
- Don't ignore mobile optimization
- Don't create confusing navigation
- Don't bypass permission checks
- Don't use inadequate spacing

## 2030b/resources/views/components/layout/header.blade.php

**Purpose**: Main application header with branding, user menu, notifications, and global navigation.

**Dependencies**:
- @auth directive for authentication
- @include for logo and dropdown components
- Navigation utilities
- User menu components

**Sections/Slots**:
- $logo slot for brand logo
- $navigation slot for main nav
- $userMenu slot for user menu

**Variables Used**:
- $user - Current user data
- $notifications - User notifications
- $unreadCount - Unread notification count
- $breadcrumbs - Navigation breadcrumbs

**Do**:
- Include consistent branding
- Show relevant user information
- Display notification indicators
- Implement responsive behavior
- Provide clear navigation

**Don't**:
- Don't clutter the header space
- Don't ignore mobile optimization
- Don't show irrelevant information
- Don't forget accessibility
- Don't use inconsistent branding

## 2030b/resources/views/components/layout/footer.blade.php

**Purpose**: Application footer with links, copyright, and additional navigation options.

**Dependencies**:
- Footer styling utilities
- Link components
- Social media icons (optional)

**Sections/Slots**:
- $links slot for footer links
- $social slot for social links
- Default slot for footer content

**Variables Used**:
- $footerLinks - Footer navigation links
- $socialLinks - Social media links
- $copyrightYear - Current year
- $companyName - Company name

**Do**:
- Include essential links
- Display copyright information
- Keep design simple and clean
- Ensure mobile responsiveness
- Include accessibility features

**Don't**:
- Don't overcrowd with links
- Don't ignore mobile layout
- Don't use outdated information
- Don't forget accessibility
- Don't create confusing navigation

## 2030b/resources/views/components/ui/loading-spinner.blade.php

**Purpose**: Loading indicator component for async operations and page transitions.

**Dependencies**:
- Spinner animation CSS
- @if directive for conditional display
- Accessibility attributes

**Sections/Slots**:
- Default slot for custom loading content

**Variables Used**:
- $size - Spinner size (sm, md, lg)
- $color - Spinner color
- $show - Visibility state
- $message - Loading message

**Do**:
- Include proper ARIA attributes
- Use appropriate sizing
- Provide loading context
- Include screen reader text
- Keep animations smooth

**Don't**:
- Don't create distracting animations
- Don't ignore accessibility
- Don't use inappropriate sizing
- Don't forget loading context
- Don't overuse loading indicators

## 2030b/resources/views/components/ui/progress-bar.blade.php

**Purpose**: Progress indication component for showing completion status of tasks or processes.

**Dependencies**:
- Progress bar styling
- Animation utilities
- ARIA progress attributes

**Sections/Slots**:
- Default slot for progress label

**Variables Used**:
- $value - Current progress value
- $max - Maximum progress value
- $color - Progress bar color
- $size - Progress bar size
- $label - Progress description

**Do**:
- Include proper ARIA attributes
- Show accurate progress values
- Use appropriate colors
- Provide clear labels
- Handle edge cases gracefully

**Don't**:
- Don't show inaccurate progress
- Don't ignore accessibility
- Don't use confusing colors
- Don't forget screen readers
- Don't create jarring animations

## 2030b/resources/views/components/ui/toast-notification.blade.php

**Purpose**: Non-blocking notification component for displaying temporary messages and alerts.

**Dependencies**:
- Alpine.js for auto-dismiss behavior
- Toast positioning utilities
- Animation classes
- ARIA live regions

**Sections/Slots**:
- Default slot for notification content
- $action slot for action buttons

**Variables Used**:
- $type - Notification type (success, error, warning, info)
- $message - Notification message
- $duration - Auto-dismiss duration
- $persistent - Prevents auto-dismiss
- $position - Toast position

**Do**:
- Use ARIA live regions
- Implement auto-dismiss timing
- Provide clear action buttons
- Stack multiple notifications properly
- Include proper contrast

**Don't**:
- Don't block user interaction
- Don't ignore accessibility
- Don't use confusing messages
- Don't dismiss too quickly
- Don't create notification spam

## 2030b/resources/views/components/ui/tooltip.blade.php

**Purpose**: Contextual tooltip component for providing additional information on hover or focus.

**Dependencies**:
- Alpine.js for tooltip behavior
- Positioning utilities
- @mouseenter and @mouseleave directives
- Tooltip styling

**Sections/Slots**:
- $trigger slot for tooltip trigger
- Default slot for tooltip content

**Variables Used**:
- $content - Tooltip text content
- $position - Tooltip position (top, bottom, left, right)
- $delay - Show/hide delay
- $maxWidth - Maximum tooltip width

**Do**:
- Include keyboard accessibility
- Use appropriate positioning
- Keep content concise
- Provide adequate contrast
- Handle mobile interactions

**Don't**:
- Don't rely solely on hover
- Don't create overly long tooltips
- Don't ignore keyboard users
- Don't use poor contrast
- Don't block important content

## 2030b/resources/views/components/charts/bar-chart.blade.php

**Purpose**: Bar chart component for displaying comparative data visualization with responsive design.

**Dependencies**:
- Chart.js library
- Canvas element
- Chart configuration utilities
- Responsive chart options

**Sections/Slots**:
- Default slot for chart container

**Variables Used**:
- $data - Chart data array
- $labels - Chart labels
- $options - Chart configuration options
- $height - Chart height
- $colors - Chart color scheme

**Do**:
- Provide accessible data tables
- Include proper chart legends
- Use responsive configurations
- Implement proper color schemes
- Include data export options

**Don't**:
- Don't ignore accessibility
- Don't use poor color choices
- Don't create unreadable charts
- Don't forget mobile optimization
- Don't overcomplicate visualizations

## 2030b/resources/views/components/charts/line-chart.blade.php

**Purpose**: Line chart component for displaying trends and time-series data with interactive features.

**Dependencies**:
- Chart.js library
- Time series utilities
- Interactive chart features
- Export functionality

**Sections/Slots**:
- Default slot for chart container

**Variables Used**:
- $data - Time series data
- $labels - Time period labels
- $datasets - Multiple data series
- $options - Chart options
- $responsive - Responsive settings

**Do**:
- Handle time-based data properly
- Include zoom and pan features
- Provide data point tooltips
- Use appropriate scales
- Include accessibility features

**Don't**:
- Don't ignore data validation
- Don't use confusing time scales
- Don't create cluttered charts
- Don't forget screen readers
- Don't overload with data points

## 2030b/resources/views/components/charts/pie-chart.blade.php

**Purpose**: Pie chart component for displaying proportional data with interactive legends and tooltips.

**Dependencies**:
- Chart.js library
- Pie chart configurations
- Interactive legend
- Color palette utilities

**Sections/Slots**:
- Default slot for chart container

**Variables Used**:
- $data - Pie chart data values
- $labels - Segment labels
- $colors - Segment colors
- $showLegend - Legend visibility
- $responsive - Responsive options

**Do**:
- Use distinct colors for segments
- Include interactive legends
- Provide percentage values
- Handle small segments appropriately
- Include accessibility descriptions

**Don't**:
- Don't use too many segments
- Don't use similar colors
- Don't ignore small values
- Don't forget accessibility
- Don't create misleading proportions

## 2030b/resources/views/components/course/card.blade.php

**Purpose**: Course display card component showing course information, progress, and enrollment options.

**Dependencies**:
- Course model data
- @auth directive for user checks
- @can directive for enrollment checks
- Progress bar component
- Rating component

**Sections/Slots**:
- $image slot for course thumbnail
- $content slot for course details
- $actions slot for enrollment buttons

**Variables Used**:
- $course - Course model instance
- $progress - User course progress
- $enrolled - Enrollment status
- $price - Course pricing
- $rating - Course rating

**Do**:
- Show accurate course information
- Display enrollment status clearly
- Include progress indicators
- Handle different course states
- Provide clear call-to-action

**Don't**:
- Don't show outdated information
- Don't ignore enrollment rules
- Don't display confusing pricing
- Don't bypass access controls
- Don't create misleading content

## 2030b/resources/views/components/course/grid.blade.php

**Purpose**: Responsive grid layout component for displaying multiple course cards with filtering and sorting.

**Dependencies**:
- Course card components
- Grid layout utilities
- Filtering functionality
- Pagination support

**Sections/Slots**:
- Default slot for course cards
- $filters slot for filter controls
- $pagination slot for pagination

**Variables Used**:
- $courses - Collection of courses
- $filters - Active filters
- $sortBy - Sort criteria
- $perPage - Items per page
- $currentPage - Current page number

**Do**:
- Implement responsive grid layout
- Include proper filtering options
- Handle empty states gracefully
- Provide sorting capabilities
- Include pagination controls

**Don't**:
- Don't ignore mobile layout
- Don't create confusing filters
- Don't show unauthorized courses
- Don't forget loading states
- Don't overcomplicate navigation

## 2030b/resources/views/components/course/progress.blade.php

**Purpose**: Course progress tracking component showing completion status and learning milestones.

**Dependencies**:
- Progress bar component
- Course progress calculations
- Milestone indicators
- Achievement badges

**Sections/Slots**:
- Default slot for progress details
- $milestones slot for milestone markers

**Variables Used**:
- $progress - Progress percentage
- $completedLessons - Completed lesson count
- $totalLessons - Total lesson count
- $milestones - Progress milestones
- $estimatedTime - Remaining time estimate

**Do**:
- Show accurate progress data
- Include milestone celebrations
- Provide time estimates
- Display achievement badges
- Update progress in real-time

**Don't**:
- Don't show inaccurate progress
- Don't ignore user achievements
- Don't provide false estimates
- Don't forget progress persistence
- Don't create confusing indicators

## 2030b/resources/views/components/course/rating.blade.php

**Purpose**: Course rating display and input component with star ratings and review integration.

**Dependencies**:
- Star rating utilities
- Review system integration
- @auth directive for rating input
- Rating calculation functions

**Sections/Slots**:
- Default slot for rating display
- $reviews slot for review summary

**Variables Used**:
- $rating - Average course rating
- $totalReviews - Total review count
- $userRating - Current user's rating
- $canRate - Rating permission
- $readonly - Read-only mode

**Do**:
- Display accurate rating averages
- Show review counts
- Allow authenticated rating input
- Handle rating updates gracefully
- Include review summaries

**Don't**:
- Don't show fake ratings
- Don't allow duplicate ratings
- Don't ignore rating validation
- Don't bypass authentication
- Don't create confusing interfaces

## 2030b/resources/views/components/course/player.blade.php

**Purpose**: Course content player component for videos, documents, and interactive content delivery.

**Dependencies**:
- Video player libraries
- Document viewers
- Progress tracking
- @can directive for content access
- Media storage integration

**Sections/Slots**:
- Default slot for player content
- $controls slot for player controls
- $transcript slot for content transcript

**Variables Used**:
- $content - Course content item
- $progress - Content progress
- $nextContent - Next content item
- $previousContent - Previous content
- $transcript - Content transcript

**Do**:
- Track content consumption
- Provide player controls
- Include accessibility features
- Handle different content types
- Implement progress saving

**Don't**:
- Don't ignore access controls
- Don't bypass progress tracking
- Don't forget accessibility
- Don't allow unauthorized access
- Don't create poor user experience
3. Authentication Views (8 files)

## 2030b/resources/views/auth/login.blade.php

**Purpose**: User login form with email/password authentication, "remember me" option, and social login integration.

**Dependencies**:
- @extends('layouts.guest')
- @include('components.auth-card')
- @include('components.auth-session-status')
- @csrf directive
- Laravel Breeze authentication
- Social authentication providers

**Sections/Slots**:
- @section('content') - Main login form

**Variables Used**:
- $errors - Validation errors
- $status - Session status messages
- $socialProviders - Available social login options
- $old - Previous form input

**Do**:
- Include CSRF protection
- Validate user credentials securely
- Provide clear error messages
- Include "forgot password" link
- Implement rate limiting

**Don't**:
- Don't expose sensitive error details
- Don't ignore brute force protection
- Don't bypass validation
- Don't store credentials insecurely
- Don't forget accessibility features

## 2030b/resources/views/auth/register.blade.php

**Purpose**: User registration form with field validation, terms acceptance, and email verification setup.

**Dependencies**:
- @extends('layouts.guest')
- @include('components.auth-card')
- Form validation components
- Email verification system
- Terms and conditions integration

**Sections/Slots**:
- @section('content') - Registration form

**Variables Used**:
- $errors - Form validation errors
- $countries - Available countries
- $termsUrl - Terms and conditions URL
- $privacyUrl - Privacy policy URL

**Do**:
- Implement strong password requirements
- Validate email addresses
- Require terms acceptance
- Include field-level validation
- Send verification emails

**Don't**:
- Don't allow weak passwords
- Don't skip email verification
- Don't ignore terms requirements
- Don't bypass validation rules
- Don't store plain text passwords

## 2030b/resources/views/auth/forgot-password.blade.php

**Purpose**: Password reset request form for users who have forgotten their login credentials.

**Dependencies**:
- @extends('layouts.guest')
- Password reset system
- Email notification system
- Rate limiting middleware

**Sections/Slots**:
- @section('content') - Password reset form

**Variables Used**:
- $errors - Validation errors
- $status - Request status message
- $email - Pre-filled email address

**Do**:
- Validate email addresses
- Implement rate limiting
- Send secure reset links
- Provide clear instructions
- Log reset attempts

**Don't**:
- Don't expose user existence
- Don't allow unlimited requests
- Don't send insecure links
- Don't bypass security measures
- Don't ignore audit logging

## 2030b/resources/views/auth/reset-password.blade.php

**Purpose**: Password reset form where users set new passwords using secure reset tokens.

**Dependencies**:
- @extends('layouts.guest')
- Password reset token validation
- Password strength requirements
- Secure form handling

**Sections/Slots**:
- @section('content') - Password reset form

**Variables Used**:
- $token - Password reset token
- $email - User email address
- $errors - Form validation errors

**Do**:
- Validate reset tokens securely
- Enforce password complexity
- Confirm password entries
- Invalidate used tokens
- Log password changes

**Don't**:
- Don't accept invalid tokens
- Don't allow weak passwords
- Don't reuse reset tokens
- Don't bypass validation
- Don't ignore security logging

## 2030b/resources/views/auth/verify-email.blade.php

**Purpose**: Email verification page prompting users to verify their email addresses after registration.

**Dependencies**:
- @extends('layouts.guest')
- Email verification system
- Resend verification functionality
- Session management

**Sections/Slots**:
- @section('content') - Verification prompt

**Variables Used**:
- $status - Verification status
- $user - Current user data
- $resendUrl - Resend verification URL

**Do**:
- Provide clear instructions
- Include resend functionality
- Handle verification status
- Implement rate limiting
- Show verification success

**Don't**:
- Don't allow access without verification
- Don't allow unlimited resends
- Don't ignore verification status
- Don't bypass email requirements
- Don't create confusing messaging

## 2030b/resources/views/auth/confirm-password.blade.php

**Purpose**: Password confirmation form for sensitive operations requiring additional authentication.

**Dependencies**:
- @extends('layouts.guest')
- Password confirmation middleware
- Session timeout handling
- Security validation

**Sections/Slots**:
- @section('content') - Password confirmation form

**Variables Used**:
- $errors - Validation errors
- $redirectUrl - Post-confirmation redirect
- $action - Action requiring confirmation

**Do**:
- Validate current password
- Implement session timeouts
- Provide clear context
- Handle confirmation errors
- Redirect appropriately

**Don't**:
- Don't bypass password checks
- Don't ignore session security
- Don't provide vague messaging
- Don't allow unlimited attempts
- Don't forget security logging

## 2030b/resources/views/auth/two-factor.blade.php

**Purpose**: Two-factor authentication setup and verification form with QR code generation and backup codes.

**Dependencies**:
- @extends('layouts.guest')
- Google2FA integration
- QR code generation
- Backup code management
- TOTP validation

**Sections/Slots**:
- @section('content') - 2FA setup/verification

**Variables Used**:
- $qrCode - QR code for authenticator setup
- $secretKey - 2FA secret key
- $backupCodes - Recovery backup codes
- $user - Current user instance

**Do**:
- Generate secure QR codes
- Provide backup recovery codes
- Validate TOTP tokens
- Include setup instructions
- Handle backup code usage

**Don't**:
- Don't expose secret keys
- Don't allow bypass mechanisms
- Don't ignore backup codes
- Don't skip token validation
- Don't forget rate limiting

## 2030b/resources/views/auth/social-auth.blade.php

**Purpose**: Social authentication interface with multiple provider options and account linking functionality.

**Dependencies**:
- @extends('layouts.guest')
- Laravel Socialite
- OAuth provider configurations
- Account linking system
- Provider-specific styling

**Sections/Slots**:
- @section('content') - Social login options

**Variables Used**:
- $providers - Available social providers
- $errors - Authentication errors
- $linkingMode - Account linking mode
- $existingUser - User for account linking

**Do**:
- Support multiple providers
- Handle OAuth errors gracefully
- Implement account linking
- Validate provider responses
- Maintain security standards

**Don't**:
- Don't store provider tokens insecurely
- Don't ignore OAuth security
- Don't allow unauthorized linking
- Don't bypass validation
- Don't forget error handling
4. Dashboard Views (5 files)

## 2030b/resources/views/dashboard/index.blade.php

**Purpose**: Main user dashboard displaying course progress, recent activity, achievements, and personalized recommendations.

**Dependencies**:
- @extends('layouts.app')
- @include('components.course.card')
- @include('components.ui.progress-bar')
- @include('components.charts.line-chart')
- Dashboard analytics services

**Sections/Slots**:
- @section('content') - Main dashboard content

**Variables Used**:
- $user - Current authenticated user
- $recentCourses - Recently accessed courses
- $progress - Overall learning progress
- $achievements - User achievements
- $recommendations - Recommended courses

**Do**:
- Display personalized content
- Show accurate progress data
- Include relevant recommendations
- Provide quick action shortcuts
- Cache dashboard data appropriately

**Don't**:
- Don't show irrelevant information
- Don't ignore user preferences
- Don't display stale data
- Don't overwhelm with content
- Don't bypass performance optimization

## 2030b/resources/views/dashboard/analytics.blade.php

**Purpose**: User analytics dashboard showing learning statistics, time tracking, and performance metrics.

**Dependencies**:
- @extends('layouts.app')
- @include('components.charts.bar-chart')
- @include('components.charts.line-chart')
- @include('components.charts.pie-chart')
- Analytics data processing

**Sections/Slots**:
- @section('content') - Analytics dashboard

**Variables Used**:
- $learningStats - Learning statistics
- $timeSpent - Time tracking data
- $progressTrends - Progress trend data
- $courseCompletion - Completion rates
- $performanceMetrics - Performance data

**Do**:
- Present data visually
- Include interactive charts
- Provide filtering options
- Show meaningful insights
- Export analytics data

**Don't**:
- Don't show confusing metrics
- Don't ignore data privacy
- Don't create overwhelming displays
- Don't use poor visualizations
- Don't forget mobile optimization

## 2030b/resources/views/dashboard/courses.blade.php

**Purpose**: Course management dashboard for enrolled courses, progress tracking, and course discovery.

**Dependencies**:
- @extends('layouts.app')
- @include('components.course.grid')
- @include('components.course.card')
- Course filtering and search
- Enrollment management

**Sections/Slots**:
- @section('content') - Course dashboard

**Variables Used**:
- $enrolledCourses - User's enrolled courses
- $availableCourses - Available courses
- $completedCourses - Completed courses
- $filters - Active course filters
- $searchQuery - Search parameters

**Do**:
- Organize courses logically
- Provide search and filtering
- Show progress indicators
- Include enrollment options
- Handle course states properly

**Don't**:
- Don't show unauthorized courses
- Don't ignore course prerequisites
- Don't display outdated information
- Don't create confusing navigation
- Don't bypass enrollment rules

## 2030b/resources/views/dashboard/progress.blade.php

**Purpose**: Detailed progress tracking dashboard showing learning paths, milestones, and achievement history.

**Dependencies**:
- @extends('layouts.app')
- @include('components.course.progress')
- @include('components.ui.progress-bar')
- Progress calculation services
- Achievement system

**Sections/Slots**:
- @section('content') - Progress dashboard

**Variables Used**:
- $overallProgress - Overall learning progress
- $courseProgress - Individual course progress
- $milestones - Achieved milestones
- $badges - Earned badges
- $learningPath - Current learning path

**Do**:
- Show accurate progress data
- Celebrate achievements
- Provide detailed breakdowns
- Include time estimates
- Update progress real-time

**Don't**:
- Don't show inaccurate data
- Don't ignore achievements
- Don't provide confusing metrics
- Don't forget progress persistence
- Don't create misleading indicators

## 2030b/resources/views/dashboard/affiliate.blade.php

**Purpose**: Affiliate dashboard showing referral statistics, commission tracking, and marketing tools.

**Dependencies**:
- @extends('layouts.app')
- @include('components.charts.bar-chart')
- Affiliate system integration
- Commission calculation
- Marketing tools

**Sections/Slots**:
- @section('content') - Affiliate dashboard

**Variables Used**:
- $referralStats - Referral statistics
- $commissions - Commission data
- $payouts - Payout history
- $marketingTools - Available marketing tools
- $affiliateLinks - Generated affiliate links

**Do**:
- Show accurate commission data
- Provide marketing resources
- Track referral performance
- Include payout information
- Generate affiliate links

**Don't**:
- Don't show incorrect commissions
- Don't ignore payout schedules
- Don't provide broken links
- Don't bypass affiliate rules
- Don't forget compliance requirements
5. Profile Management (8 files)

## 2030b/resources/views/profile/edit.blade.php

**Purpose**: Main profile editing page with tabbed interface for personal information, security, and preferences.

**Dependencies**:
- @extends('layouts.app')
- @include('profile.partials.update-profile-information-form')
- @include('profile.partials.update-password-form')
- @include('profile.partials.delete-user-form')
- Profile management services

**Sections/Slots**:
- @section('content') - Profile editing interface

**Variables Used**:
- $user - Current user instance
- $countries - Available countries
- $timezones - Available timezones
- $languages - Supported languages
- $status - Update status messages

**Do**:
- Organize settings logically
- Include form validation
- Provide clear save indicators
- Handle file uploads securely
- Implement change confirmations

**Don't**:
- Don't ignore validation rules
- Don't allow insecure uploads
- Don't bypass authentication
- Don't forget data sanitization
- Don't ignore privacy settings

## 2030b/resources/views/profile/security.blade.php

**Purpose**: Security settings page for password management, 2FA setup, and active session monitoring.

**Dependencies**:
- @extends('layouts.app')
- @include('profile.partials.two-factor-form')
- Security management services
- Session monitoring
- Device tracking

**Sections/Slots**:
- @section('content') - Security settings

**Variables Used**:
- $user - Current user
- $activeSessions - Active user sessions
- $devices - Registered devices
- $twoFactorEnabled - 2FA status
- $backupCodes - 2FA backup codes

**Do**:
- Display active sessions
- Allow session termination
- Show device information
- Include 2FA management
- Log security changes

**Don't**:
- Don't expose sensitive data
- Don't allow unauthorized changes
- Don't ignore session security
- Don't bypass 2FA requirements
- Don't forget audit logging

## 2030b/resources/views/profile/notifications.blade.php

**Purpose**: Notification preferences management for email, SMS, and in-app notifications.

**Dependencies**:
- @extends('layouts.app')
- Notification preference system
- Email template previews
- Notification channels

**Sections/Slots**:
- @section('content') - Notification settings

**Variables Used**:
- $user - Current user
- $notificationTypes - Available notification types
- $preferences - Current preferences
- $channels - Notification channels
- $templates - Email templates

**Do**:
- Provide granular controls
- Show template previews
- Include unsubscribe options
- Validate preferences
- Update settings immediately

**Don't**:
- Don't ignore user preferences
- Don't send unwanted notifications
- Don't bypass opt-out requests
- Don't forget compliance rules
- Don't create confusing options

## 2030b/resources/views/profile/partials/update-profile-information-form.blade.php

**Purpose**: Profile information update form partial for personal details, contact information, and preferences.

**Dependencies**:
- Form validation components
- Image upload handling
- Country/timezone data
- Profile update services

**Sections/Slots**:
- Partial form content

**Variables Used**:
- $user - User model instance
- $countries - Country options
- $timezones - Timezone options
- $errors - Validation errors

**Do**:
- Validate all input fields
- Handle file uploads securely
- Provide clear field labels
- Include helpful hints
- Show update confirmations

**Don't**:
- Don't skip validation
- Don't allow insecure uploads
- Don't ignore required fields
- Don't expose sensitive data
- Don't forget error handling

## 2030b/resources/views/profile/partials/update-password-form.blade.php

**Purpose**: Password change form partial with current password verification and strength requirements.

**Dependencies**:
- Password validation rules
- Current password verification
- Password strength indicators
- Security logging

**Sections/Slots**:
- Password update form

**Variables Used**:
- $errors - Validation errors
- $passwordRequirements - Password rules
- $strengthIndicator - Password strength

**Do**:
- Require current password
- Enforce strength requirements
- Confirm new password
- Show strength indicators
- Log password changes

**Don't**:
- Don't allow weak passwords
- Don't bypass current password check
- Don't ignore confirmation
- Don't forget security logging
- Don't show password hints

## 2030b/resources/views/profile/partials/delete-user-form.blade.php

**Purpose**: Account deletion form partial with confirmation process and data retention information.

**Dependencies**:
- Account deletion services
- Data export options
- Confirmation requirements
- Audit logging

**Sections/Slots**:
- Account deletion form

**Variables Used**:
- $user - Current user
- $confirmationRequired - Deletion confirmation
- $dataRetention - Data retention policy
- $exportOptions - Available data exports

**Do**:
- Require explicit confirmation
- Explain data retention
- Offer data export
- Log deletion requests
- Handle dependencies properly

**Don't**:
- Don't allow accidental deletion
- Don't ignore data dependencies
- Don't bypass confirmation
- Don't forget audit trails
- Don't violate data policies

## 2030b/resources/views/profile/partials/two-factor-form.blade.php

**Purpose**: Two-factor authentication setup and management form partial with QR codes and backup codes.

**Dependencies**:
- Google2FA integration
- QR code generation
- Backup code generation
- TOTP validation

**Sections/Slots**:
- 2FA management form

**Variables Used**:
- $user - Current user
- $qrCode - Setup QR code
- $secretKey - 2FA secret
- $backupCodes - Recovery codes
- $enabled - 2FA status

**Do**:
- Generate secure secrets
- Provide clear setup instructions
- Include backup codes
- Validate setup properly
- Handle disable requests

**Don't**:
- Don't expose secret keys
- Don't skip validation
- Don't ignore backup codes
- Don't allow easy bypass
- Don't forget rate limiting

## 2030b/resources/views/profile/partials/api-token-form.blade.php

**Purpose**: API token management form partial for creating, viewing, and revoking personal access tokens.

**Dependencies**:
- Laravel Sanctum
- Token generation
- Scope management
- Token validation

**Sections/Slots**:
- API token management

**Variables Used**:
- $user - Current user
- $tokens - Existing tokens
- $scopes - Available token scopes
- $newToken - Newly created token

**Do**:
- Show token creation date
- Include scope selection
- Allow token revocation
- Display token usage
- Handle token expiration

**Don't**:
- Don't show full tokens after creation
- Don't allow unlimited tokens
- Don't ignore scope restrictions
- Don't bypass validation
- Don't forget token cleanup
6. Course Management (8 files)

## 2030b/resources/views/courses/index.blade.php

**Purpose**: Course listing page with search, filtering, sorting, and pagination for course discovery.

**Dependencies**:
- @extends('layouts.app')
- @include('components.course.grid')
- @include('components.course.card')
- Search functionality
- Filter and sort components

**Sections/Slots**:
- @section('content') - Course listing

**Variables Used**:
- $courses - Paginated course collection
- $categories - Course categories
- $filters - Active filters
- $sortOptions - Sort options
- $searchQuery - Current search term

**Do**:
- Implement comprehensive search
- Provide useful filters
- Show course previews
- Include pagination
- Handle empty results

**Don't**:
- Don't show unauthorized courses
- Don't ignore user preferences
- Don't create slow queries
- Don't forget mobile optimization
- Don't bypass access controls

## 2030b/resources/views/courses/show.blade.php

**Purpose**: Course detail page showing comprehensive course information, curriculum, and enrollment options.

**Dependencies**:
- @extends('layouts.app')
- @include('components.course.rating')
- @include('components.course.progress')
- @can directive for enrollment checks
- Course content structure

**Sections/Slots**:
- @section('content') - Course details

**Variables Used**:
- $course - Course model instance
- $curriculum - Course curriculum
- $reviews - Course reviews
- $instructor - Course instructor
- $enrollmentStatus - User enrollment status

**Do**:
- Show comprehensive course info
- Display curriculum structure
- Include instructor details
- Show student reviews
- Provide clear enrollment options

**Don't**:
- Don't expose restricted content
- Don't show inaccurate information
- Don't ignore enrollment rules
- Don't bypass access controls
- Don't forget SEO optimization

## 2030b/resources/views/courses/create.blade.php

**Purpose**: Course creation form for instructors to create new courses with content upload and configuration.

**Dependencies**:
- @extends('layouts.app')
- @can directive for instructor permissions
- File upload components
- Course creation services
- Content management tools

**Sections/Slots**:
- @section('content') - Course creation form

**Variables Used**:
- $categories - Available categories
- $levels - Course difficulty levels
- $languages - Supported languages
- $pricingOptions - Pricing models
- $errors - Validation errors

**Do**:
- Validate all course data
- Handle file uploads securely
- Provide content management tools
- Include preview functionality
- Implement draft saving

**Don't**:
- Don't bypass instructor verification
- Don't allow insecure uploads
- Don't ignore content guidelines
- Don't skip validation
- Don't forget content moderation

## 2030b/resources/views/courses/edit.blade.php

**Purpose**: Course editing interface for instructors to modify existing courses and manage content.

**Dependencies**:
- @extends('layouts.app')
- @can directive for edit permissions
- Course editing services
- Content management components
- Version control

**Sections/Slots**:
- @section('content') - Course editing interface

**Variables Used**:
- $course - Course being edited
- $categories - Available categories
- $content - Course content
- $settings - Course settings
- $publishStatus - Publication status

**Do**:
- Check edit permissions
- Track content changes
- Provide content versioning
- Include preview mode
- Handle published course updates

**Don't**:
- Don't allow unauthorized edits
- Don't ignore version control
- Don't disrupt enrolled students
- Don't bypass approval processes
- Don't forget change logging

## 2030b/resources/views/courses/player.blade.php

**Purpose**: Course content player interface for video lessons, documents, and interactive content consumption.

**Dependencies**:
- @extends('layouts.app')
- @include('components.course.player')
- @can directive for content access
- Progress tracking services
- Content delivery network

**Sections/Slots**:
- @section('content') - Content player

**Variables Used**:
- $course - Current course
- $lesson - Current lesson
- $progress - User progress
- $nextLesson - Next lesson
- $transcript - Lesson transcript

**Do**:
- Track viewing progress
- Provide player controls
- Include transcript options
- Handle different content types
- Implement bookmark functionality

**Don't**:
- Don't allow unauthorized access
- Don't ignore progress tracking
- Don't bypass content protection
- Don't forget accessibility
- Don't create poor user experience

## 2030b/resources/views/episodes/show.blade.php

**Purpose**: Individual episode/lesson viewing page with content player and learning tools.

**Dependencies**:
- @extends('layouts.app')
- @include('components.course.player')
- Episode content delivery
- Progress tracking
- Note-taking tools

**Sections/Slots**:
- @section('content') - Episode content

**Variables Used**:
- $episode - Current episode
- $course - Parent course
- $userNotes - User notes
- $resources - Episode resources
- $nextEpisode - Next episode

**Do**:
- Provide rich content experience
- Include note-taking features
- Track completion status
- Show related resources
- Enable content bookmarking

**Don't**:
- Don't bypass access controls
- Don't ignore progress tracking
- Don't allow content piracy
- Don't forget mobile optimization
- Don't create poor navigation

## 2030b/resources/views/episodes/create.blade.php

**Purpose**: Episode creation form for adding new lessons and content to existing courses.

**Dependencies**:
- @extends('layouts.app')
- @can directive for content creation
- Media upload services
- Content validation
- Episode management tools

**Sections/Slots**:
- @section('content') - Episode creation form

**Variables Variables Used**:
- $course - Parent course
- $contentTypes - Available content types
- $uploadLimits - File upload limits
- $errors - Validation errors

**Do**:
- Validate content uploads
- Support multiple content types
- Provide content preview
- Include metadata fields
- Implement draft functionality

**Don't**:
- Don't allow unauthorized creation
- Don't bypass content validation
- Don't ignore file size limits
- Don't skip copyright checks
- Don't forget content moderation

## 2030b/resources/views/episodes/edit.blade.php

**Purpose**: Episode editing form interface allowing instructors and admins to modify episode content, settings, access levels, and metadata within a course.

**Dependencies**:
- **Layout**: @extends('layouts.app')
- **Blade Directives**: @csrf, @method('PUT'), @auth, @error, @if, @foreach, @old
- **Components**: &lt;x-input&gt;, &lt;x-label&gt;, &lt;x-button&gt;, &lt;x-alert&gt;, &lt;x-modal&gt;
- **Frontend Assets**: Vite compiled CSS/JS, TinyMCE editor, file upload handlers
- **Localization**: __('episodes.edit'), __('common.save'), __('common.cancel')

**Sections / Slots**:
- @section('title', __('episodes.edit_title'))
- @section('content')
- @section('scripts') for editor initialization

**Variables Used**:
- $episode: App\Models\Course\Episode instance
- $course: App\Models\Course\DocuCourse instance
- $categories: Collection of available categories
- $languages: Collection of supported languages
- $errors: ViewErrorBag for validation errors
- $access_levels: Array of episode access level options

**Do**:
- Use proper CSRF protection and method spoofing
- Implement client-side validation before submission
- Provide rich text editor for content editing
- Show clear success/error messages
- Validate file uploads and media attachments
- Maintain responsive design for mobile editing
- Use localization for all user-facing text
- Implement auto-save functionality

**Don't**:
- Don't expose sensitive episode metadata
- Don't allow unauthorized access to edit forms
- Don't skip server-side validation
- Don't hardcode access level options
- Don't ignore file size limits
- Don't bypass course ownership checks

## 2030b/resources/views/payments/checkout.blade.php

**Purpose**: Payment checkout interface for course purchases, handling multiple payment methods, currency selection, and invoice generation.

**Dependencies**:
- **Layout**: @extends('layouts.app')
- **Blade Directives**: @csrf, @auth, @if, @foreach, @switch, @json
- **Components**: &lt;x-card&gt;, &lt;x-button&gt;, &lt;x-input&gt;, &lt;x-form.select&gt;, &lt;x-alert&gt;
- **Frontend Assets**: Stripe.js, payment form handlers, currency converters
- **Localization**: __('payments.checkout'), __('payments.total'), __('payments.pay_now')

**Sections / Slots**:
- @section('title', __('payments.checkout_title'))
- @section('content')
- @section('scripts') for payment processing

**Variables Used**:
- $course: App\Models\Course\DocuCourse instance
- $total_amount: Calculated course price
- $currencies: Collection of supported currencies
- $payment_methods: Available payment gateways
- $user: Authenticated user instance
- $cart_items: Array of items being purchased
- $tax_rate: Applicable tax percentage

**Do**:
- Implement secure payment form validation
- Display itemized price breakdown
- Support multiple currencies and payment methods
- Show loading states during payment processing
- Provide clear error handling for failed payments
- Use HTTPS for all payment forms
- Cache exchange rates appropriately
- Implement payment retry mechanisms

**Don't**:
- Don't store credit card details client-side
- Don't process payments without proper validation
- Don't expose payment gateway credentials
- Don't skip PCI compliance requirements
- Don't allow payment without user authentication
- Don't ignore transaction security measures

## 2030b/resources/views/payments/success.blade.php

**Purpose**: Payment confirmation page displaying successful transaction details, course access information, and next steps for users.

**Dependencies**:
- **Layout**: @extends('layouts.app')
- **Blade Directives**: @if, @auth, @foreach
- **Components**: &lt;x-card&gt;, &lt;x-button&gt;, &lt;x-badge&gt;, &lt;x-alert&gt;
- **Frontend Assets**: Confetti animation, social sharing widgets
- **Localization**: __('payments.success'), __('payments.access_granted'), __('courses.start_learning')

**Sections / Slots**:
- @section('title', __('payments.success_title'))
- @section('content')
- @section('meta') for social sharing metadata

**Variables Used**:
- $transaction: App\Models\Payment\Transaction instance
- $course: App\Models\Course\DocuCourse purchased course
- $user: Authenticated user instance
- $invoice: App\Models\Payment\Invoice if applicable
- $next_steps: Array of recommended actions

**Do**:
- Display clear transaction confirmation
- Provide immediate course access links
- Show invoice download options
- Include social sharing capabilities
- Offer related course recommendations
- Send confirmation email automatically
- Track conversion events for analytics
- Provide customer support contact information

**Don't**:
- Don't expose sensitive payment details
- Don't cache sensitive transaction data
- Don't allow unauthorized access to success pages
- Don't skip analytics tracking
- Don't ignore email notification sending
- Don't bypass access permission updates

## 2030b/resources/views/payments/failed.blade.php

**Purpose**: Payment failure page providing error information, alternative payment options, and support resources for unsuccessful transactions.

**Dependencies**:
- **Layout**: @extends('layouts.app')
- **Blade Directives**: @if, @switch, @auth
- **Components**: &lt;x-alert&gt;, &lt;x-button&gt;, &lt;x-card&gt;
- **Frontend Assets**: Error tracking scripts, retry handlers
- **Localization**: __('payments.failed'), __('payments.try_again'), __('support.contact')

**Sections / Slots**:
- @section('title', __('payments.failed_title'))
- @section('content')
- @section('scripts') for retry functionality

**Variables Used**:
- $error_code: Payment gateway error identifier
- $error_message: User-friendly error description
- $transaction: Failed transaction instance (if exists)
- $alternative_methods: Array of other payment options
- $support_contact: Customer service information

**Do**:
- Provide clear error explanations
- Offer alternative payment methods
- Include retry payment functionality
- Show customer support contact options
- Log payment failures for analysis
- Maintain user-friendly error messages
- Provide troubleshooting suggestions
- Track failed payment analytics

**Don't**:
- Don't expose technical error details
- Don't blame users for payment failures
- Don't ignore failed payment logging
- Don't skip error analytics tracking
- Don't provide unclear error messages
- Don't omit customer support options

## 2030b/resources/views/payments/history.blade.php

**Purpose**: User payment history dashboard displaying transaction records, invoices, refunds, and downloadable receipts with filtering and search capabilities.

**Dependencies**:
- **Layout**: @extends('layouts.app')
- **Blade Directives**: @auth, @foreach, @if, @empty, @switch
- **Components**: &lt;x-card&gt;, &lt;x-badge&gt;, &lt;x-button&gt;, &lt;x-form.select&gt;, &lt;x-modal&gt;
- **Frontend Assets**: DataTables, date pickers, PDF generators
- **Localization**: __('payments.history'), __('payments.download'), __('payments.filter')

**Sections / Slots**:
- @section('title', __('payments.history_title'))
- @section('content')
- @section('scripts') for interactive features

**Variables Used**:
- $transactions: Paginated collection of user transactions
- $user: Authenticated user instance
- $filters: Applied search and filter criteria
- $total_spent: Calculated total spending amount
- $currencies: Available currency options for filtering

**Do**:
- Implement pagination for large transaction lists
- Provide filtering by date, status, and amount
- Include search functionality
- Show detailed transaction information
- Allow invoice and receipt downloads
- Display refund status and history
- Use responsive table design
- Cache transaction summaries

**Don't**:
- Don't expose other users' payment data
- Don't allow unauthorized access to payment history
- Don't skip data pagination for performance
- Don't ignore privacy considerations
- Don't omit transaction security measures
- Don't cache sensitive payment information

## 2030b/resources/views/payments/invoices/show.blade.php

**Purpose**: Individual invoice display page with detailed billing information, payment status, line items, and download options for accounting purposes.

**Dependencies**:
- **Layout**: @extends('layouts.app')
- **Blade Directives**: @if, @foreach, @auth
- **Components**: &lt;x-card&gt;, &lt;x-button&gt;, &lt;x-badge&gt;
- **Frontend Assets**: Print styles, PDF generation, QR code generator
- **Localization**: __('invoices.details'), __('invoices.download'), __('invoices.print')

**Sections / Slots**:
- @section('title', __('invoices.invoice_number', ['number' => $invoice->number]))
- @section('content')
- @section('print-styles') for printable version

**Variables Used**:
- $invoice: App\Models\Payment\Invoice instance
- $user: Invoice owner (authenticated user)
- $line_items: Invoice items collection
- $tax_details: Tax calculation breakdown
- $payment_info: Associated payment transaction

**Do**:
- Display professional invoice formatting
- Include all required billing information
- Provide PDF download functionality
- Show payment status clearly
- Include tax breakdown if applicable
- Maintain print-friendly styling
- Show QR codes for digital payments
- Include company/billing addresses

**Don't**:
- Don't allow access to unauthorized invoices
- Don't omit required tax information
- Don't ignore invoice numbering sequences
- Don't expose sensitive payment details
- Don't skip invoice validation checks
- Don't bypass accounting compliance requirements

## 2030b/resources/views/affiliate/dashboard.blade.php

**Purpose**: Affiliate program dashboard showing commission earnings, referral statistics, performance metrics, and payout information for affiliate users.

**Dependencies**:
- **Layout**: @extends('layouts.app')
- **Blade Directives**: @auth, @if, @foreach, @can('manage-affiliate')
- **Components**: &lt;x-card&gt;, &lt;x-charts.line-chart&gt;, &lt;x-badge&gt;, &lt;x-button&gt;
- **Frontend Assets**: Chart.js, dashboard widgets, copy-to-clipboard
- **Localization**: __('affiliate.dashboard'), __('affiliate.earnings'), __('affiliate.referrals')

**Sections / Slots**:
- @section('title', __('affiliate.dashboard_title'))
- @section('content')
- @section('scripts') for interactive charts

**Variables Used**:
- $affiliate: App\Models\Affiliate\Affiliate instance
- $earnings: Total and pending commission amounts
- $referrals: Collection of referred users
- $clicks: Affiliate link click statistics
- $conversions: Successful conversion data
- $payout_history: Previous payout records

**Do**:
- Display real-time earnings information
- Show referral link performance metrics
- Provide easy link sharing tools
- Include conversion rate analytics
- Display payout history and schedules
- Implement responsive dashboard design
- Cache performance metrics appropriately
- Validate affiliate permissions

**Don't**:
- Don't expose other affiliates' data
- Don't allow unauthorized affiliate access
- Don't skip commission calculation validation
- Don't ignore payout processing rules
- Don't cache sensitive financial data
- Don't bypass affiliate program terms

## 2030b/resources/views/affiliate/links.blade.php

**Purpose**: Affiliate link management interface for creating, customizing, and tracking referral links with analytics and sharing tools.

**Dependencies**:
- **Layout**: @extends('layouts.app')
- **Blade Directives**: @auth, @foreach, @if, @csrf
- **Components**: &lt;x-card&gt;, &lt;x-button&gt;, &lt;x-input&gt;, &lt;x-modal&gt;, &lt;x-alert&gt;
- **Frontend Assets**: Copy-to-clipboard, QR code generator, social sharing
- **Localization**: __('affiliate.links'), __('affiliate.create_link'), __('affiliate.copy')

**Sections / Slots**:
- @section('title', __('affiliate.links_title'))
- @section('content')
- @section('scripts') for link management

**Variables Used**:
- $affiliate_links: Collection of user's referral codes
- $available_courses: Courses available for promotion
- $link_analytics: Click and conversion statistics
- $user: Authenticated affiliate user

**Do**:
- Provide easy link generation tools
- Include click tracking and analytics
- Offer multiple sharing format options
- Show link performance metrics
- Allow custom link parameters
- Implement QR code generation
- Validate link ownership
- Cache link analytics data

**Don't**:
- Don't allow unauthorized link creation
- Don't expose other affiliates' links
- Don't skip link validation
- Don't ignore tracking parameters
- Don't bypass affiliate program rules
- Don't omit performance tracking

## 2030b/resources/views/affiliate/commissions.blade.php

**Purpose**: Commission tracking and history page displaying earned commissions, payment status, and detailed breakdown by referral source.

**Dependencies**:
- **Layout**: @extends('layouts.app')
- **Blade Directives**: @auth, @foreach, @if, @empty
- **Components**: &lt;x-card&gt;, &lt;x-badge&gt;, &lt;x-button&gt;, &lt;x-form.select&gt;
- **Frontend Assets**: DataTables, export functions, filtering tools
- **Localization**: __('affiliate.commissions'), __('affiliate.pending'), __('affiliate.paid')

**Sections / Slots**:
- @section('title', __('affiliate.commissions_title'))
- @section('content')
- @section('scripts') for filtering and export

**Variables Used**:
- $commissions: Paginated collection of commission records
- $total_earned: Lifetime commission earnings
- $pending_amount: Unpaid commission balance
- $payment_schedule: Next payout information
- $filters: Applied filtering criteria

**Do**:
- Display detailed commission breakdowns
- Show payment status and schedules
- Provide filtering and search capabilities
- Include export functionality
- Display referral source information
- Show conversion details
- Implement pagination for performance
- Validate commission calculations

**Don't**:
- Don't expose incorrect commission data
- Don't allow unauthorized commission access
- Don't skip commission validation
- Don't ignore payment processing rules
- Don't omit audit trails
- Don't bypass payout verification

## 2030b/resources/views/affiliate/analytics.blade.php

**Purpose**: Comprehensive affiliate analytics dashboard with charts, conversion funnels, geographic data, and performance trends for affiliate marketing optimization.

**Dependencies**:
- **Layout**: @extends('layouts.app')
- **Blade Directives**: @auth, @foreach, @if
- **Components**: &lt;x-card&gt;, &lt;x-charts.line-chart&gt;, &lt;x-charts.pie-chart&gt;, &lt;x-charts.bar-chart&gt;
- **Frontend Assets**: Chart.js, analytics widgets, date range pickers
- **Localization**: __('affiliate.analytics'), __('analytics.conversions'), __('analytics.traffic')

**Sections / Slots**:
- @section('title', __('affiliate.analytics_title'))
- @section('content')
- @section('scripts') for chart initialization

**Variables Used**:
- $analytics_data: Comprehensive affiliate performance metrics
- $conversion_funnel: Step-by-step conversion analysis
- $traffic_sources: Referral traffic breakdown
- $geographic_data: Location-based performance data
- $date_range: Selected analysis period

**Do**:
- Provide comprehensive performance metrics
- Include interactive charts and visualizations
- Show conversion funnel analysis
- Display geographic performance data
- Implement date range filtering
- Cache analytics data appropriately
- Use responsive chart designs
- Validate data accuracy

**Don't**:
- Don't expose inaccurate analytics data
- Don't allow unauthorized analytics access
- Don't ignore performance optimization
- Don't skip data validation
- Don't omit important metrics
- Don't bypass privacy considerations

## 2030b/resources/views/admin/dashboard.blade.php

**Purpose**: Administrative dashboard providing system overview, user metrics, course statistics, revenue analytics, and quick management actions for platform administrators.

**Dependencies**:
- **Layout**: @extends('layouts.admin')
- **Blade Directives**: @auth, @can('admin-access'), @foreach, @if
- **Components**: &lt;x-card&gt;, &lt;x-charts.line-chart&gt;, &lt;x-badge&gt;, &lt;x-alert&gt;
- **Frontend Assets**: Admin dashboard styles, Chart.js, real-time updates
- **Localization**: __('admin.dashboard'), __('admin.users'), __('admin.revenue')

**Sections / Slots**:
- @section('title', __('admin.dashboard_title'))
- @section('content')
- @section('scripts') for dashboard widgets

**Variables Used**:
- $user_statistics: User registration and activity metrics
- $course_statistics: Course creation and enrollment data
- $revenue_data: Financial performance metrics
- $system_alerts: Important system notifications
- $recent_activity: Latest platform activity

**Do**:
- Display key performance indicators (KPIs)
- Provide real-time system monitoring
- Show user growth and engagement metrics
- Include revenue and financial summaries
- Display system health indicators
- Implement responsive admin design
- Cache dashboard metrics appropriately
- Validate admin permissions

**Don't**:
- Don't allow unauthorized admin access
- Don't expose sensitive system data
- Don't ignore security considerations
- Don't skip performance monitoring
- Don't omit error tracking
- Don't bypass permission validation

## 2030b/resources/views/admin/users/index.blade.php

**Purpose**: User management interface for administrators to view, search, filter, and manage all platform users with bulk actions and detailed user information.

**Dependencies**:
- **Layout**: @extends('layouts.admin')
- **Blade Directives**: @auth, @can('manage-users'), @foreach, @if, @csrf
- **Components**: &lt;x-card&gt;, &lt;x-button&gt;, &lt;x-form.select&gt;, &lt;x-modal&gt;, &lt;x-badge&gt;
- **Frontend Assets**: DataTables, user management scripts, bulk action handlers
- **Localization**: __('admin.users'), __('users.search'), __('users.actions')

**Sections / Slots**:
- @section('title', __('admin.users_title'))
- @section('content')
- @section('scripts') for user management

**Variables Used**:
- $users: Paginated collection of platform users
- $filters: Applied search and filter criteria
- $user_levels: Available user level options
- $roles: System roles for assignment
- $statistics: User management statistics

**Do**:
- Implement advanced search and filtering
- Provide bulk user management actions
- Display user status and role information
- Include user activity summaries
- Show registration and last login dates
- Implement secure user actions
- Use responsive table design
- Validate administrative permissions

**Don't**:
- Don't expose user passwords or sensitive data
- Don't allow unauthorized user management
- Don't skip user action validation
- Don't ignore privacy regulations
- Don't omit audit logging
- Don't bypass permission checks

## 2030b/resources/views/admin/users/show.blade.php

**Purpose**: Detailed user profile view for administrators showing complete user information, activity history, course progress, and management actions.

**Dependencies**:
- **Layout**: @extends('layouts.admin')
- **Blade Directives**: @auth, @can('view-user'), @foreach, @if
- **Components**: &lt;x-card&gt;, &lt;x-badge&gt;, &lt;x-button&gt;, &lt;x-modal&gt;, &lt;x-alert&gt;
- **Frontend Assets**: User profile widgets, activity timelines, action handlers
- **Localization**: __('admin.user_details'), __('users.activity'), __('users.courses')

**Sections / Slots**:
- @section('title', __('admin.user_detail_title', ['name' => $user->name]))
- @section('content')
- @section('scripts') for profile management

**Variables Used**:
- $user: App\Models\User instance being viewed
- $user_profile: Extended user profile information
- $activity_log: User activity history
- $enrolled_courses: User's course enrollments
- $payment_history: User's transaction history

**Do**:
- Display comprehensive user information
- Show user activity and engagement history
- Include course enrollment and progress data
- Provide administrative action buttons
- Display payment and billing information
- Show user communication history
- Implement secure profile viewing
- Validate view permissions

**Don't**:
- Don't expose extremely sensitive user data
- Don't allow unauthorized profile access
- Don't skip permission validation
- Don't ignore privacy considerations
- Don't omit important user metrics
- Don't bypass security measures

## 2030b/resources/views/admin/users/edit.blade.php

**Purpose**: Administrative user editing interface allowing admins to modify user accounts, roles, permissions, and profile information with proper validation and audit trails.

**Dependencies**:
- **Layout**: @extends('layouts.admin')
- **Blade Directives**: @auth, @can('edit-user'), @csrf, @method('PUT'), @error
- **Components**: &lt;x-input&gt;, &lt;x-label&gt;, &lt;x-button&gt;, &lt;x-form.select&gt;, &lt;x-alert&gt;
- **Frontend Assets**: Form validation, role management, password generators
- **Localization**: __('admin.edit_user'), __('users.roles'), __('common.save')

**Sections / Slots**:
- @section('title', __('admin.edit_user_title', ['name' => $user->name]))
- @section('content')
- @section('scripts') for form management

**Variables Used**:
- $user: App\Models\User instance being edited
- $roles: Available system roles
- $permissions: Available permissions
- $user_levels: User progression levels
- $errors: Form validation errors

**Do**:
- Implement comprehensive form validation
- Provide role and permission management
- Show current user status and settings
- Include password reset capabilities
- Log all administrative changes
- Use secure form handling
- Validate edit permissions
- Maintain audit trails

**Don't**:
- Don't allow unauthorized user editing
- Don't skip validation for user changes
- Don't ignore security considerations
- Don't omit change logging
- Don't bypass permission checks
- Don't expose sensitive operations

## 2030b/resources/views/admin/courses/index.blade.php

**Purpose**: Course management interface for administrators to oversee all platform courses, with filtering, search, bulk actions, and course status management capabilities.

**Dependencies**:
- **Layout**: @extends('layouts.admin')
- **Blade Directives**: @auth, @can('manage-courses'), @foreach, @if, @csrf
- **Components**: &lt;x-card&gt;, &lt;x-button&gt;, &lt;x-badge&gt;, &lt;x-form.select&gt;, &lt;x-modal&gt;
- **Frontend Assets**: DataTables, course management scripts, bulk action handlers
- **Localization**: __('admin.courses'), __('courses.status'), __('courses.actions')

**Sections / Slots**:
- @section('title', __('admin.courses_title'))
- @section('content')
- @section('scripts') for course management

**Variables Used**:
- $courses: Paginated collection of platform courses
- $categories: Course categories for filtering
- $instructors: Available course instructors
- $filters: Applied search and filter criteria
- $statistics: Course management statistics

**Do**:
- Implement advanced course filtering and search
- Provide bulk course management actions
- Display course status and enrollment data
- Show instructor and category information
- Include course performance metrics
- Implement secure course actions
- Use responsive data presentation
- Validate administrative permissions

**Don't**:
- Don't allow unauthorized course management
- Don't skip course action validation
- Don't ignore content moderation
- Don't omit instructor permissions
- Don't bypass quality control
- Don't ignore course guidelines

## 2030b/resources/views/admin/courses/show.blade.php

**Purpose**: Detailed course view for administrators displaying comprehensive course information, enrollment statistics, revenue data, and management actions for individual courses.

**Dependencies**:
- **Layout**: @extends('layouts.admin')
- **Blade Directives**: @auth, @can('view-course'), @foreach, @if
- **Components**: &lt;x-card&gt;, &lt;x-badge&gt;, &lt;x-charts.line-chart&gt;, &lt;x-button&gt;, &lt;x-modal&gt;
- **Frontend Assets**: Course analytics widgets, enrollment charts, action handlers
- **Localization**: __('admin.course_details'), __('courses.analytics'), __('courses.manage')

**Sections / Slots**:
- @section('title', __('admin.course_detail_title', ['title' => $course->title]))
- @section('content')
- @section('scripts') for course analytics

**Variables Used**:
- $course: App\Models\Course\DocuCourse instance
- $enrollment_data: Course enrollment statistics
- $revenue_data: Course revenue analytics
- $episodes: Course episodes collection
- $reviews: Course reviews and ratings

**Do**:
- Display comprehensive course information
- Show enrollment trends and statistics
- Include revenue and financial data
- Display course content structure
- Show student feedback and reviews
- Provide course management actions
- Implement performance analytics
- Validate view permissions

**Don't**:
- Don't expose sensitive course data inappropriately
- Don't allow unauthorized course access
- Don't skip content validation
- Don't ignore instructor rights
- Don't omit important course metrics
- Don't bypass security measures

## 2030b/resources/views/admin/courses/edit.blade.php

**Purpose**: Administrative course editing interface allowing admins to modify course content, settings, pricing, and publishing status with comprehensive validation and approval workflows.

**Dependencies**:
- **Layout**: @extends('layouts.admin')
- **Blade Directives**: @auth, @can('edit-course'), @csrf, @method('PUT'), @error
- **Components**: &lt;x-input&gt;, &lt;x-label&gt;, &lt;x-button&gt;, &lt;x-form.textarea&gt;, &lt;x-form.select&gt;
- **Frontend Assets**: Rich text editor, file upload, course builder tools
- **Localization**: __('admin.edit_course'), __('courses.content'), __('courses.settings')

**Sections / Slots**:
- @section('title', __('admin.edit_course_title', ['title' => $course->title]))
- @section('content')
- @section('scripts') for course editing

**Variables Used**:
- $course: App\Models\Course\DocuCourse instance being edited
- $categories: Available course categories
- $languages: Supported course languages
- $currencies: Available pricing currencies
- $errors: Form validation errors

**Do**:
- Implement comprehensive course editing capabilities
- Provide rich content editing tools
- Include media and file management
- Show course preview functionality
- Validate course content and structure
- Log all administrative changes
- Use secure form handling
- Maintain approval workflows

**Don't**:
- Don't allow unauthorized course editing
- Don't skip content validation
- Don't ignore instructor permissions
- Don't omit change tracking
- Don't bypass quality standards
- Don't ignore course guidelines

## 2030b/resources/views/admin/payments/index.blade.php

**Purpose**: Administrative payment management dashboard displaying all platform transactions, payment analytics, refund management, and financial reporting tools.

**Dependencies**:
- **Layout**: @extends('layouts.admin')
- **Blade Directives**: @auth, @can('manage-payments'), @foreach, @if, @switch
- **Components**: &lt;x-card&gt;, &lt;x-badge&gt;, &lt;x-button&gt;, &lt;x-charts.bar-chart&gt;, &lt;x-modal&gt;
- **Frontend Assets**: Payment analytics, transaction filtering, export tools
- **Localization**: __('admin.payments'), __('payments.transactions'), __('payments.refunds')

**Sections / Slots**:
- @section('title', __('admin.payments_title'))
- @section('content')
- @section('scripts') for payment management

**Variables Used**:
- $transactions: Paginated payment transactions
- $payment_statistics: Financial analytics data
- $currencies: Supported payment currencies
- $filters: Applied filtering criteria
- $refund_requests: Pending refund requests

**Do**:
- Display comprehensive payment analytics
- Provide transaction filtering and search
- Include refund management capabilities
- Show payment gateway performance
- Implement financial reporting tools
- Validate payment management permissions
- Use secure payment data handling
- Cache financial metrics appropriately

**Don't**:
- Don't expose sensitive payment credentials
- Don't allow unauthorized payment access
- Don't skip financial data validation
- Don't ignore compliance requirements
- Don't omit audit trails
- Don't bypass security measures

## 2030b/resources/views/admin/system/config.blade.php

**Purpose**: System configuration interface for administrators to manage platform settings, feature toggles, API configurations, and system parameters with proper validation and backup capabilities.

**Dependencies**:
- **Layout**: @extends('layouts.admin')
- **Blade Directives**: @auth, @can('manage-system'), @csrf, @foreach, @if
- **Components**: &lt;x-card&gt;, &lt;x-input&gt;, &lt;x-form.select&gt;, &lt;x-form.checkbox&gt;, &lt;x-button&gt;
- **Frontend Assets**: Configuration management, validation scripts, backup tools
- **Localization**: __('admin.system_config'), __('config.general'), __('config.features')

**Sections / Slots**:
- @section('title', __('admin.system_config_title'))
- @section('content')
- @section('scripts') for configuration management

**Variables Used**:
- $configurations: System configuration settings
- $feature_flags: Available feature toggles
- $api_settings: API configuration options
- $backup_info: System backup information
- $environment: Current environment settings

**Do**:
- Organize settings into logical groups
- Provide validation for configuration values
- Include setting descriptions and help text
- Implement configuration backup/restore
- Show current system status
- Validate system management permissions
- Use secure configuration handling
- Log configuration changes

**Don't**:
- Don't expose sensitive system credentials
- Don't allow unauthorized system access
- Don't skip configuration validation
- Don't ignore security implications
- Don't omit change tracking
- Don't bypass safety measures

## 2030b/resources/views/admin/system/analytics.blade.php

**Purpose**: System-wide analytics dashboard providing comprehensive platform metrics, user behavior analysis, performance monitoring, and business intelligence for administrators.

**Dependencies**:
- **Layout**: @extends('layouts.admin')
- **Blade Directives**: @auth, @can('view-analytics'), @foreach, @if
- **Components**: &lt;x-card&gt;, &lt;x-charts.line-chart&gt;, &lt;x-charts.pie-chart&gt;, &lt;x-charts.bar-chart&gt;
- **Frontend Assets**: Advanced charting, analytics widgets, export functionality
- **Localization**: __('admin.analytics'), __('analytics.users'), __('analytics.revenue')

**Sections / Slots**:
- @section('title', __('admin.analytics_title'))
- @section('content')
- @section('scripts') for analytics visualization

**Variables Used**:
- $user_analytics: User behavior and engagement metrics
- $course_analytics: Course performance data
- $revenue_analytics: Financial performance metrics
- $system_metrics: Platform performance indicators
- $date_ranges: Available analysis periods

**Do**:
- Provide comprehensive business intelligence
- Include interactive data visualizations
- Show key performance indicators (KPIs)
- Implement data export functionality
- Display real-time metrics where appropriate
- Use responsive chart designs
- Cache analytics data effectively
- Validate analytics access permissions

**Don't**:
- Don't expose user privacy data inappropriately
- Don't allow unauthorized analytics access
- Don't ignore data accuracy
- Don't skip performance optimization
- Don't omit important business metrics
- Don't bypass privacy regulations

## 2030b/resources/views/admin/system/backups.blade.php

**Purpose**: System backup management interface for administrators to monitor, create, restore, and manage platform backups with scheduling and storage options.

**Dependencies**:
- **Layout**: @extends('layouts.admin')
- **Blade Directives**: @auth, @can('manage-backups'), @foreach, @if, @csrf
- **Components**: &lt;x-card&gt;, &lt;x-button&gt;, &lt;x-badge&gt;, &lt;x-modal&gt;, &lt;x-alert&gt;
- **Frontend Assets**: Backup management, progress indicators, file download handlers
- **Localization**: __('admin.backups'), __('backups.create'), __('backups.restore')

**Sections / Slots**:
- @section('title', __('admin.backups_title'))
- @section('content')
- @section('scripts') for backup management

**Variables Used**:
- $backups: Available system backups
- $backup_config: Backup configuration settings
- $storage_info: Backup storage information
- $last_backup: Most recent backup information
- $schedule: Backup schedule settings

**Do**:
- Display backup status and health information
- Provide manual backup creation tools
- Include backup restoration capabilities
- Show storage usage and limits
- Implement backup scheduling interface
- Validate backup management permissions
- Use secure backup handling
- Monitor backup integrity

**Don't**:
- Don't allow unauthorized backup access
- Don't expose backup storage credentials
- Don't skip backup validation
- Don't ignore storage security
- Don't omit backup monitoring
- Don't bypass safety protocols

## 2030b/resources/views/admin/reports/financial.blade.php

**Purpose**: Comprehensive financial reporting interface showing revenue analytics, payment trends, refund tracking, and financial performance metrics for business intelligence.

**Dependencies**:
- **Layout**: @extends('layouts.admin')
- **Blade Directives**: @auth, @can('view-financial-reports'), @foreach, @if
- **Components**: &lt;x-card&gt;, &lt;x-charts.line-chart&gt;, &lt;x-charts.bar-chart&gt;, &lt;x-button&gt;
- **Frontend Assets**: Financial charting, report export, date range pickers
- **Localization**: __('admin.financial_reports'), __('reports.revenue'), __('reports.export')

**Sections / Slots**:
- @section('title', __('admin.financial_reports_title'))
- @section('content')
- @section('scripts') for financial reporting

**Variables Used**:
- $revenue_data: Revenue analytics and trends
- $payment_methods: Payment method performance
- $refund_data: Refund statistics and trends
- $currency_breakdown: Multi-currency financial data
- $period_comparison: Period-over-period analysis

**Do**:
- Provide detailed financial analytics
- Include revenue trend analysis
- Show payment method performance
- Display refund and chargeback data
- Implement financial export functionality
- Use professional report formatting
- Validate financial report access
- Cache financial calculations

**Don't**:
- Don't expose sensitive financial data inappropriately
- Don't allow unauthorized report access
- Don't skip financial data validation
- Don't ignore accuracy requirements
- Don't omit important financial metrics
- Don't bypass compliance standards

## 2030b/resources/views/admin/reports/user-activity.blade.php

**Purpose**: User activity reporting dashboard displaying engagement metrics, user behavior patterns, course completion rates, and platform usage analytics for administrative insights.

**Dependencies**:
- **Layout**: @extends('layouts.admin')
- **Blade Directives**: @auth, @can('view-user-reports'), @foreach, @if
- **Components**: &lt;x-card&gt;, &lt;x-charts.line-chart&gt;, &lt;x-charts.pie-chart&gt;, &lt;x-button&gt;
- **Frontend Assets**: Activity analytics, user behavior charts, export tools
- **Localization**: __('admin.user_reports'), __('reports.activity'), __('reports.engagement')

**Sections / Slots**:
- @section('title', __('admin.user_activity_reports_title'))
- @section('content')
- @section('scripts') for activity reporting

**Variables Used**:
- $activity_data: User activity and engagement metrics
- $completion_rates: Course completion statistics
- $user_segments: User behavior segmentation
- $retention_data: User retention analytics
- $engagement_trends: Engagement pattern analysis

**Do**:
- Display comprehensive user activity metrics
- Show engagement and retention analytics
- Include course completion tracking
- Provide user behavior segmentation
- Implement activity trend analysis
- Use privacy-compliant reporting
- Validate report access permissions
- Cache activity analytics

**Don't**:
- Don't expose individual user privacy data
- Don't allow unauthorized activity reports
- Don't skip privacy compliance
- Don't ignore data anonymization
- Don't omit important engagement metrics
- Don't bypass user consent requirements

## 2030b/resources/views/admin/reports/affiliate-performance.blade.php

**Purpose**: Affiliate program performance reporting showing commission analytics, referral effectiveness, conversion metrics, and affiliate network insights for program optimization.

**Dependencies**:
- **Layout**: @extends('layouts.admin')
- **Blade Directives**: @auth, @can('view-affiliate-reports'), @foreach, @if
- **Components**: &lt;x-card&gt;, &lt;x-charts.bar-chart&gt;, &lt;x-charts.line-chart&gt;, &lt;x-button&gt;
- **Frontend Assets**: Affiliate analytics, performance charts, export functionality
- **Localization**: __('admin.affiliate_reports'), __('reports.commissions'), __('reports.conversions')

**Sections / Slots**:
- @section('title', __('admin.affiliate_performance_title'))
- @section('content')
- @section('scripts') for affiliate reporting

**Variables Used**:
- $affiliate_metrics: Affiliate performance data
- $commission_data: Commission analytics and trends
- $conversion_rates: Affiliate conversion statistics
- $top_performers: Best performing affiliates
- $referral_sources: Traffic source analysis

**Do**:
- Display comprehensive affiliate analytics
- Show commission and payout trends
- Include conversion rate analysis
- Provide top performer insights
- Implement affiliate comparison tools
- Use secure affiliate data handling
- Validate affiliate report access
- Cache performance metrics

**Don't**:
- Don't expose sensitive affiliate data
- Don't allow unauthorized affiliate reports
- Don't skip affiliate privacy considerations
- Don't ignore performance accuracy
- Don't omit important conversion metrics
- Don't bypass affiliate agreement terms

## 2030b/resources/views/api/documentation.blade.php

**Purpose**: API documentation interface providing comprehensive REST API documentation, endpoint specifications, authentication guides, and interactive testing tools for developers.

**Dependencies**:
- **Layout**: @extends('layouts.api')
- **Blade Directives**: @foreach, @if, @switch
- **Components**: &lt;x-card&gt;, &lt;x-badge&gt;, &lt;x-button&gt;, &lt;x-modal&gt;
- **Frontend Assets**: Syntax highlighting, API testing tools, copy-to-clipboard
- **Localization**: __('api.documentation'), __('api.endpoints'), __('api.examples')

**Sections / Slots**:
- @section('title', __('api.documentation_title'))
- @section('content')
- @section('scripts') for interactive features

**Variables Used**:
- $api_endpoints: Available API endpoints documentation
- $authentication: API authentication methods
- $examples: Code examples and responses
- $rate_limits: API rate limiting information
- $changelog: API version history

**Do**:
- Provide comprehensive API documentation
- Include interactive endpoint testing
- Show request/response examples
- Display authentication requirements
- Include rate limiting information
- Provide SDK and library information
- Use syntax highlighting for code
- Maintain up-to-date documentation

**Don't**:
- Don't expose API keys or credentials
- Don't provide outdated documentation
- Don't skip authentication requirements
- Don't ignore rate limiting details
- Don't omit error handling examples
- Don't bypass security considerations

## 2030b/resources/views/legal/terms.blade.php

**Purpose**: Terms of service page displaying platform usage terms, user obligations, service policies, and legal agreements with proper formatting and version tracking.

**Dependencies**:
- **Layout**: @extends('layouts.guest')
- **Blade Directives**: @if, @foreach
- **Components**: &lt;x-card&gt;, &lt;x-button&gt;
- **Frontend Assets**: Legal document styling, print functionality
- **Localization**: __('legal.terms'), __('legal.updated'), __('legal.accept')

**Sections / Slots**:
- @section('title', __('legal.terms_title'))
- @section('content')
- @section('meta') for SEO and legal metadata

**Variables Used**:
- $terms_content: Terms of service content
- $last_updated: Last modification date
- $version: Terms version number
- $contact_info: Legal contact information

**Do**:
- Display clear and readable terms
- Include last updated information
- Provide print-friendly formatting
- Show version information
- Include contact information for questions
- Use proper legal formatting
- Maintain accessibility standards
- Track user acceptance

**Don't**:
- Don't use unclear legal language unnecessarily
- Don't omit important terms or conditions
- Don't ignore accessibility requirements
- Don't skip version tracking
- Don't hide important information
- Don't bypass legal requirements

## 2030b/resources/views/legal/privacy.blade.php

**Purpose**: Privacy policy page detailing data collection, usage, storage, and user rights in compliance with privacy regulations like GDPR and CCPA.

**Dependencies**:
- **Layout**: @extends('layouts.guest')
- **Blade Directives**: @if, @foreach
- **Components**: &lt;x-card&gt;, &lt;x-button&gt;, &lt;x-alert&gt;
- **Frontend Assets**: Legal document styling, cookie consent integration
- **Localization**: __('legal.privacy'), __('privacy.data_collection'), __('privacy.rights')

**Sections / Slots**:
- @section('title', __('legal.privacy_title'))
- @section('content')
- @section('meta') for privacy policy metadata

**Variables Used**:
- $privacy_content: Privacy policy content sections
- $data_types: Types of data collected
- $user_rights: User privacy rights information
- $contact_info: Privacy contact information

**Do**:
- Provide clear privacy information
- Include data collection details
- Explain user rights and controls
- Display cookie usage information
- Include contact information for privacy questions
- Maintain GDPR/CCPA compliance
- Use accessible formatting
- Track policy updates

**Don't**:
- Don't use vague privacy language
- Don't omit important privacy information
- Don't ignore regulatory requirements
- Don't hide data usage details
- Don't skip user rights information
- Don't bypass consent requirements

## 2030b/resources/views/legal/refund-policy.blade.php

**Purpose**: Refund and cancellation policy page outlining refund eligibility, processing procedures, timelines, and customer rights for platform purchases.

**Dependencies**:
- **Layout**: @extends('layouts.guest')
- **Blade Directives**: @if, @foreach
- **Components**: &lt;x-card&gt;, &lt;x-button&gt;, &lt;x-alert&gt;
- **Frontend Assets**: Legal document styling, refund calculator
- **Localization**: __('legal.refunds'), __('refunds.eligibility'), __('refunds.process')

**Sections / Slots**:
- @section('title', __('legal.refund_policy_title'))
- @section('content')
- @section('meta') for refund policy metadata

**Variables Used**:
- $refund_policy: Refund policy content
- $eligibility_criteria: Refund eligibility requirements
- $processing_times: Refund processing timelines
- $contact_info: Refund support contact information

**Do**:
- Clearly state refund eligibility criteria
- Provide processing timeline information
- Include contact information for refund requests
- Display policy effective dates
- Use clear, understandable language
- Include examples where helpful
- Maintain consistent formatting
- Provide policy update notifications

**Don't**:
- Don't use confusing refund terms
- Don't hide refund limitations
- Don't omit processing timeframes
- Don't ignore customer rights
- Don't skip contact information
- Don't bypass legal requirements

## 2030b/resources/views/legal/affiliate-agreement.blade.php

**Purpose**: Affiliate program terms and conditions page detailing affiliate obligations, commission structures, promotional guidelines, and legal requirements for affiliate partnerships.

**Dependencies**:
- **Layout**: @extends('layouts.guest')
- **Blade Directives**: @if, @foreach, @auth
- **Components**: &lt;x-card&gt;, &lt;x-button&gt;, &lt;x-alert&gt;
- **Frontend Assets**: Legal document styling, agreement tracking
- **Localization**: __('legal.affiliate_agreement'), __('affiliate.terms'), __('affiliate.commissions')

**Sections / Slots**:
- @section('title', __('legal.affiliate_agreement_title'))
- @section('content')
- @section('meta') for affiliate agreement metadata

**Variables Used**:
- $agreement_content: Affiliate agreement terms
- $commission_structure: Commission rates and rules
- $promotional_guidelines: Marketing and promotion rules
- $termination_policy: Agreement termination terms

**Do**:
- Clearly define affiliate obligations
- Display commission structure details
- Include promotional guidelines and restrictions
- Provide termination and dispute procedures
- Include intellectual property guidelines
- Display agreement effective dates
- Use professional legal formatting
- Track agreement acceptance

**Don't**:
- Don't use unclear affiliate terms
- Don't omit commission calculation details
- Don't ignore promotional restrictions
- Don't skip termination procedures
- Don't hide important obligations
- Don't bypass compliance requirements

## 2030b/resources/views/errors/403.blade.php

**Purpose**: Access forbidden error page providing user-friendly messaging for unauthorized access attempts with appropriate navigation options and support information.

**Dependencies**:
- **Layout**: @extends('layouts.error')
- **Blade Directives**: @auth, @guest, @if
- **Components**: &lt;x-button&gt;, &lt;x-card&gt;, &lt;x-alert&gt;
- **Frontend Assets**: Error page styling, navigation helpers
- **Localization**: __('errors.403'), __('errors.forbidden'), __('errors.contact_support')

**Sections / Slots**:
- @section('title', __('errors.403_title'))
- @section('content')
- @section('code', '403')

**Variables Used**:
- $message: Custom error message if available
- $exception: Exception details for debugging
- $user: Current user for context-aware messaging

**Do**:
- Provide clear error explanation
- Include appropriate navigation options
- Show context-aware messaging
- Provide contact information for support
- Maintain consistent error page design
- Include search functionality
- Log unauthorized access attempts
- Use helpful error messaging

**Don't**:
- Don't expose sensitive system information
- Don't provide technical error details to users
- Don't ignore security logging
- Don't omit navigation options
- Don't use intimidating error messages
- Don't skip user support options

## 2030b/resources/views/errors/404.blade.php

**Purpose**: Page not found error page providing helpful navigation, search functionality, and suggested content when users encounter missing pages or broken links.

**Dependencies**:
- **Layout**: @extends('layouts.error')
- **Blade Directives**: @if, @foreach
- **Components**: &lt;x-button&gt;, &lt;x-input&gt;, &lt;x-card&gt;
- **Frontend Assets**: Error page styling, search functionality
- **Localization**: __('errors.404'), __('errors.not_found'), __('errors.suggestions')

**Sections / Slots**:
- @section('title', __('errors.404_title'))
- @section('content')
- @section('code', '404')

**Variables Used**:
- $suggested_pages: Recommended pages for user
- $popular_courses: Popular course suggestions
- $search_term: Pre-filled search if available

**Do**:
- Provide helpful navigation suggestions
- Include search functionality
- Show popular or related content
- Maintain friendly and helpful tone
- Include clear navigation back to main site
- Log 404 errors for analysis
- Use engaging error page design
- Provide multiple recovery options

**Don't**:
- Don't use technical jargon in error messages
- Don't provide empty or unhelpful pages
- Don't ignore SEO implications
- Don't skip error logging
- Don't omit navigation options
- Don't create frustrating user experiences

## 2030b/resources/views/errors/419.blade.php

**Purpose**: CSRF token mismatch error page explaining session expiration and providing user-friendly options to retry the action or return to safety.

**Dependencies**:
- **Layout**: @extends('layouts.error')
- **Blade Directives**: @if, @csrf
- **Components**: &lt;x-button&gt;, &lt;x-alert&gt;, &lt;x-card&gt;
- **Frontend Assets**: Error page styling, form retry handlers
- **Localization**: __('errors.419'), __('errors.csrf_mismatch'), __('errors.retry')

**Sections / Slots**:
- @section('title', __('errors.419_title'))
- @section('content')
- @section('code', '419')

**Variables Used**:
- $previous_url: URL to retry the action
- $form_data: Previous form data if available
- $retry_action: Suggested retry action

**Do**:
- Explain CSRF protection in user-friendly terms
- Provide retry functionality
- Include navigation back to previous page
- Maintain user form data when possible
- Use reassuring messaging
- Log CSRF failures for security analysis
- Provide clear action options
- Include session refresh capabilities

**Don't**:
- Don't expose technical CSRF details
- Don't ignore security implications
- Don't frustrate users with technical explanations
- Don't skip security logging
- Don't omit retry options
- Don't create security vulnerabilities

## 2030b/resources/views/errors/429.blade.php

**Purpose**: Rate limit exceeded error page informing users about request throttling with wait times and guidelines for proper platform usage.

**Dependencies**:
- **Layout**: @extends('layouts.error')
- **Blade Directives**: @if
- **Components**: &lt;x-alert&gt;, &lt;x-button&gt;, &lt;x-card&gt;
- **Frontend Assets**: Error page styling, countdown timers
- **Localization**: __('errors.429'), __('errors.rate_limit'), __('errors.wait_time')

**Sections / Slots**:
- @section('title', __('errors.429_title'))
- @section('content')
- @section('code', '429')

**Variables Used**:
- $retry_after: Seconds until user can retry
- $limit_info: Rate limit information
- $guidelines: Usage guidelines for users

**Do**:
- Clearly explain rate limiting
- Show remaining wait time
- Provide usage guidelines
- Include automatic retry options
- Use informative messaging
- Log rate limit violations
- Provide countdown timers
- Include contact information for questions

**Don't**:
- Don't use harsh or punitive language
- Don't expose technical rate limit details
- Don't ignore user education
- Don't skip logging for abuse detection
- Don't omit retry information
- Don't create frustrating experiences

## 2030b/resources/views/errors/500.blade.php

**Purpose**: Internal server error page providing professional error handling with incident tracking and user support options while maintaining security and user confidence.

**Dependencies**:
- **Layout**: @extends('layouts.error')
- **Blade Directives**: @if
- **Components**: &lt;x-alert&gt;, &lt;x-button&gt;, &lt;x-card&gt;
- **Frontend Assets**: Error page styling, error reporting
- **Localization**: __('errors.500'), __('errors.server_error'), __('errors.support')

**Sections / Slots**:
- @section('title', __('errors.500_title'))
- @section('content')
- @section('code', '500')

**Variables Used**:
- $incident_id: Error tracking identifier
- $contact_info: Support contact information
- $status_page: System status page URL if available

**Do**:
- Provide professional error messaging
- Include incident tracking information
- Show support contact options
- Maintain user confidence in the platform
- Log detailed error information
- Provide status page links when available
- Use reassuring language
- Include retry suggestions

**Don't**:
- Don't expose technical error details
- Don't blame users for system errors
- Don't ignore error logging and monitoring
- Don't skip incident tracking
- Don't omit support contact information
- Don't create panic or concern

## 2030b/resources/views/errors/503.blade.php

**Purpose**: Service unavailable error page for maintenance mode or system outages with estimated restoration times and communication channels for updates.

**Dependencies**:
- **Layout**: @extends('layouts.error')
- **Blade Directives**: @if
- **Components**: &lt;x-alert&gt;, &lt;x-button&gt;, &lt;x-card&gt;
- **Frontend Assets**: Error page styling, maintenance timers
- **Localization**: __('errors.503'), __('errors.maintenance'), __('errors.estimated_time')

**Sections / Slots**:
- @section('title', __('errors.503_title'))
- @section('content')
- @section('code', '503')

**Variables Users**:
- $maintenance_message: Custom maintenance message
- $estimated_completion: Expected completion time
- $status_updates: Communication channels for updates

**Do**:
- Provide clear maintenance messaging
- Show estimated completion times
- Include communication channels for updates
- Maintain professional appearance
- Use reassuring language about service restoration
- Provide alternative contact methods
- Include social media links for updates
- Log maintenance access attempts

**Don't**:
- Don't provide inaccurate time estimates
- Don't ignore user communication needs
- Don't omit update channels
- Don't create unnecessary concern
- Don't skip maintenance logging
- Don't provide unclear messaging

## 2030b/resources/views/emails/auth/welcome.blade.php

**Purpose**: Welcome email template for new user registration providing account confirmation, platform introduction, and first steps for user onboarding.

**Dependencies**:
- **Layout**: @extends('emails.layout')
- **Blade Directives**: @if, @foreach
- **Components**: Email-specific components for buttons and formatting
- **Frontend Assets**: Email-safe CSS, responsive email design
- **Localization**: __('emails.welcome'), __('auth.welcome_message'), __('auth.get_started')

**Sections / Slots**:
- @section('content')
- @section('subject', __('emails.welcome_subject'))
- @section('preheader', __('emails.welcome_preheader'))

**Variables Used**:
- $user: New user instance
- $login_url: Direct login link for user
- $onboarding_steps: Suggested first actions
- $support_contact: Support contact information

**Do**:
- Provide warm welcome messaging
- Include clear next steps for users
- Show direct login or activation links
- Use responsive email design
- Include support contact information
- Maintain brand consistency
- Use accessible email formatting
- Include unsubscribe options

**Don't**:
- Don't overwhelm users with too much information
- Don't include suspicious-looking links
- Don't omit important account information
- Don't ignore email accessibility
- Don't skip brand consistency
- Don't omit legal compliance requirements

## 2030b/resources/views/emails/auth/verify-email.blade.php

**Purpose**: Email verification template containing secure verification links and instructions for users to confirm their email addresses during registration or changes.

**Dependencies**:
- **Layout**: @extends('emails.layout')
- **Blade Directives**: @if
- **Components**: Email verification components, secure buttons
- **Frontend Assets**: Email-safe CSS, verification styling
- **Localization**: __('emails.verify'), __('auth.verify_email'), __('auth.click_to_verify')

**Sections / Slots**:
- @section('content')
- @section('subject', __('emails.verify_subject'))
- @section('preheader', __('emails.verify_preheader'))

**Variables Used**:
- $user: User requiring email verification
- $verification_url: Secure verification link
- $expires_at: Link expiration time
- $resend_url: Link to request new verification

**Do**:
- Provide clear verification instructions
- Include secure verification links
- Show link expiration information
- Use professional email formatting
- Include security messaging
- Provide resend verification option
- Maintain email security best practices
- Use accessible design

**Don't**:
- Don't expose verification tokens unnecessarily
- Don't create confusing verification steps
- Don't omit security warnings
- Don't ignore link expiration
- Don't skip secure link generation
- Don't create verification vulnerabilities

## 2030b/resources/views/emails/auth/password-reset.blade.php

**Purpose**: Password reset email template providing secure reset links, security instructions, and account protection guidance for users requesting password changes.

**Dependencies**:
- **Layout**: @extends('emails.layout')
- **Blade Directives**: @if
- **Components**: Secure email buttons, password reset components
- **Frontend Assets**: Email-safe CSS, security-focused styling
- **Localization**: __('emails.password_reset'), __('auth.reset_password'), __('auth.security_notice')

**Sections / Slots**:
- @section('content')
- @section('subject', __('emails.password_reset_subject'))
- @section('preheader', __('emails.password_reset_preheader'))

**Variables Used**:
- $user: User requesting password reset
- $reset_url: Secure password reset link
- $expires_at: Reset link expiration time
- $ip_address: Request IP for security notice

**Do**:
- Provide clear password reset instructions
- Include secure reset links with expiration
- Show security notices about the request
- Use professional security messaging
- Include contact information for unauthorized requests
- Maintain email security standards
- Provide account protection guidance
- Use accessible email design

**Don't**:
- Don't expose reset tokens unnecessarily
- Don't ignore security implications
- Don't omit expiration information
- Don't skip unauthorized request warnings
- Don't create security vulnerabilities
- Don't use unclear instructions

## 2030b/resources/views/emails/payments/receipt.blade.php

**Purpose**: Payment receipt email template providing transaction details, invoice information, and course access confirmation for successful purchases.

**Dependencies**:
- **Layout**: @extends('emails.layout')
- **Blade Directives**: @foreach, @if
- **Components**: Invoice formatting components, payment details
- **Frontend Assets**: Email-safe CSS, receipt styling, print optimization
- **Localization**: __('emails.receipt'), __('payments.transaction_details'), __('payments.thank_you')

**Sections / Slots**:
- @section('content')
- @section('subject', __('emails.receipt_subject'))
- @section('preheader', __('emails.receipt_preheader'))

**Variables Used**:
- $transaction: Payment transaction details
- $user: Customer information
- $items: Purchased items details
- $invoice: Associated invoice if applicable
- $course_access: New course access information

**Do**:
- Provide detailed transaction information
- Include itemized purchase breakdown
- Show course access confirmation
- Use professional receipt formatting
- Include tax and fee breakdowns
- Provide invoice download links
- Maintain financial record accuracy
- Use print-friendly design

**Don't**:
- Don't omit important transaction details
- Don't expose sensitive payment information
- Don't ignore tax compliance requirements
- Don't skip course access notifications
- Don't provide inaccurate financial information
- Don't ignore receipt formatting standards

## 2030b/resources/views/emails/payments/invoice.blade.php

**Purpose**: Invoice email template for billing purposes providing detailed invoice information, payment instructions, and due date notifications for outstanding payments.

**Dependencies**:
- **Layout**: @extends('emails.layout')
- **Blade Directives**: @foreach, @if
- **Components**: Professional invoice components, payment instructions
- **Frontend Assets**: Email-safe CSS, invoice styling, business formatting
- **Localization**: __('emails.invoice'), __('invoices.payment_due'), __('invoices.details')

**Sections / Slots**:
- @section('content')
- @section('subject', __('emails.invoice_subject'))
- @section('preheader', __('emails.invoice_preheader'))

**Variables Used**:
- $invoice: Invoice details and line items
- $user: Customer billing information
- $due_date: Payment due date
- $payment_methods: Available payment options
- $late_fees: Late payment fee information

**Do**:
- Provide complete invoice information
- Include clear payment instructions
- Show due dates and late fee policies
- Use professional invoice formatting
- Include multiple payment method options
- Provide customer service contact information
- Maintain accounting compliance
- Use business-appropriate design

**Don't**:
- Don't omit critical billing information
- Don't provide unclear payment instructions
- Don't ignore due date notifications
- Don't skip payment method options
- Don't omit late fee disclosures
- Don't ignore billing compliance requirements

## 2030b/resources/views/emails/payments/refund.blade.php

**Purpose**: Refund notification email template confirming refund processing, providing refund details, and explaining processing timelines for customer financial records.

**Dependencies**:
- **Layout**: @extends('emails.layout')
- **Blade Directives**: @if, @foreach
- **Components**: Refund confirmation components, financial details
- **Frontend Assets**: Email-safe CSS, refund notification styling
- **Localization**: __('emails.refund'), __('refunds.confirmed'), __('refunds.processing_time')

**Sections / Slots**:
- @section('content')
- @section('subject', __('emails.refund_subject'))
- @section('preheader', __('emails.refund_preheader'))

**Variables Used**:
- $refund: Refund transaction details
- $original_transaction: Original purchase information
- $processing_time: Expected refund processing duration
- $user: Customer receiving refund

**Do**:
- Provide clear refund confirmation
- Include original transaction references
- Show expected processing timelines
- Use reassuring and professional messaging
- Include customer service contact information
- Provide refund tracking information
- Maintain financial accuracy
- Use accessible email design

**Don't**:
- Don't omit refund processing details
- Don't provide inaccurate timeline information
- Don't ignore customer communication needs
- Don't skip original transaction references
- Don't omit customer service contact
- Don't create confusion about refund status

## 2030b/resources/views/emails/courses/published.blade.php

**Purpose**: Course publication notification email for instructors confirming course approval, providing course URLs, and sharing promotion resources for newly published courses.

**Dependencies**:
- **Layout**: @extends('emails.layout')
- **Blade Directives**: @if, @foreach
- **Components**: Course promotion components, instructor resources
- **Frontend Assets**: Email-safe CSS, course showcase styling
- **Localization**: __('emails.course_published'), __('courses.congratulations'), __('courses.promote')

**Sections / Slots**:
- @section('content')
- @section('subject', __('emails.course_published_subject'))
- @section('preheader', __('emails.course_published_preheader'))

**Variables Used**:
- $course: Newly published course details
- $instructor: Course instructor information
- $course_url: Direct course access link
- $promotion_tools: Marketing and promotion resources

**Do**:
- Congratulate instructor on course publication
- Provide direct course access links
- Include promotion and marketing resources
- Show course analytics and tracking information
- Provide instructor support resources
- Use encouraging and motivational messaging
- Include social sharing options
- Maintain professional instructor communication

**Don't**:
- Don't omit important course information
- Don't skip promotion resource sharing
- Don't ignore instructor support needs
- Don't provide broken course links
- Don't omit analytics access information
- Don't skip congratulatory messaging

## 2030b/resources/views/emails/courses/completed.blade.php

**Purpose**: Course completion congratulation email for students providing completion certificates, achievement recognition, and recommendations for continued learning.

**Dependencies**:
- **Layout**: @extends('emails.layout')
- **Blade Directives**: @if, @foreach
- **Components**: Achievement components, certificate links, recommendation widgets
- **Frontend Assets**: Email-safe CSS, celebration styling, certificate formatting
- **Localization**: __('emails.course_completed'), __('courses.congratulations'), __('courses.next_steps')

**Sections / Slots**:
- @section('content')
- @section('subject', __('emails.course_completed_subject'))
- @section('preheader', __('emails.course_completed_preheader'))

**Variables Used**:
- $course: Completed course information
- $user: Student who completed course
- $certificate: Completion certificate details
- $recommendations: Recommended next courses
- $achievements: Unlocked achievements or badges

**Do**:
- Congratulate student on course completion
- Provide completion certificate access
- Include achievement and badge information
- Show recommended next courses
- Use celebratory and motivational messaging
- Provide social sharing options for achievements
- Include learning progress tracking
- Maintain encouraging tone

**Don't**:
- Don't omit certificate access information
- Don't skip achievement recognition
- Don't ignore next learning recommendations
- Don't provide generic completion messages
- Don't omit social sharing capabilities
- Don't skip motivation for continued learning

## 2030b/resources/views/emails/courses/certificate.blade.php

**Purpose**: Certificate delivery email template providing downloadable completion certificates, verification information, and sharing options for course achievements.

**Dependencies**:
- **Layout**: @extends('emails.layout')
- **Blade Directives**: @if
- **Components**: Certificate components, verification widgets, sharing tools
- **Frontend Assets**: Email-safe CSS, certificate styling, verification elements
- **Localization**: __('emails.certificate'), __('certificates.download'), __('certificates.verification')

**Sections / Slots**:
- @section('content')
- @section('subject', __('emails.certificate_subject'))
- @section('preheader', __('emails.certificate_preheader'))

**Variables Used**:
- $certificate: Certificate details and metadata
- $user: Certificate recipient
- $course: Associated course information
- $verification_url: Certificate verification link
- $download_url: Certificate download link

**Do**:
- Provide direct certificate download access
- Include certificate verification information
- Show course and achievement details
- Use professional certificate presentation
- Include social and professional sharing options
- Provide verification URLs for authenticity
- Maintain certificate security standards
- Use celebratory messaging

**Don't**:
- Don't omit certificate verification details
- Don't provide insecure certificate links
- Don't ignore certificate authenticity measures
- Don't skip download access information
- Don't omit sharing capabilities
- Don't bypass certificate security

## 2030b/resources/views/emails/affiliate/commission-earned.blade.php

**Purpose**: Commission notification email for affiliates confirming earned commissions, providing earning details, and showing payout information for successful referrals.

**Dependencies**:
- **Layout**: @extends('emails.layout')
- **Blade Directives**: @if, @foreach
- **Components**: Commission tracking components, earnings display
- **Frontend Assets**: Email-safe CSS, financial notification styling
- **Localization**: __('emails.commission_earned'), __('affiliate.earnings'), __('affiliate.payout_info')

**Sections / Slots**:
- @section('content')
- @section('subject', __('emails.commission_earned_subject'))
- @section('preheader', __('emails.commission_earned_preheader'))

**Variables Used**:
- $commission: Commission details and amount
- $affiliate: Affiliate account information
- $referral: Successful referral information
- $payout_schedule: Next payout information
- $total_earnings: Cumulative earnings data

**Do**:
- Congratulate affiliate on successful referral
- Provide detailed commission information
- Show payout schedule and processing information
- Include referral performance metrics
- Use motivational messaging for continued promotion
- Provide affiliate dashboard access
- Include support contact information
- Maintain earnings accuracy

**Don't**:
- Don't omit commission calculation details
- Don't provide inaccurate earnings information
- Don't skip payout processing information
- Don't ignore affiliate performance metrics
- Don't omit dashboard access information
- Don't bypass earnings verification

## 2030b/resources/views/emails/affiliate/referral-signed-up.blade.php

**Purpose**: Referral signup notification email informing affiliates when their referrals create accounts, providing referral tracking and potential commission information.

**Dependencies**:
- **Layout**: @extends('emails.layout')
- **Blade Directives**: @if
- **Components**: Referral tracking components, conversion information
- **Frontend Assets**: Email-safe CSS, referral notification styling
- **Localization**: __('emails.referral_signup'), __('affiliate.new_referral'), __('affiliate.conversion_potential')

**Sections / Slots**:
- @section('content')
- @section('subject', __('emails.referral_signup_subject'))
- @section('preheader', __('emails.referral_signup_preheader'))

**Variables Used**:
- $referral: New referral signup information
- $affiliate: Affiliate account details
- $referral_source: Source of the referral
- $potential_commission: Potential earning information

**Do**:
- Notify affiliate of successful referral signup
- Provide referral tracking information
- Show potential commission opportunities
- Include referral source and campaign details
- Use encouraging messaging for continued promotion
- Provide affiliate analytics access
- Include next steps for commission earning
- Maintain referral tracking accuracy

**Don't**:
- Don't omit referral tracking details
- Don't ignore referral source information
- Don't skip potential commission information
- Don't provide inaccurate referral data
- Don't omit affiliate dashboard access
- Don't bypass referral verification

## 2030b/resources/views/emails/affiliate/payout-processed.blade.php

**Purpose**: Payout confirmation email for affiliates confirming commission payments, providing transaction details, and showing payment method information for financial records.

**Dependencies**:
- **Layout**: @extends('emails.layout')
- **Blade Directives**: @if, @foreach
- **Components**: Payment confirmation components, financial details
- **Frontend Assets**: Email-safe CSS, payout notification styling
- **Localization**: __('emails.payout_processed'), __('affiliate.payment_sent'), __('affiliate.transaction_details')

**Sections / Slots**:
- @section('content')
- @section('subject', __('emails.payout_processed_subject'))
- @section('preheader', __('emails.payout_processed_preheader'))

**Variables Used**:
- $payout: Payout transaction details
- $affiliate: Affiliate receiving payment
- $commission_period: Period covered by payout
- $payment_method: Payment method used
- $transaction_id: Payout transaction identifier

**Do**:
- Confirm payout processing completion
- Provide detailed transaction information
- Show payment method and processing details
- Include commission period breakdown
- Provide transaction tracking information
- Use professional financial messaging
- Include contact information for questions
- Maintain payment record accuracy

**Don't**:
- Don't omit critical payout information
- Don't provide inaccurate payment details
- Don't skip transaction tracking information
- Don't ignore payment method verification
- Don't omit commission period details
- Don't bypass financial record requirements

## 2030b/resources/views/emails/system/backup-success.blade.php

**Purpose**: System backup notification email for administrators confirming successful backup completion, providing backup details, and showing storage information for system maintenance records.

**Dependencies**:
- **Layout**: @extends('emails.layout')
- **Blade Directives**: @if, @foreach
- **Components**: System status components, backup information
- **Frontend Assets**: Email-safe CSS, system notification styling
- **Localization**: __('emails.backup_success'), __('system.backup_completed'), __('system.storage_info')

**Sections / Slots**:
- @section('content')
- @section('subject', __('emails.backup_success_subject'))
- @section('preheader', __('emails.backup_success_preheader'))

**Variables Used**:
- $backup: Backup operation details
- $backup_size: Size of completed backup
- $storage_location: Backup storage information
- $next_backup: Next scheduled backup time
- $system_status: Overall system health status

**Do**:
- Confirm successful backup completion
- Provide backup size and storage details
- Include backup verification information
- Show next scheduled backup information
- Use professional system messaging
- Include system administrator contact
- Provide backup integrity verification
- Maintain system monitoring standards

**Don't**:
- Don't omit backup verification details
- Don't ignore storage capacity information
- Don't skip system status updates
- Don't provide inaccurate backup information
- Don't omit next backup scheduling
- Don't bypass system monitoring

## 2030b/resources/views/emails/system/system-alert.blade.php

**Purpose**: System alert email template for administrators providing critical system notifications, error alerts, and required administrative actions for platform maintenance and security.

**Dependencies**:
- **Layout**: @extends('emails.layout')
- **Blade Directives**: @if, @switch
- **Components**: Alert components, system status indicators
- **Frontend Assets**: Email-safe CSS, alert styling, urgency indicators
- **Localization**: __('emails.system_alert'), __('system.urgent'), __('system.action_required')

**Sections / Slots**:
- @section('content')
- @section('subject', __('emails.system_alert_subject'))
- @section('preheader', __('emails.system_alert_preheader'))

**Variables Used**:
- $alert: System alert details and severity
- $alert_type: Type of system alert
- $affected_services: Services impacted by alert
- $recommended_actions: Suggested administrative actions
- $contact_info: Emergency contact information

**Do**:
- Clearly communicate alert severity and urgency
- Provide specific system impact information
- Include recommended administrative actions
- Show affected services and systems
- Use appropriate urgency indicators
- Provide emergency contact information
- Include system status dashboard links
- Maintain system alert security

**Don't**:
- Don't downplay critical system issues
- Don't omit recommended actions
- Don't ignore affected service information
- Don't skip emergency contact details
- Don't provide unclear alert messaging
- Don't bypass system security protocols

## 2030b/resources/views/emails/system/new-user-registered.blade.php

**Purpose**: New user registration notification email for administrators providing user registration details, account verification status, and administrative review options for platform monitoring.

**Dependencies**:
- **Layout**: @extends('emails.layout')
- **Blade Directives**: @if
- **Components**: User information components, administrative actions
- **Frontend Assets**: Email-safe CSS, user notification styling
- **Localization**: __('emails.new_user'), __('admin.user_registered'), __('admin.review_user')

**Sections / Slots**:
- @section('content')
- @section('subject', __('emails.new_user_subject'))
- @section('preheader', __('emails.new_user_preheader'))

**Variables Used**:
- $user: Newly registered user information
- $registration_details: Registration metadata
- $verification_status: Email verification status
- $admin_actions: Available administrative actions
- $user_profile: Initial user profile information

**Do**:
- Provide comprehensive user registration details
- Include verification and approval status
- Show administrative review options
- Include user profile and registration metadata
- Use professional administrative messaging
- Provide user management dashboard access
- Include fraud detection indicators if applicable
- Maintain user privacy considerations

**Don't**:
- Don't expose sensitive user information inappropriately
- Don't omit verification status information
- Don't ignore administrative review requirements
- Don't skip fraud detection indicators
- Don't provide unclear registration details
- Don't bypass privacy protection measures

## 2030b/resources/views/emails/layout.blade.php

**Purpose**: Base email layout template providing consistent email formatting, branding, responsive design, and standard email components for all platform email communications.

**Dependencies**:
- **Blade Directives**: @yield, @section, @if, @isset
- **Components**: Email header, footer, branding components
- **Frontend Assets**: Email-safe CSS, responsive email framework, brand assets
- **Localization**: __('emails.footer'), __('emails.unsubscribe'), __('emails.contact')

**Sections / Slots**:
- @yield('subject') for email subject
- @yield('preheader') for email preheader text
- @yield('content') for main email content
- @section('footer') for email footer information

**Variables Used**:
- $user: Email recipient (when available)
- $company_info: Company branding and contact information
- $unsubscribe_url: Unsubscribe link for email preferences
- $social_links: Company social media links

**Do**:
- Provide consistent email branding
- Use responsive email design patterns
- Include required legal compliance elements
- Maintain accessible email formatting
- Use email-safe CSS and HTML
- Include unsubscribe functionality
- Provide company contact information
- Use professional email styling

**Don't**:
- Don't use unsupported email CSS
- Don't omit unsubscribe options
- Don't ignore email accessibility requirements
- Don't skip legal compliance elements
- Don't use non-responsive email design
- Don't omit company branding
