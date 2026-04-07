# Enigma App: Inner Workings and Feature Documentation

Last updated: March 13, 2026

## 1) Product Overview

Enigma is an iOS app for writing-quality workflows with three core tools:

- AI Detection
- Plagiarism Checking
- Humanizer

It uses a credit system, App Store subscriptions via RevenueCat, and a Supabase backend for session security, credit ledgering, and edge-function APIs.

## 2) User-Facing Features

### AI Detection

- Accepts pasted text, typed text, TXT files, and PDF files.
- Returns overall AI score from Winston AI.
- Supports per-sentence AI scoring.
- Tracks analyzed word count for result scope.
- Invalidates per-sentence results when input text is edited after scan.
- Supports rescans and result history save.

### Plagiarism Checker

- Accepts pasted/typed/imported text (TXT/PDF).
- Calls Winston plagiarism endpoint via backend proxy.
- Returns plagiarism percentage, source count, and source list.
- Highlights suspected matched ranges in the text.

### Humanizer

- Supports model options (`Standard`, `Pro`) plus readability and purpose settings.
- Allows user to check AI score on humanized output.
- Current implementation is local mock humanization in-app (not a live paid external humanizer API path).

### Document History

- Saves tool input and output snapshots to local device storage.
- Supports viewing and deleting saved documents.

### Credits and Billing

- Credits are required for paid tool usage.
- Current server-authoritative costs:
  - AI detection: `1` credit/word
  - Plagiarism: `2` credits/word
  - Humanizer: `9` credits/word (cost table is server-defined)
- Subscription and top-up purchase events are handled through App Store + RevenueCat webhook + Supabase RPC grant/deduct flows.
- Credit history view includes earned/used/balance and transaction rows.

### Paywall and Purchases

- Weekly and annual subscription plans displayed via RevenueCat offerings.
- Weekly plan is shown as `3-Day Free Trial` in UI.
- CTA text changes dynamically (`Try for Free` vs `Continue`) based on selected plan.
- Subscriber-only top-up packs are supported.

### Update Notification System

- Remote-config-driven update prompts (`app_update` in app config).
- Optional update overlay + settings banner.
- Reprompt behavior supports once-per-version or interval mode.

### Service Health

- App monitors backend/API status and can show outage/degraded banners.
- Can block actions during certain outage conditions.

## 3) High-Level Architecture

### iOS App (SwiftUI)

- UI layers: tool screens, paywall, settings, history, update overlay.
- Local state/services:
  - `CreditService`
  - `SubscriptionService`
  - `DeviceSessionService`
  - `AppConfigService`
  - `AppUpdateService`
  - `DocumentStore`

### Backend (Supabase)

- Edge Functions (selected):
  - `credits-bootstrap`, `credits-refresh`, `credits-balance`, `credits-deduct`
  - `credits-history`, `credits-restore`
  - `starter-claim`, `starter-spend`
  - `api-winston`
  - `revenuecat-webhook`
  - `app-config`
  - `attest-register`
  - `account-delete`
- Database tables (selected):
  - `credit_balances`, `credit_ledger`
  - `bonus_credit_grants`, `bonus_credit_usages`, `bonus_credit_usage_items`, `bonus_credit_rollbacks`
  - `starter_quotas`, `starter_usage_ledger`
  - `device_sessions`, `attestation_keys`
  - `app_config`

### Third-Party Services

- Apple App Store / StoreKit: purchase and subscription billing.
- RevenueCat: entitlement and webhook event orchestration.
- Winston AI: AI detection + plagiarism processing through a backend proxy.

## 4) Core Runtime Flows

### A) Identity and Session Bootstrapping

1. App reads or creates device-local anonymous app user ID (`$RCAnonymousID:...`) in Keychain.
2. App obtains session token using:
   - App Attest (v2 auth path), and
   - DeviceCheck token validation in production.
3. Backend issues short-lived access JWT + refresh token.

### B) AI Detection Request

1. User submits text.
2. App obtains valid device session token.
3. App calls `api-winston` (endpoint: `ai-detection`, `sentences=true`).
4. Backend enforces rate limits and deducts credits server-side.
5. App renders overall + per-sentence results.

### C) Plagiarism Request

1. User submits text.
2. App calls `api-winston` (endpoint: `plagiarism`).
3. Backend deducts credits server-side.
4. App renders score, sources, and matched ranges.

### D) Subscription Credit Granting

1. Purchase occurs in App Store.
2. RevenueCat sends webhook event to `revenuecat-webhook`.
3. Backend validates webhook secret/signature.
4. Backend grants credits using RPCs with idempotent `request_id`.
5. App refreshes balance/history after purchase.

### E) Weekly Trial Bonus Flow

- Weekly trial start can grant one-time trial bonus credits (configured in remote app config).
- Bonus credits are tracked in dedicated bonus tables and may carry explicit expiry.

## 5) Credit Rules and Current Config Snapshot

From current code defaults and migrations:

- Subscription credits:
  - Weekly: `30,000`
  - Annual: `500,000`
- Weekly trial bonus: `1,500` credits, `3`-day expiry (remote-config-driven).
- Annual signup bonus is currently disabled by config (`0`).
- Purchased subscription/top-up credits are currently granted as non-expiring in webhook flow (`expire_at = null`).

Important: live values can be overridden remotely in `app_config.override` and may differ by environment.

## 6) Security Controls

- App Attest key registration + assertion verification.
- DeviceCheck-based integrity checks in production.
- Device-bound session model (`app_user_id` + `device_secret_hash`).
- Access/refresh JWT separation.
- Webhook signature verification and timing-safe secret comparisons.
- Rate limiting across critical endpoints.
- Server-authoritative credit pricing and deduction.
- Idempotency via request IDs + unique constraints.

## 7) Data Storage Map

### On Device

- Keychain:
  - anonymous app user ID
  - session token / refresh token
  - device secret
  - attestation key identifiers
  - local transaction log cache
- Application Support:
  - saved documents JSON (`saved_documents.json`)
- UserDefaults:
  - app config cache
  - update prompt dismissal state
  - UI flags

### On Backend

- Credit balances, ledger records, bonus grant/usage records.
- Device session and attestation records.
- Starter quota records (if starter/trial quota flow is active).
- Security/rate-limit supporting records and logs.

## 8) Operational Notes

- App has separate development/production environment support for Supabase and RevenueCat keys.
- Privacy policy URL in `AppConstants` is currently a placeholder and should be replaced with hosted policy URL.
- Terms URL points to Apple Standard EULA.
- Account deletion endpoint exists in backend (`account-delete`) and deletes core records for an app user ID.

## 9) Key Source Files

- iOS:
  - `plagiarism/DetectorView.swift`
  - `plagiarism/PlagiarismView.swift`
  - `plagiarism/HumanizerView.swift`
  - `plagiarism/Paywall/PurchaseView.swift`
  - `plagiarism/Services/CreditService.swift`
  - `plagiarism/Services/DeviceSessionService.swift`
  - `plagiarism/Services/AppAttestService.swift`
  - `plagiarism/Services/DocumentStore.swift`
- Backend:
  - `supabase/functions/api-winston/index.ts`
  - `supabase/functions/revenuecat-webhook/index.ts`
  - `supabase/functions/credits-balance/index.ts`
  - `supabase/functions/credits-deduct/index.ts`
  - `supabase/functions/credits-history/index.ts`
  - `supabase/functions/credits-bootstrap/index.ts`
  - `supabase/functions/app-config/index.ts`
  - `supabase/functions/account-delete/index.ts`

