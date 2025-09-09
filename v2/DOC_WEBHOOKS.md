# DOC_WEBHOOKS.md

## File: 2030b/app/Webhooks/StripeWebhookHandler.php

**Purpose**: Handles incoming Stripe webhook events for payment processing, subscription management, and dispute handling.

**Dependencies**:
```php
use App\Services\Payment\StripeService;
use App\Services\Payment\PaymentService;
use App\Models\Transaction;
use App\Events\PaymentSucceeded;
use App\Events\PaymentFailed;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;
use Stripe\Webhook;
use Stripe\Exception\SignatureVerificationException;
```

**Methods**:
- handle(Request $request): Response
- verifyWebhookSignature(Request $request): bool
- handlePaymentIntentSucceeded(array $data): void
- handlePaymentIntentFailed(array $data): void
- handleInvoicePaid(array $data): void
- handleCustomerSubscriptionUpdated(array $data): void
- handleDisputeCreated(array $data): void

**Do**:
- Verify webhook signatures
- Process webhooks idempotently
- Log webhook processing
- Handle all Stripe event types

**Don't**:
- Don't process unverified webhooks
- Don't ignore webhook failures
- Don't process webhooks synchronously
- Don't expose webhook processing details

## File: 2030b/app/Webhooks/BinanceWebhookHandler.php

**Purpose**: Handles Binance Pay webhook notifications for cryptocurrency payment processing and transaction confirmations.

**Dependencies**:
```php
use App\Services\Payment\BinanceService;
use App\Services\Payment\CryptoPaymentService;
use App\Models\Transaction;
use App\Enums\PaymentProvider;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;
```

**Methods**:
- handle(Request $request): Response
- verifyWebhookSignature(Request $request): bool
- handlePaymentCompleted(array $data): void
- handlePaymentExpired(array $data): void
- handlePaymentCancelled(array $data): void
- processRefund(array $data): void

**Do**:
- Verify Binance webhook signatures
- Handle cryptocurrency payment states
- Process refunds appropriately
- Log crypto payment events

**Don't**:
- Don't process unsigned webhooks
- Don't ignore payment state changes
- Don't bypass cryptocurrency validation
- Don't expose crypto payment details

## File: 2030b/app/Webhooks/PayPalWebhookHandler.php

**Purpose**: Processes PayPal webhook events for payment verification, dispute management, and subscription handling.

**Dependencies**:
```php
use App\Services\Payment\PayPalService;
use App\Models\Transaction;
use App\Events\PaymentSucceeded;
use App\Events\PaymentRefunded;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;
use PayPal\Api\WebhookEvent;
```

**Methods**:
- handle(Request $request): Response
- verifyWebhookSignature(Request $request): bool
- handlePaymentCaptureCompleted(array $data): void
- handlePaymentCaptureDenied(array $data): void
- handleBillingSubscriptionActivated(array $data): void
- handleCustomerDisputeCreated(array $data): void

**Do**:
- Verify PayPal webhook authenticity
- Handle payment capture events
- Process subscription changes
- Manage dispute notifications

**Don't**:
- Don't trust unverified PayPal webhooks
- Don't ignore subscription status changes
- Don't bypass PayPal dispute handling
- Don't expose PayPal transaction details

## File: 2030b/app/Webhooks/WebhookVerifier.php

**Purpose**: Provides centralized webhook signature verification and security validation for all payment providers.

**Dependencies**:
```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\Config;
use App\Exceptions\WebhookVerificationException;
```

**Methods**:
- verifyStripeWebhook(Request $request): bool
- verifyBinanceWebhook(Request $request): bool
- verifyPayPalWebhook(Request $request): bool
- validateWebhookTimestamp(Request $request): bool
- getWebhookSecret(string $provider): string
- logVerificationAttempt(Request $request, bool $success): void

**Do**:
- Implement provider-specific verification
- Validate webhook timestamps
- Log verification attempts
- Use secure signature comparison

**Don't**:
- Don't skip signature verification
- Don't use weak signature validation
- Don't ignore timestamp validation
- Don't expose webhook secrets
