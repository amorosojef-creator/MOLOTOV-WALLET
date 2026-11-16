# What was actually run (2026-11-16, Linux sandbox, Node 22.23.4, PostgreSQL 16.15)

| Check | Command | Result |
|---|---|---|
| Backend automated tests (real Postgres) | `cd backend && npm test` | **28 / 28 pass** — two-account transfer, idempotent retry, 25 parallel sends from a ₱100 wallet (exactly 10 succeed), 12 parallel retries of one key → one transfer, crossing A↔B transfers (no deadlock, money conserved), insufficient funds, wrong/locked PIN, limits, DB rejects unbalanced/mutated journal, token forgery/expiry/refresh-reuse, QR Ph CRC + tamper, live endpoint disabled, unsafe config refused |
| Live HTTP smoke (running server, 2 fresh users) | `npm run smoke` | **all 12 checks pass**, against both dev mode and a simulated production container run |
| Mobile typecheck (strict) | `cd mobile && npx tsc --noEmit` | pass |
| Mobile unit tests | `npm test` | 4 / 4 pass |
| Native bundles | `npx expo export --platform ios` / `android` / `web` | all three compile |
| **Browser UI end-to-end** (real web build ↔ real backend, 2 users) | `npm run e2e` | **6 / 6 pass**: register ×2 → add funds → send ₱1,250.50 with PIN → success + receipt → recipient sees it → balance ₱3,749.50 after reload → search → QR Ph shown as blocked |

## NOT verified (be honest with yourself)
- **Docker image build and Render blueprint** — no Docker daemon here.
- **Any real iPhone/Android device**: camera scanning, Face ID, haptics, native animations feel. Web run used reduced-motion.
- **EAS cloud build / IPA** — requires your Expo + Apple accounts. No IPA exists.
- `expo-doctor`: 18/20 checks passed; 2 could not run (no access to Expo's servers from my sandbox).
- Any public HTTPS deployment. Any real-money flow (impossible without provider credentials).

To reproduce the browser test: `cd mobile && EXPO_PUBLIC_API_URL=http://localhost:4000 npx expo export --platform web --output-dir dist-web`, serve `dist-web` on :8099, start the backend with `CORS_ORIGIN=*`, install Playwright (`npm i -D playwright && npx playwright install chromium`), then `npm run e2e`.
