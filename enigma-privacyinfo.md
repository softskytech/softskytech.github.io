# Privacy Policy for Enigma

Effective date: March 13, 2026

This Privacy Policy explains how Enigma ("we", "us", "our") collects, uses, and discloses information when you use the Enigma iOS app.

## 1) Scope

This policy applies to the Enigma iOS application and related backend services used to operate credits, subscriptions, and text-processing features.

## 2) Information We Collect

### A) Content You Provide

- Text you enter, paste, or import for AI detection, plagiarism checking, and humanization workflows.
- Files you import (for example TXT or PDF). PDF text extraction is performed on-device first.

### B) Purchase and Subscription Data

- Product identifiers, entitlement status, and transaction events from Apple App Store and RevenueCat.
- Credit grants, deductions, bonus grants/usages, and balance history.

### C) Device and Session Data

- A device-local anonymous app user ID (for example `$RCAnonymousID:...`).
- Device session data (for example hashed device secret, session timestamps).
- Device integrity and attestation data used to prevent abuse (for example App Attest and DeviceCheck-related artifacts/hashes).

### D) Operational and Security Data

- Request IDs, endpoint usage, rate-limit metadata, and service health checks.
- IP address and user-agent may be processed in security logging contexts.
- Technical logs and diagnostics required to secure and operate the service.

### E) Support Data

- If you contact support, we may collect your message and any details you provide (such as support ID and issue context).

## 3) How We Use Information

We use information to:

- Deliver core app features (AI detection, plagiarism checking, humanization workflow, credit accounting).
- Process purchases, renewals, refunds, and restore actions.
- Enforce security, anti-abuse protections, and rate limits.
- Maintain reliable operation and troubleshoot failures.
- Improve product quality and user experience.
- Comply with legal obligations and enforce our terms.

## 4) Sharing and Service Providers

We share relevant data with service providers needed to operate the app:

- Apple (App Store / StoreKit) for billing and subscription processing.
- RevenueCat for subscription/entitlement and purchase webhook orchestration.
- Supabase for backend APIs, database operations, and secure infrastructure.
- Winston AI for AI detection and plagiarism processing requests routed through our backend proxy.

We do not sell your personal information.

## 5) Data Retention

- On-device saved documents and cached data remain on your device until deleted by you or by app removal.
- Credit, purchase, and ledger records are retained on backend systems as needed to provide service integrity, fraud prevention, accounting, and support.
- Session records may be rotated/cleaned according to operational policies.
- Security logs are retained for security monitoring and abuse prevention for a limited period or as required by law.

## 6) Your Choices and Rights

Depending on your region, you may have rights to access, correct, delete, or restrict certain data processing.

You can:

- Delete local app content from within the app where available.
- Cancel subscriptions in Apple settings.
- Request account/data deletion by contacting support.

If requested and verified, we can process deletion of backend account data tied to your app user ID.

## 7) Children

Enigma is not directed to children under 13 (or equivalent minimum age in your jurisdiction), and we do not knowingly collect personal information from children.

## 8) Security

We use technical safeguards including:

- Device attestation and integrity checks (App Attest / DeviceCheck flows).
- Token-based authenticated sessions.
- Signature validation for webhook events.
- Rate limiting and idempotency controls for critical credit operations.
- Access controls for backend data operations.

No system is 100% secure, but we use reasonable measures to protect data.

## 9) International Processing

Your information may be processed in countries other than your own through our service providers' infrastructure. Where required, we apply appropriate safeguards.

## 10) Policy Updates

We may update this Privacy Policy from time to time. We will update the effective date when changes are made.

## 11) Contact

For privacy requests or questions, contact:

- Support Email: `feedback@example.com` (replace with your real support address)

---

## App Store / Production Notes

Before publishing:

1. Host this policy at a public HTTPS URL.
2. Update `AppConstants.URLs.privacyPolicy` in the app to the hosted URL.
3. Ensure App Store Connect privacy disclosures are aligned with this policy.

