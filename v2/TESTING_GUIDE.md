# 📘 TESTING_GUIDE.md

## 1. Overview

This project follows a **test-first, automation-first** philosophy. Testing ensures production-grade quality, security, and consistency across all services.

### Testing Layers

* **Unit Tests** → Validate small components (DTOs, Value Objects, Services).
* **Feature Tests** → Validate HTTP endpoints, controllers, and workflows.
* **Integration Tests** → Validate communication between subsystems (Payments, Affiliates, Media).
* **Browser Tests (Dusk)** → Validate user journeys in real browser simulation.
* **Performance & Benchmark Tests** → Validate system responsiveness under load.

---

## 2. Tools & Frameworks

* **PHPUnit** → Standard PHP unit & integration tests.
* **Pest** → Expressive, fluent testing framework.
* **Laravel Dusk** → End-to-end browser testing.
* **phpstan** → Static analysis.
* **laravel/pint** → Code style enforcement.
* **Xdebug** + **phpunit-coverage** → Code coverage reports.

---

## 3. Test Data Contracts

Factories and seeders ensure **repeatable, reliable test data**.
Required factories:

* `UserFactory` → supports all roles (admin, instructor, student, affiliate).
* `CourseFactory` → with episodes, categories, and upload variants.
* `PaymentFactory` → with multiple providers (Stripe, Binance).
* `AffiliateFactory` → with referral codes, clicks, and commissions.
* `SystemFactory` → languages, configs, and feature flags.

📌 **Seeder Policy**:

* Each test suite must seed a **minimum viable dataset** (1 user, 1 course, 1 payment, 1 affiliate).
* Advanced cases (refunds, commission chains, caching) use scenario-specific seeders.

---

## 4. Coverage & Quality Rules

* **90%+ coverage required** across Units, Features, Integrations.
* **No skipped tests** allowed in CI.
* **phpstan level 8+** must pass.
* **pint** must be applied (no styling drift).
* **Mutation Testing** recommended for critical services (payments, authentication).

---

## 5. CI/CD Integration

GitHub Actions Workflow:

* Run migrations against `sqlite` or `mysql:8` with Redis service.
* Run static analysis + Pint.
* Run PHPUnit & Pest with coverage.
* Run Dusk tests (headless Chrome).
* Upload coverage report as artifact.

---

## 6. Example Scenarios

* **Authentication Flow** → login, 2FA, social login.
* **API** → `api/v1/courses` returns correct paginated JSON.
* **Payments** → Stripe webhook → invoice generated → receipt notification.
* **Media Library** → upload image → optimize → validate dimensions.
* **Activity Log** → user progress recorded correctly.
* **Permissions** → user cannot access restricted endpoint without role.

---

## 7. Error Recovery & Debugging

* If tests fail after migrations:

  ```bash
  php artisan migrate:fresh --seed --env=testing
  ```
* If Dusk fails:

  * Run locally with `php artisan dusk:chrome-driver --detect`.
  * Use `--browse` flag to watch browser.
* If coverage dips below threshold:

  * Run `php artisan test --coverage-html coverage/` and open report.
* If factories fail:

  * Check relationships and ensure seeders reference correct model classes.
* Log inspection:

  * Always check `storage/logs/laravel.log` for runtime exceptions.

---

## 8. Acceptance Criteria

✅ All tests green in CI/CD.
✅ 90%+ coverage.
✅ Static analysis level 8.
✅ No coding standard violations.
✅ Critical workflows tested end-to-end.

