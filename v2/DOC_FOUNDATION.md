# app/Foundation/Core/ApplicationService.php

## Purpose
→ Base service class with common functionality for all application services
→ Provides standardized error handling and logging
→ Implements common patterns like validation and caching
→ Offers consistent transaction management

## Dependencies
use Illuminate\Support\Facades\DB
use Illuminate\Support\Facades\Log
use Illuminate\Support\Facades\Cache
use App\Foundation\Core\BaseDto
use App\DTOs\ApiResponse
use App\Exceptions\ValidationException

## Methods
execute()
validate()
handle()
rollback()
commit()
log()
cache()
invalidateCache()
handleException()
beforeExecution()
afterExecution()
getCacheKey()
getCacheTtl()

## Do
✅ Wrap business operations in database transactions
✅ Implement consistent logging across services
✅ Use standardized validation patterns
✅ Provide clear method documentation
✅ Handle exceptions gracefully with rollback

## Don't
❌ Don't expose internal implementation details
❌ Don't ignore transaction boundaries
❌ Don't skip validation for trusted input
❌ Don't log sensitive data like passwords
❌ Don't create services without clear single responsibility

# app/Foundation/Core/BaseDto.php

## Purpose
→ Base Data Transfer Object class with common functionality
→ Provides immutable data structures for service layer
→ Implements serialization and validation patterns
→ Offers type-safe data access methods

## Dependencies
use JsonSerializable
use ArrayAccess
use Illuminate\Contracts\Support\Arrayable
use Illuminate\Support\Collection
use App\Foundation\Core\Exceptions\ValidationException

## Methods
__construct()
toArray()
toJson()
fromArray()
validate()
has()
get()
set()
merge()
only()
except()
isEmpty()
isValid()
getErrors()

## Do
✅ Make DTOs immutable after construction
✅ Implement proper validation in constructors
✅ Use type hints for all properties
✅ Provide clear property documentation
✅ Support array and JSON serialization

## Don't
❌ Don't allow modification after construction
❌ Don't skip type validation
❌ Don't expose internal data structures
❌ Don't create DTOs without validation
❌ Don't use DTOs for database operations

# ValueObjects: MoneyValue, EmailValue, UuidValue, PercentageValue

## Purpose
→ Immutable value objects for domain-specific data types
→ Encapsulate validation and business rules
→ Provide type safety and consistency
→ Support comparison and equality operations

## Dependencies (MoneyValue)
use Money\Money
use Money\Currency
use App\Enums\CurrencyType
use App\Models\Payment\Currency as CurrencyModel

## Dependencies (EmailValue)
use Illuminate\Support\Facades\Validator

## Dependencies (UuidValue)
use Ramsey\Uuid\Uuid
use Ramsey\Uuid\UuidInterface

## Dependencies (PercentageValue)
use InvalidArgumentException

## Methods (Common)
__construct()
equals()
toString()
__toString()
jsonSerialize()
toArray()

## Methods (MoneyValue)
getAmount()
getCurrency()
add()
subtract()
multiply()
divide()
isPositive()
isNegative()
format()

## Methods (EmailValue)
getDomain()
getLocalPart()
isValid()
normalize()

## Methods (UuidValue)
generate()
isValid()
getVersion()

## Methods (PercentageValue)
asDecimal()
asInteger()
format()
isValid()

## Do
✅ Always validate input in constructors
✅ Make value objects immutable
✅ Implement proper equality comparison
✅ Use specific validation rules for each type
✅ Provide meaningful string representations

## Don't
❌ Don't allow invalid values to be constructed
❌ Don't make value objects mutable
❌ Don't skip validation for performance
❌ Don't use generic validation rules
❌ Don't expose internal implementation details

# Core Exceptions: DomainException, InfrastructureException, ValidationException

## Purpose
→ Base exception classes for different application layers
→ Provide consistent error handling patterns
→ Support error categorization and logging
→ Enable proper exception hierarchy

## Dependencies
use Exception
use Throwable
use App\Enums\LogLevel

## Methods (DomainException)
__construct()
getErrorCode()
getErrorType()
getDomainContext()
shouldReport()
toArray()

## Methods (InfrastructureException)
__construct()
getService()
getOperation()
getInfrastructureContext()
shouldRetry()
getRetryDelay()

## Methods (ValidationException)
__construct()
getField()
getRule()
getValue()
getValidationContext()
getErrorBag()

## Do
✅ Use appropriate exception types for different layers
✅ Include relevant context in exception messages
✅ Implement proper exception hierarchy
✅ Log exceptions with appropriate severity
✅ Provide actionable error information

## Don't
❌ Don't use generic Exception class directly
❌ Don't mix domain and infrastructure concerns
❌ Don't ignore exception context
❌ Don't expose sensitive data in exceptions
❌ Don't create exceptions without clear purpose

# app/Foundation/Domain/AggregateRoot.php

## Purpose
→ Base class for domain aggregate roots in DDD architecture
→ Manages domain events and business invariants
→ Provides consistent entity lifecycle management
→ Implements aggregate boundary enforcement

## Dependencies
use App\Foundation\Domain\Entity
use App\Foundation\Domain\DomainEvent
use Illuminate\Support\Collection

## Methods
__construct()
recordEvent()
releaseEvents()
getRecordedEvents()
clearRecordedEvents()
markAsChanged()
isChanged()
getVersion()
incrementVersion()
applyEvent()
when()
reconstitute()

## Properties
recordedEvents
version
isChanged

## Do
✅ Record domain events for important business actions
✅ Enforce business invariants in methods
✅ Use aggregate boundaries to maintain consistency
✅ Implement proper event sourcing if needed
✅ Validate state changes before applying

## Don't
❌ Don't allow direct access to internal collections
❌ Don't bypass business rules in domain methods
❌ Don't create aggregates larger than necessary
❌ Don't ignore aggregate version conflicts
❌ Don't leak domain events outside aggregate boundaries

# app/Foundation/Domain/Entity.php

## Purpose
→ Base class for all domain entities with identity
→ Provides UUID-based identity management
→ Implements equality and comparison operations
→ Supports entity lifecycle tracking

## Dependencies
use App\Foundation\Core\ValueObjects\UuidValue
use App\Traits\HasUuid
use Carbon\Carbon

## Methods
__construct()
getId()
getUuid()
equals()
sameIdentityAs()
createdAt()
updatedAt()
isNew()
hasBeenPersisted()
markAsPersisted()
touch()

## Properties
id
uuid
createdAt
updatedAt
isPersisted

## Do
✅ Use UUID for external entity references
✅ Implement proper equality based on identity
✅ Track entity lifecycle states
✅ Validate entity invariants
✅ Use meaningful entity identifiers

## Don't
❌ Don't expose internal ID to external systems
❌ Don't implement equality based on attributes
❌ Don't ignore entity lifecycle events
❌ Don't create entities without proper validation
❌ Don't modify entity identity after creation

# app/Foundation/Domain/DomainEvent.php

## Purpose
→ Base class for all domain events in event-driven architecture
→ Provides event metadata and serialization
→ Implements event versioning and compatibility
→ Supports event sourcing patterns

## Dependencies
use App\Events\Contracts\DomainEventInterface
use App\Foundation\Core\ValueObjects\UuidValue
use Carbon\Carbon
use JsonSerializable

## Methods
__construct()
getEventId()
getAggregateId()
getEventType()
getOccurredOn()
getVersion()
getPayload()
serialize()
deserialize()
toArray()
jsonSerialize()

## Properties
eventId
aggregateId
eventType
occurredOn
version
payload

## Do
✅ Include all necessary event metadata
✅ Make events immutable after creation
✅ Use semantic event names
✅ Version events for backward compatibility
✅ Include aggregate context in events

## Don't
❌ Don't modify events after creation
❌ Don't include mutable objects in event payload
❌ Don't create events without proper context
❌ Don't ignore event versioning requirements
❌ Don't expose sensitive data in events

# app/Foundation/Domain/ValueObject.php

## Purpose
→ Abstract base class for all domain value objects
→ Enforces immutability and value equality
→ Provides common value object operations
→ Implements proper comparison semantics

## Dependencies
use JsonSerializable
use Illuminate\Contracts\Support\Arrayable

## Methods (Abstract)
equals()
__toString()

## Methods (Concrete)
sameValueAs()
toArray()
jsonSerialize()
__construct()
validate()

## Do
✅ Make all value objects immutable
✅ Implement equals() method for proper comparison
✅ Validate all input in constructor
✅ Provide meaningful string representation
✅ Use value objects for complex domain concepts

## Don't
❌ Don't create mutable value objects
❌ Don't implement identity-based equality
❌ Don't skip input validation
❌ Don't use value objects for simple primitive types
❌ Don't expose internal state directly

# app/Foundation/Infrastructure/ServiceProvider.php

## Purpose
→ Enhanced base service provider with common registration patterns
→ Provides standardized service binding and configuration
→ Implements consistent provider lifecycle management
→ Supports conditional service registration

## Dependencies
use Illuminate\Support\ServiceProvider as BaseServiceProvider
use Illuminate\Contracts\Container\Container
use App\Contracts\Services\UserServiceInterface
use App\Services\User\UserService

## Methods
register()
boot()
provides()
when()
isDeferred()
bindServices()
bindRepositories()
bindContracts()
publishConfiguration()
publishAssets()
registerCommands()
registerMiddleware()
mergeConfigFrom()

## Do
✅ Register all service bindings in register() method
✅ Use boot() method for post-registration setup
✅ Implement proper service provider dependencies
✅ Use deferred providers for performance
✅ Document all registered services

## Don't
❌ Don't perform heavy operations in register() method
❌ Don't access services during registration
❌ Don't ignore provider dependencies
❌ Don't register services conditionally without proper guards
❌ Don't skip interface-to-implementation bindings

# app/Foundation/Infrastructure/CommandBus.php

## Purpose
→ Command bus implementation for CQRS pattern
→ Dispatches commands to appropriate handlers
→ Provides middleware support for cross-cutting concerns
→ Implements command validation and logging

## Dependencies
use Illuminate\Contracts\Container\Container
use App\Commands\CreateUserCommand
use App\Handlers\Commands\CreateUserHandler
use App\Foundation\Core\Exceptions\ValidationException

## Methods
dispatch()
handle()
registerHandler()
addMiddleware()
getHandler()
validateCommand()
logCommand()
executeMiddleware()
resolveHandler()

## Properties
handlers
middleware
container

## Do
✅ Register all command handlers explicitly
✅ Validate commands before dispatching
✅ Use middleware for cross-cutting concerns
✅ Log command execution for audit trails
✅ Handle command failures gracefully

## Don't
❌ Don't dispatch commands without validation
❌ Don't ignore command handler registration
❌ Don't skip middleware execution
❌ Don't expose command bus internals
❌ Don't use command bus for queries

# app/Foundation/Infrastructure/QueryBus.php

## Purpose
→ Query bus implementation for CQRS pattern
→ Dispatches queries to appropriate handlers
→ Provides caching and performance optimization
→ Implements query validation and result formatting

## Dependencies
use Illuminate\Contracts\Container\Container
use Illuminate\Support\Facades\Cache
use App\Queries\GetUserQuery
use App\Handlers\Queries\GetUserHandler

## Methods
ask()
handle()
registerHandler()
addMiddleware()
getHandler()
validateQuery()
cacheResult()
getCachedResult()
formatResult()
resolveHandler()

## Properties
handlers
middleware
container
cacheConfig

## Do
✅ Register all query handlers explicitly
✅ Implement result caching where appropriate
✅ Validate queries before execution
✅ Format results consistently
✅ Use middleware for authorization and logging

## Don't
❌ Don't use query bus for commands
❌ Don't cache sensitive or user-specific data inappropriately
❌ Don't ignore query validation
❌ Don't expose internal query structure
❌ Don't skip result formatting

# app/Foundation/Infrastructure/EventDispatcher.php

## Purpose
→ Enhanced event dispatcher for domain events
→ Provides event sourcing and replay capabilities
→ Implements event middleware and filtering
→ Supports synchronous and asynchronous event handling

## Dependencies
use Illuminate\Events\Dispatcher
use Illuminate\Contracts\Queue\Queue
use App\Foundation\Domain\DomainEvent
use App\Events\Contracts\DomainEventInterface

## Methods
dispatch()
fire()
listen()
subscribe()
flush()
forget()
hasListeners()
push()
until()
makeListener()
createQueuedHandlerCallable()
dispatchDomainEvents()
storeDomainEvent()
replayEvents()

## Properties
listeners
wildcards
queueResolver

## Do
✅ Dispatch domain events after aggregate changes
✅ Support both sync and async event handling
✅ Store events for potential replay
✅ Use event middleware for cross-cutting concerns
✅ Handle event failures gracefully

## Don't
❌ Don't dispatch events before aggregate persistence
❌ Don't ignore event handler failures
❌ Don't create circular event dependencies
❌ Don't expose event dispatcher internals
❌ Don't skip event validation

# OpenAPI Specifications: openapi.yaml, users.yaml, courses.yaml, payments.yaml, affiliates.yaml

## Purpose
→ OpenAPI 3.0 specifications for API documentation
→ Defines API endpoints, schemas, and authentication
→ Provides interactive API documentation
→ Supports API client generation and validation

## Dependencies
OpenAPI: 3.0.3
Components: schemas, responses, parameters, examples
Security: bearerAuth, apiKey

## Structures (openapi.yaml)
info
servers
paths
components
security
tags

## Structures (Domain-specific YAML)
paths
components/schemas
components/responses
components/parameters
components/examples

## Do
✅ Keep specifications in sync with actual API
✅ Use consistent naming conventions
✅ Include comprehensive examples
✅ Document all error responses
✅ Version API specifications properly

## Don't
❌ Don't create specifications without validation
❌ Don't ignore breaking changes in specifications
❌ Don't skip security scheme documentation
❌ Don't use inconsistent response formats
❌ Don't omit required field documentation

# app/Api/Documentation/ApiDocGenerator.php

## Purpose
→ Generates API documentation from OpenAPI specifications
→ Creates interactive Swagger UI documentation
→ Supports multiple output formats (HTML, PDF, JSON)
→ Integrates with Laravel routing and validation

## Dependencies
use Illuminate\Support\Facades\Route
use Illuminate\Support\Facades\File
use App\Api\Documentation\OpenApiGenerator
use Symfony\Component\Yaml\Yaml

## Methods
generate()
generateFromRoutes()
generateFromSpecifications()
buildPathsFromRoutes()
extractParametersFromRoute()
generateSchemas()
generateResponses()
validateSpecification()
exportToHtml()
exportToPdf()
exportToJson()

## Do
✅ Generate documentation from actual routes
✅ Validate generated specifications
✅ Support multiple export formats
✅ Include comprehensive examples
✅ Update documentation automatically

## Don't
❌ Don't generate documentation without validation
❌ Don't ignore route parameter types
❌ Don't skip response schema generation
❌ Don't create documentation without examples
❌ Don't forget to update after route changes

# app/Api/Documentation/SwaggerUiController.php

## Purpose
→ Controller for serving interactive Swagger UI documentation
→ Provides API testing interface for developers
→ Supports authentication and authorization testing
→ Integrates with Laravel authentication system

## Dependencies
use Illuminate\Http\Controller
use Illuminate\Http\Request
use Illuminate\Http\Response
use Illuminate\Support\Facades\File
use Illuminate\Support\Facades\View

## Methods
index()
specification()
assets()
authenticate()
validateApiKey()
getSpecificationPath()
renderSwaggerUi()
injectAuthentication()

## Do
✅ Protect documentation in production environments
✅ Support API authentication testing
✅ Provide comprehensive endpoint testing
✅ Include request/response examples
✅ Enable CORS for API testing

## Don't
❌ Don't expose documentation without authentication in production
❌ Don't ignore API versioning in documentation
❌ Don't skip authentication integration
❌ Don't provide incomplete endpoint documentation
❌ Don't forget to secure sensitive endpoints

# app/Api/Documentation/OpenApiGenerator.php

## Purpose
→ Generates OpenAPI specifications from Laravel application
→ Analyzes routes, controllers, and form requests
→ Creates comprehensive API schema definitions
→ Supports automatic documentation generation

## Dependencies
use Illuminate\Support\Facades\Route
use Illuminate\Routing\Router
use ReflectionClass
use ReflectionMethod
use App\Http\Requests\BaseRequest

## Methods
generateSpecification()
analyzeRoutes()
analyzeController()
analyzeFormRequest()
extractParameters()
extractResponses()
generateSchemaFromModel()
generateSchemaFromDto()
buildPathItem()
buildOperation()
buildResponse()
buildSchema()

## Do
✅ Analyze actual Laravel routes and controllers
✅ Extract parameter types from form requests
✅ Generate schemas from models and DTOs
✅ Include comprehensive response examples
✅ Support nested object schemas

## Don't
❌ Don't generate schemas without proper validation
❌ Don't ignore controller method documentation
❌ Don't skip form request validation rules
❌ Don't create incomplete parameter definitions
❌ Don't forget to handle complex data types