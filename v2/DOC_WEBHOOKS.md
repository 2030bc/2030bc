# DOC_WEBHOOKS.md

Webhooks handle incoming HTTP notifications from external payment providers and services. They ensure secure processing of external events with proper verification, idempotency, and integration with the application's event-driven architecture. Updated.

## app/Webhooks/StripeWebhookHandler.php

**Purpose**: Handles incoming Stripe webhook events for payment processing, subscription management, and dispute handling with complete integration to PaymentService and StripeService.

**Dependencies**:
```php
use App\Services\Payment\StripeService;
use App\Services\Payment\PaymentService;
use App\Models\Transaction;
use App\Events\PaymentSucceeded;
use App\Events\PaymentFailed;
use App\Events\PaymentProcessed;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;
use Stripe\Webhook;
use Stripe\Exception\SignatureVerificationException;
```

**Service Integration**:
- **StripeService Methods Used**: 
  - confirmPayment()
  - retrievePaymentIntent()
  - processRefund()
  - handleWebhook()
- **PaymentService Methods Used**:
  - processPayment()
  - processRefund()
  - getPaymentStatus()

**Methods**:
- handle(Request $request): Response
- verifyWebhookSignature(Request $request): bool
- handlePaymentIntentSucceeded(array $data): void
- handlePaymentIntentFailed(array $data): void
- handleInvoicePaid(array $data): void
- handleCustomerSubscriptionUpdated(array $data): void
- handleDisputeCreated(array $data): void

**Events Triggered**:
- PaymentSucceeded (handled by HandlePaymentSuccess listener)
- PaymentFailed (handled by payment failure listeners)
- PaymentProcessed (handled by ProcessCommissionPayment listener)

**Security Measures**:
- Webhook signature verification using Stripe SDK
- Request timestamp validation
- IP allowlist verification
- Idempotency key validation
- Rate limiting per DOC_MIDDLEWARE.md patterns

**Do**:
- Verify webhook signatures using StripeService::handleWebhook()
- Process webhooks idempotently
- Log webhook processing with SecurityHeaders middleware
- Handle all Stripe event types
- Integrate with PaymentService for transaction updates
- Fire appropriate events for listener processing
- Use RateLimitMiddleware for webhook endpoints

**Don't**:
- Don't process unverified webhooks
- Don't ignore webhook failures
- Don't process webhooks synchronously
- Don't expose webhook processing details
- Don't bypass PaymentService for payment operations

---

## app/Webhooks/BinanceWebhookHandler.php

**Purpose**: Handles Binance Pay webhook notifications for cryptocurrency payment processing and transaction confirmations with full BinanceService integration.

**Dependencies**:
```php
use App\Services\Payment\BinanceService;
use App\Services\Payment\CryptoPaymentService;
use App\Models\Transaction;
use App\Enums\PaymentProvider;
use App\Events\PaymentSucceeded;
use App\Events\PaymentFailed;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;
```

**Service Integration**:
- **BinanceService Methods Used**:
  - checkPaymentStatus()
  - validateTransaction()
  - getTransactionDetails()
  - processWebhook()
- **CryptoPaymentService Methods Used**:
  - processCryptoPayment()
  - validateCryptoTransaction()

**Methods**:
- handle(Request $request): Response
- verifyWebhookSignature(Request $request): bool
- handlePaymentCompleted(array $data): void
- handlePaymentExpired(array $data): void
- handlePaymentCancelled(array $data): void
- processRefund(array $data): void

**Events Triggered**:
- PaymentSucceeded (for completed crypto payments)
- PaymentFailed (for failed/expired crypto payments)
- PaymentProcessed (for successful transaction processing)

**Security Measures**:
- Binance Pay signature verification
- Cryptocurrency transaction validation
- Network fee verification
- Wallet address validation per BinanceService::validateWalletAddress()
- Rate limiting via DOC_MIDDLEWARE.md patterns

**Do**:
- Verify Binance webhook signatures using BinanceService::processWebhook()
- Handle cryptocurrency payment states through BinanceService
- Process refunds using BinanceService::processRefund()
- Log crypto payment events with proper security headers
- Validate transactions using BinanceService::validateTransaction()
- Fire events for affiliate commission processing

**Don't**:
- Don't process unsigned webhooks
- Don't ignore payment state changes
- Don't bypass cryptocurrency validation
- Don't expose crypto payment details
- Don't process without BinanceService integration

---

## app/Webhooks/PayPalWebhookHandler.php

**Purpose**: Processes PayPal webhook events for payment verification, dispute management, and subscription handling with PayPalService integration.

**Dependencies**:
```php
use App\Services\Payment\PayPalService;
use App\Models\Transaction;
use App\Events\PaymentSucceeded;
use App\Events\PaymentRefunded;
use App\Events\PaymentProcessed;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;
use PayPal\Api\WebhookEvent;
```

**Service Integration**:
- **PayPalService Methods Used**:
  - verifyWebhookEvent()
  - processPayment()
  - processRefund()
  - handleDispute()

**Methods**:
- handle(Request $request): Response
- verifyWebhookSignature(Request $request): bool
- handlePaymentCaptureCompleted(array $data): void
- handlePaymentCaptureDenied(array $data): void
- handleBillingSubscriptionActivated(array $data): void
- handleCustomerDisputeCreated(array $data): void

**Events Triggered**:
- PaymentSucceeded (for successful captures)
- PaymentRefunded (for refund processing)
- PaymentProcessed (for completed transactions)

**Security Measures**:
- PayPal webhook signature verification
- Event ID duplication prevention
- Subscription status validation
- Dispute handling integration
- Security headers per DOC_MIDDLEWARE.md

**Do**:
- Verify PayPal webhook authenticity using PayPalService
- Handle payment capture events through PayPalService
- Process subscription changes via service layer
- Manage dispute notifications securely
- Integrate with middleware security patterns
- Fire appropriate payment events

**Don't**:
- Don't trust unverified PayPal webhooks
- Don't ignore subscription status changes
- Don't bypass PayPal dispute handling
- Don't expose PayPal transaction details
- Don't process without PayPalService integration

---

## app/Webhooks/WebhookVerifier.php

**Purpose**: Provides centralized webhook signature verification and security validation for all payment providers, ensuring consistent security across all webhook handlers.

**Dependencies**:
```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\Config;
use App\Exceptions\WebhookVerificationException;
use App\Services\Payment\StripeService;
use App\Services\Payment\BinanceService;
use App\Services\Payment\PayPalService;
```

**Methods**:
- verifyStripeWebhook(Request $request): bool
- verifyBinanceWebhook(Request $request): bool
- verifyPayPalWebhook(Request $request): bool
- validateWebhookTimestamp(Request $request): bool
- getWebhookSecret(string $provider): string
- logVerificationAttempt(Request $request, bool $success): void

**Security Integration**:
- Integrates with SecurityHeaders middleware patterns
- Uses PreventBruteForce middleware for attack detection
- Implements LogSuspiciousActivity for monitoring
- Follows ValidateSignature middleware patterns
- Applies RateLimitMiddleware for protection

**Do**:
- Implement provider-specific verification through service classes
- Validate webhook timestamps per security requirements
- Log verification attempts using security middleware patterns
- Use secure signature comparison methods
- Delegate to appropriate service classes for verification
- Apply middleware security measures consistently

**Don't**:
- Don't skip signature verification
- Don't use weak signature validation
- Don't ignore timestamp validation
- Don't expose webhook secrets
- Don't bypass service layer verification
- Don't ignore security middleware patterns

---

## Webhook Request Validation Integration

### Form Request Validation
Webhook controllers integrate with Form Request classes per DOC_REQUESTS.md patterns:

**StripeWebhookRequest**:
- Validates Stripe webhook signature
- Validates required event data structure
- Implements authorization rules

**BinanceWebhookRequest**:
- Validates Binance Pay signature
- Validates cryptocurrency transaction data
- Implements crypto-specific validation rules

**PayPalWebhookRequest**:
- Validates PayPal event signatures
- Validates subscription and payment data
- Implements PayPal-specific authorization

### Controller Integration
Webhook handlers follow DOC_CONTROLLERS.md patterns:

**Base Webhook Controller**:
- Extends BaseApiController for consistent API responses
- Implements common webhook patterns
- Provides shared security measures
- Handles rate limiting and logging

**Security Middleware Stack**:
- SecurityHeaders: Adds security headers to responses
- RateLimitMiddleware: Prevents webhook abuse
- LogSuspiciousActivity: Monitors for attacks
- ValidateSignature: Validates webhook signatures

---

## Event-Driven Integration

### Webhook Event Flow
1. **Webhook Receipt**: Handler receives and verifies webhook
2. **Service Integration**: Delegates to appropriate service class
3. **Event Firing**: Triggers domain events for business logic
4. **Listener Processing**: Events processed by appropriate listeners
5. **Job Dispatching**: Complex operations queued as jobs

### Event Mapping
| Webhook Source | Events Fired | Listeners Triggered |
|---------------|--------------|-------------------|
| Stripe Payment Success | PaymentSucceeded, PaymentProcessed | HandlePaymentSuccess, ProcessCommissionPayment |
| Binance Payment Complete | PaymentSucceeded, PaymentProcessed | HandlePaymentSuccess, ProcessCommissionPayment |
| PayPal Capture Complete | PaymentSucceeded, PaymentProcessed | HandlePaymentSuccess, ProcessCommissionPayment |
| Stripe Subscription Update | SubscriptionUpdated | UpdateUserAccess |
| PayPal Dispute Created | DisputeCreated | NotifyAdministrators |

### Job Integration
Webhook processing integrates with DOC_JOBS.md patterns:
- **ProcessPayment Job**: Handles complex payment processing
- **GenerateInvoice Job**: Creates invoices for successful payments
- **TrackAffiliateConversion Job**: Processes affiliate commissions
- **SendBulkNotifications Job**: Notifies users of payment status

---

## Testing Alignment

### Webhook Testing Strategy
Per TESTING_GUIDE.md requirements:

**Unit Tests**:
- Test signature verification for each provider
- Test event processing logic
- Test error handling scenarios
- Test service integration points

**Integration Tests**:
- Test webhook handler with actual service calls
- Test event firing and listener processing
- Test database transaction consistency
- Test queue job dispatching

**Security Tests**:
- Test signature verification bypass attempts
- Test rate limiting effectiveness
- Test suspicious activity detection
- Test timestamp validation
