# ARCHITECTURE_DECISIONS.md

## 🎯 Architecture Philosophy

This document records key architectural decisions for the Laravel Docu-Course Platform, providing context, reasoning, and consequences for each major design choice. All decisions prioritize **scalability**, **maintainability**, **performance**, and **developer experience**.

## 🏗️ ADR-001: Module-Based Architecture

**ACCEPTED**

**Context:** The platform needs to support multiple domains (courses, users, payments, affiliates) while maintaining clear boundaries and enabling independent development and testing.

**Decision:** We will organize code into modules based on business domains rather than Laravel's default structure. Each module will contain its own models, services, controllers, and tests.

## 🔑 ADR-002: Dual ID Strategy (Auto-increment + UUID)

**ACCEPTED**

**Context:** We need to balance performance requirements (auto-increment IDs for joins and indexing) with security concerns (predictable IDs expose information) and distributed system compatibility (UUIDs for external references).

**Decision:** Every table will have both an auto-incrementing `id` for internal use and a `uuid` for external references and API responses.

## Update: Laravel Breeze & Sanctum Authentication

### 🔐 ADR-011: Authentication Stack Decision

**ACCEPTED**

**Context:** Platform requires both web authentication for course access and API authentication for mobile/third-party integrations with session management and token-based auth.

**Decision:** Implement Laravel Breeze for web authentication (Blade templates) and Laravel Sanctum for API authentication, providing unified user experience across web and API.

**Implementation Stack:**
- **Web Auth:** Laravel Breeze with Blade templates
- **API Auth:** Laravel Sanctum with token abilities
- **2FA:** pragmarx/google2fa-laravel integration
- **Sessions:** Redis-backed secure sessions
- **Permissions:** Spatie Laravel Permission package

### 💳 ADR-012: Payment Gateway Architecture

**ACCEPTED**

**Context:** Platform needs multiple payment methods (Stripe for traditional payments, Binance for crypto) with unified transaction recording and webhook handling.

**Decision:** Implement payment provider abstraction with Stripe and Binance integrations, using single transaction table with polymorphic gateway data.

**Payment Stack:**
- **Traditional:** stripe/stripe-php integration
- **Crypto:** binance/binance-connector-php
- **Currency:** akaunting/laravel-money + florianv/exchanger
- **Processing:** Queue-based async webhook handling

### 📦 ADR-013: Spatie Package Ecosystem

**ACCEPTED**

**Context:** Platform requires extensive functionality for permissions, media handling, activity logging, backups, and query building with proven, maintainable solutions.

**Decision:** Use Spatie package ecosystem for core functionality, providing battle-tested, well-documented solutions with consistent API patterns.

**Spatie Packages Implemented:**
- **spatie/laravel-permission:** Role-based access control
- **spatie/laravel-medialibrary:** File uploads and media management
- **spatie/laravel-activitylog:** User action auditing
- **spatie/laravel-backup:** Automated database/file backups
- **spatie/laravel-query-builder:** API filtering and sorting
- **spatie/laravel-translatable:** Multi-language content

## 💰 ADR-007: Payment Processing Architecture

**ACCEPTED**

**Context:** Platform needs to support multiple payment methods (crypto via Binance, traditional via Stripe) and custom local currencies managed by super-admin.

**Decision:** Implement a payment gateway abstraction with provider-specific implementations and a unified transaction recording system.

## 🎯 ADR-008: Event-Driven Architecture

**ACCEPTED**

**Context:** Platform has complex business rules with multiple side effects: user progression, affiliate commissions, notifications, analytics. Need decoupled event handling.

**Decision:** Implement event-driven architecture with domain events and async listeners for all major business actions.

## ✅ Architecture Decision Summary

These architectural decisions provide a solid foundation for building a scalable, maintainable, and performant Laravel docu-course platform. Each decision balances immediate needs with long-term scalability and developer experience.

