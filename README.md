# Repetz

Production SaaS for pet shop management, combining scheduling, customer and pet records, point of sale, inventory, recurring service plans, financial analytics, public booking, and customer communication.

I co-founded Repetz and built the platform end to end, from product decisions and interface design to backend services, billing, integrations, testing, and production operations.

[Live product](https://repetz.com.br) · [LinkedIn](https://linkedin.com/in/lucas-da-silva-santos-a46879285)

> Repetz serves active customers in production. The source repository is private, so this case study describes the product and engineering decisions without exposing customer or operational data.

---

## Product Overview

Pet shops often coordinate appointments, customer history, payments, inventory, recurring packages, and client communication across disconnected tools.

Repetz brings those workflows into one web application. Staff use an authenticated operational dashboard, while pet owners interact through public booking, shared reports, and a customer portal.

The current account model is intentionally simple: one operator account owns one business unit. Tenant boundaries are still explicit in the database and application services, without prematurely introducing multi-location or multi-operator complexity.

---

## Main Features

### Scheduling

The scheduling module supports:

- Calendar views by date and professional.
- Appointment creation, editing, rescheduling, cancellation, and completion.
- Recurring appointments.
- Professional availability.
- Configurable operating hours.
- Controlled schedule overrides.
- Appointment status transitions.
- Internal notes and service details.
- Grooming reports with photos.
- Audited status changes.

The internal calendar and public-booking flow use the same server-side scheduling rules instead of maintaining separate availability models.

### Public Booking

Each business can configure a public booking link under its own slug.

Customers can:

- Identify themselves using existing contact information.
- Select an eligible pet.
- Choose services and add-ons.
- Select an available professional and time.
- Submit an appointment request without accessing the internal dashboard.

The public flow applies unit scoping, abuse throttling, conflict checks, and the same operating window used by internal scheduling.

### Customer Portal

The customer portal provides controlled external access to information connected to a pet owner.

It includes:

- One-time-code authentication.
- Pet and appointment information.
- Public appointment consultation.
- Cancellation where permitted.
- Neutral responses for sensitive identity flows.

Portal tokens have an explicit lifetime and are signed with the application's session secret.

### Customer and Pet Records

The platform maintains a complete relationship between owners, pets, appointments, services, and payments.

Records can include:

- Contact and address information.
- Pet identification and photos.
- Allergies and health information.
- Grooming preferences.
- Behavioral notes.
- Medical history.
- Appointment timeline.
- Purchase and payment history.
- Follow-up activity.

Client data can also be imported in batches through a validated administrative workflow.

### Services and Professionals

Administrators can configure:

- Service catalog.
- Duration and pricing.
- Pet-size variations.
- Optional service add-ons.
- Professional profiles.
- Availability.
- Commission rules.

Those settings feed the agenda, public booking, point of sale, reports, and recurring plans.

### Point of Sale and Cashier

The POS module combines service and product sales with operational cashier control.

It supports:

- Cashier-shift opening and closing.
- Product and service items.
- Multiple payment methods.
- Split payments.
- Pending sales.
- Payment completion.
- Customer and appointment association.
- Inventory deduction.
- Sale history and detail views.
- Commission reporting.

Sale creation validates tenant ownership and preserves consistency between payments, product items, appointments, follow-ups, inventory, and customer history.

### Inventory

Product management includes:

- Product catalog.
- Current stock.
- Stock adjustments.
- Low-stock alerts.
- Expiration tracking.
- Product use in POS transactions.
- Tenant-scoped movement history.

### Recurring Service Plans

Pet shops can create recurring service packages associated with a pet and service.

The module tracks:

- Active and expired plans.
- Included and consumed services.
- Remaining usage.
- Payment association.
- Renewal and reactivation.
- Usage alerts.
- POS integration.

### Finance

The financial area consolidates operational data from appointments, payments, sales, and expenses.

It provides:

- Revenue and expense summaries.
- Cash-flow views.
- Paid and pending amounts.
- Recurring expenses.
- Period filters.
- Service and product breakdowns.
- Cashier information.
- Commission reports.
- Billing and operational exports.

Date filters are implemented as explicit PostgreSQL intervals, protecting reports from losing records later in the selected end date because of implicit timestamp casts.

### AI-Assisted Business Summary

Operators can generate a written analysis from their own consolidated business metrics.

The AI flow:

- Builds a structured financial and operational summary on the server.
- Sends only the required metrics through the configured AI gateway.
- Produces a narrative with observations and recommendations tied to those numbers.
- Requires an active subscription.
- Applies account cooldown and daily usage limits.
- Stores enough state to enforce generation rules consistently.

The model does not query the production database directly. Application services decide which metrics are included before the request is made.

### Communication Automation

Repetz integrates with the Meta WhatsApp Cloud API and transactional email services.

Automations cover:

- Appointment confirmations.
- Scheduled reminders.
- Pet-ready notifications.
- Birthday communication.
- Inactive-customer follow-ups.
- Trial and subscription notifications.
- Account and password-recovery emails.

WhatsApp messages are queued and processed by protected scheduled routes rather than being sent only from the browser request that triggered the event.

### Subscription Management

The SaaS billing layer supports trials, checkout, subscription state, cancellation, reactivation, and customer billing management.

Stripe and Pagar.me integrations coexist behind a normalized subscription model. The stored subscription identifies which provider owns its lifecycle, while server-side guards enforce paid access independently of the frontend navigation.

---

## Engineering Highlights

### Explicit Tenant and Ownership Boundaries

Every business query is scoped by the authenticated account and, where available, its unit identifier. An external record identifier is never sufficient by itself; application services validate that the record belongs to the active tenant.

```text
Authenticated request
        |
        v
Resolve user and unit context
        |
        +---- verify active subscription when required
        +---- validate record ownership
        +---- enforce domain rules
        |
        v
Parameterized PostgreSQL query
```

The current product does not pretend to support multiple units per operator. The data boundaries are explicit while the user model remains proportional to the product's present needs.

### Scheduling Concurrency

Appointment creation is vulnerable to races: two browser requests can observe the same available time before either writes it.

Repetz combines:

- Redis-backed short-lived locks for coordination.
- Idempotency protection for repeated submissions.
- Server-side availability checks.
- PostgreSQL constraints and final conflict validation.
- Explicit handling for authorized schedule overrides.

Redis reduces duplicate work, but PostgreSQL remains the final authority for appointment integrity.

### Protected Public Surfaces

The application classifies endpoints as public, authenticated, scheduled, webhook, or internal.

This matters in Next.js because middleware does not automatically secure every API route. Public booking, contact, portal authentication, webhooks, and scheduled routes each implement the guard appropriate to their surface:

- Rate limit or throttle.
- Neutral response where identity disclosure matters.
- Explicit secret or signature verification.
- Unit and ownership scoping.
- Server-side input validation.
- Structured observability.

### Server-Side Subscription Enforcement

Paid access is enforced by a shared server-side guard used by sensitive reads and mutations.

The guard evaluates the normalized subscription state, including active trials and paid periods, instead of relying on hidden buttons or client redirects. Billing actions and webhooks remain separate because they must still be reachable when a customer needs to recover or manage access.

### Multi-Provider Billing

Stripe and Pagar.me have different checkout, cancellation, reactivation, and webhook contracts.

The application normalizes their result into a shared subscription record while keeping provider-specific clients isolated. The stored payment provider routes future lifecycle operations to the correct integration.

Stripe events use a database-backed idempotency record. Provider credentials and webhook secrets remain server-side, and critical webhooks declare a Node-compatible runtime.

### POS Consistency Across Modules

A sale can affect several records: payment, product items, appointment status, stock, follow-up state, customer history, and recurring plans.

The POS service treats those effects as one workflow. Validation happens before the final result is returned, and compensating behavior protects consistency when a later step fails.

### Direct PostgreSQL Access

The application uses the Neon serverless driver with parameterized tagged-template SQL rather than an ORM.

This keeps queries explicit and makes tenant filters visible during review. Shared helpers establish authentication, tenant, and subscription context before domain queries execute.

Independent reads are parallelized where it reduces round trips without obscuring the contract.

### Image Delivery and Storage Controls

Pet photos, business logos, and report images use Vercel Blob, but raw Blob URLs are not served directly throughout the interface.

A shared image component routes remote assets through Next.js image optimization, handles temporary browser previews, and provides an accessible fallback for unavailable files. Report-photo and pet-edit flows compress photographs before sending them, and storage prefixes distinguish permanent assets from disposable report images.

The design addresses both storage and transfer limits: repeated delivery of large originals can be more expensive and fragile than the stored file volume itself.

### Stable Facades for a Growing Codebase

Large domain actions are being split conservatively. Existing public modules remain stable facades while internal submodules are organized by responsibility.

For example, scheduling, finance, sales, settings, and authentication preserve their external imports while queries, mutations, summaries, and shared helpers move into focused internal files.

This makes structural improvement possible without combining a refactor with an accidental contract change.

---

## Architecture

```text
Marketing and public pages
Authenticated dashboard
Public booking and customer portal
          |
          v
Next.js App Router
          |
          +---- Server Components for route composition and reads
          +---- Server Actions for authenticated domain operations
          +---- Route Handlers for webhooks, public APIs, and scheduled jobs
          |
          v
Authentication, tenant, ownership, and subscription guards
          |
          v
Domain services and parameterized SQL
          |
          v
Neon PostgreSQL
          |
          +---- operational records
          +---- subscription state
          +---- audit history
          +---- idempotency records
```

External services are isolated behind server-side integrations:

```text
Application services
      |
      +---- Stripe / Pagar.me        SaaS billing
      +---- Meta Cloud API           WhatsApp automation
      +---- Resend                   transactional email
      +---- Upstash Redis            rate limits and short locks
      +---- Vercel AI Gateway        business summaries
      +---- Vercel Blob              images and reports
```

Static marketing pages are CDN-compatible. Authenticated, public-booking, portal, and onboarding route groups declare their runtime and caching behavior independently.

---

## Technology Stack

| Layer | Technology |
|---|---|
| Framework | Next.js 15, App Router |
| UI | React 19, TypeScript |
| Styling | Tailwind CSS 4, shadcn/ui, Radix UI |
| Database | PostgreSQL, Neon serverless driver |
| Authentication | Custom database sessions, bcrypt, `httpOnly` cookies |
| Billing | Stripe, Pagar.me |
| Distributed coordination | Upstash Redis |
| Email | Resend |
| WhatsApp | Meta Cloud API |
| AI | Vercel AI SDK, Vercel AI Gateway |
| File storage | Vercel Blob, Next.js Image Optimization |
| Unit and component testing | Jest multi-project, Testing Library |
| Browser testing | Playwright |
| Hosting | Vercel |
| Package manager | pnpm |

---

## Security and Data Integrity

- Passwords are hashed with bcrypt.
- Sessions are stored in PostgreSQL and referenced through `httpOnly` cookies.
- Email identity is normalized before comparison.
- Password-reset and verification tokens have explicit expiry rules.
- Business queries require tenant scoping.
- Record identifiers from the browser require ownership validation.
- Paid operations enforce subscription state on the server.
- Public, authenticated, webhook, scheduled, and internal surfaces use different explicit guards.
- Stripe webhook signatures and Pagar.me webhook credentials are verified server-side.
- Stripe and Pagar.me event identifiers are recorded in PostgreSQL to reject duplicate delivery.
- Redis is used for rate limiting, OTP control, throttling, and short-lived locks.
- PostgreSQL remains the final integrity boundary when Redis is unavailable.
- SQL values are passed through parameterized tagged templates.
- Destructive UI actions use confirmation flows.
- Image-delivery paths restrict remote hosts and use optimized rendering; selected photo flows also apply client-side compression and storage-retention rules.

Some public flows use a documented fail-soft rate-limit policy when Redis is unavailable. Those fallbacks do not replace database constraints or ownership checks.

---

## Testing and Quality Gates

The automated suite is divided by runtime and risk.

### Server-Side Tests

The Jest `node` project covers:

- Authentication and session behavior.
- Tenant and ownership isolation.
- Subscription guards.
- Scheduling conflicts and distributed locks.
- Public booking and customer portal security.
- Clients, pets, services, and professionals.
- POS consistency and stock updates.
- Recurring service plans.
- Financial intervals and summaries.
- Stripe and Pagar.me billing.
- Webhook authentication and duplicate-event handling.
- Redis fallbacks.
- WhatsApp automation.
- AI-summary usage rules.
- Public and scheduled API surfaces.

### Component Tests

The Jest `jsdom` project covers user interactions and component contracts where browser behavior matters.

### Browser Workflows

Playwright exercises selected end-to-end flows in a real browser, complementing the faster server and component suites.

### Coverage Policy

The repository enforces global Jest minimums of:

| Metric | Minimum |
|---|---:|
| Statements | 65% |
| Lines | 65% |
| Branches | 55% |
| Functions | 55% |

The standard verification commands are:

```bash
pnpm lint
pnpm typecheck
pnpm test:coverage:local
pnpm test:e2e
pnpm build
```

The exact set is selected according to the risk of the change. Authentication, billing, middleware, public routes, runtime configuration, and database contracts receive focused checks in addition to the general gates.

---

## Deployment

Repetz runs on Vercel with Neon PostgreSQL, Upstash Redis, Vercel Blob, and server-side integrations configured through environment variables.

Database DDL is consolidated in an idempotent production migration file and is never executed implicitly during the application build. Schema changes and application deployments remain separate, reviewable operations.

Live product: [repetz.com.br](https://repetz.com.br)

---

## Source Access

The production source repository is private because Repetz is an active commercial product.

This showcase intentionally excludes:

- Customer and pet information.
- Credentials and integration secrets.
- Production database contents.
- Internal pricing and commercial data.
- Operational backups.
- Administrative access.

Architecture and implementation details can be discussed during an interview without exposing customer data or proprietary source code.

---

## Author

**Lucas da Silva Santos** — Full Stack Developer and Repetz Co-founder

[LinkedIn](https://linkedin.com/in/lucas-da-silva-santos-a46879285) · [repetz.com.br](https://repetz.com.br)
