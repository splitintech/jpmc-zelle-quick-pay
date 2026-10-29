<p align="center"><img src="docs/brand/splitin-logo.png" alt="SplitIn" width="96" height="96"></p>

# JPMC Zelle Quick Pay

Independent TypeScript and React orchestration for direct-to-recipient payment journeys.
The package keeps browser UI, server state, payment evidence and provider adapters separate.
It is **not an official JPMC SDK**.

## Status and scope

`@splitin/jpmc-zelle-quick-pay@0.1.0-beta.4` is a **synthetic-development prerelease**.
It includes manual banking-app handoff and synthetic adapter flows. It does not include
an approved bank reporting source or a tenant-authorized JPMC initiation adapter.
All JPMC capabilities remain false. A handoff, browser return or accepted submission
is not proof that funds were received.

This standalone migration preserves package names, entry points, migrations and existing
behavior. It does not publish to npm or activate banking capabilities. Use a local build
and packed artifact for development until a registry release is verified.

## Requirements and installation

Use Node.js 22 or later and npm. React UI consumers need React and React DOM 18 or 19.
PostgreSQL is optional for the in-memory demo and required for the reference durable store.

```sh
git clone https://github.com/splitintech/jpmc-zelle-quick-pay.git
cd jpmc-zelle-quick-pay
npm ci
npm run typecheck
npm test
npm run build
npm pack
```

Install the generated tarball in a separate application:

```sh
npm install /absolute/path/splitin-jpmc-zelle-quick-pay-0.1.0-beta.4.tgz
```

## Package entry points

| Import | Responsibility |
| --- | --- |
| `@splitin/jpmc-zelle-quick-pay` | Browser-safe core contracts and journey helpers |
| `@splitin/jpmc-zelle-quick-pay/react` | Payment cards and React journey components |
| `@splitin/jpmc-zelle-quick-pay/styles.css` | Component styles and theme variables |
| `@splitin/jpmc-zelle-quick-pay/server` | Payment engine, stores and authenticated HTTP handler |
| `@splitin/jpmc-zelle-quick-pay/providers/jpmc` | Provider capability interfaces and adapters |
| `@splitin/jpmc-zelle-quick-pay/testing` | Synthetic fixtures and test helpers |

Keep server and testing exports out of browser bundles. Consumers own identity,
authoritative obligations, recipient authority, cross-provider reservations and
idempotent ledger application. Do not substitute a UI event for verified payment evidence.

## What works

- Separate browser-safe core, React UI, server, JPMC capabilities, and testing exports.
- V1 manual banking-app instructions, restore/status refresh, reviewed recipient/amount, persisted allowlisted provider links and supported references.
- Durable authorization-request recovery, canonical obligation aliases, authenticated source exclusion and explicit prepared-payment switching.
- V2 account eligibility, pending consent/challenge, authorization/initiation adapters and synthetic implementation; ambiguous submissions never automatically retry.
- Authenticated receipt ingestion, amount/currency/destination validation, deduplication, review holds, authenticated source corrections, transactional outbox.
- Suggested and confirmed scoped preferences, explicit autopay-conflict resolution, separate receiving-detail and bank-verification adapters.
- Square two-column payment cards, configurable artwork, safe-area sticky launcher and accessible native modal sheet/dialog.
- Postgres reference persistence, versioned migrations, ordered retryable outbox, operational exceptions, runnable Node service and React demo.

**No approved bank reporting source or tenant-authorized JPMC initiation adapter is included.** All JPMC capabilities are false. CQP disbursements are not tenant account initiation. A successful handoff, user return, or accepted submission is not a verified payment.

## Local development

Node 22 or later:

```sh
npm ci
npm run typecheck
npm test
npm run build
SYNTHETIC_DEMO=1 npm run service
# Second terminal
npm run demo
```

Open http://127.0.0.1:4320. The banner and receipt control explicitly identify synthetic data. Switch journey versions only before starting an attempt; restart the service for a fresh in-memory synthetic fixture. No banking credentials are needed. Synthetic endpoints are unavailable in the non-synthetic service.

Optional persistent demo:

```sh
docker compose up -d
DATABASE_URL=postgres://zelle:local-development-only@127.0.0.1:54329/zelle npm run migrate
DATABASE_URL=postgres://zelle:local-development-only@127.0.0.1:54329/zelle SYNTHETIC_DEMO=1 npm run service
```

Use the exact database/user/password in `compose.yaml` if you change it. Set `TEST_DATABASE_URL` to a disposable Postgres database for integration tests; tests create the package schema and delete their test namespace. Never point tests at a production database.

```sh
TEST_DATABASE_URL=postgres://... npm run test:postgres
npx playwright install chromium
npm run test:browser
```

## Consumer exports

```tsx
import { eligiblePurpose } from '@splitin/jpmc-zelle-quick-pay';
import { PaymentMethodCards, ZellePaymentJourney } from '@splitin/jpmc-zelle-quick-pay/react';
import '@splitin/jpmc-zelle-quick-pay/styles.css';
```

Server code imports `PaymentEngine`, `PostgresStore`, `createPaymentHandler`, `signedActorAuthentication` from `/server`. Keep `/server` and `/testing` out of browser bundles. Consumers provide identity, authoritative obligations, recipient authority, cross-provider reservation, and idempotent ledger application. Core contains none of those application's clients or routes.

Provide `imageSrc` per card, or an icon fallback. Theme with `--zqp-accent`, `--zqp-ink`, `--zqp-muted`, `--zqp-bg`, and `--zqp-border`. Add `dark` or `data-theme="dark"` on an ancestor. Opening the sticky button creates/restores a session; it does not send money.

See [contracts](docs/contracts.md), [operations and release gates](docs/operations.md), and [source references](docs/sources.md).

## Architecture and repository layout

The UI creates or restores a journey; server-side orchestration persists attempts and
validates authenticated receipts. Ambiguous submissions are held for reconciliation
instead of automatically retried. Confirmed receipts must match amount, currency and
destination and pass deduplication before the consumer applies a ledger update.

| Directory | Contents |
| --- | --- |
| `src` | Core, React, server, provider and testing entry points |
| `migrations` | Versioned PostgreSQL schema changes |
| `examples/service` | Runnable reference service and migration command |
| `examples/demo` | React synthetic demonstration |
| `tests` | Unit and PostgreSQL behavior checks |
| `scripts` | Browser validation and package/release tooling |
| `docs` | Integration contracts, operations and source references |

## Validation and release

Run the type check, unit tests, build and `npm run check:packed` for a clean installed
consumer check. Use `npm run pack:check` to inspect the package inventory. The CI workflow
also provisions disposable PostgreSQL and runs the browser demo checks. No production
database or bank credentials are needed for these synthetic checks.

Read [operations and release gates](docs/operations.md) before integrating real providers.
Publication requires repository-specific npm trusted publishing and deliberately enabling
`NPM_TRUSTED_PUBLISHING_ENABLED`. Existing versions must not be overwritten or relabeled
as production-ready as part of a repository move.

## Contributing and provenance

See [CONTRIBUTING.md](CONTRIBUTING.md), [migration provenance](docs/migration.md),
[validation record](docs/migration/validation.json), and [release setup](docs/repository-release.md).
Relevant Git history, source inventories, original licenses and artwork are retained.
The migration does not publish packages, configure credentials or deploy infrastructure.

## License

MIT. See [LICENSE](LICENSE).
