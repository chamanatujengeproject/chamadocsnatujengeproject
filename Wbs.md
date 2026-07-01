# FUNDLOOP — WORK BREAKDOWN STRUCTURE (WBS)

> Stack: React (web) frontend, Node.js/Express + PostgreSQL backend — matching your Prime Awards Kenya stack.
>
> Sequencing note: since the OpenAPI contract (§ previous doc) already exists, **Frontend can be built against mocked/stubbed responses from that contract while Backend is developed in parallel** — you don't strictly have to wait for a real backend to start UI work. The WBS below is ordered Frontend → Backend per your request, but the dependency column flags where they can overlap.

---

## 1.0 FRONTEND (React Web App)

### 1.1 Project Setup & Architecture
| WBS | Task | Deliverable | Depends On |
|---|---|---|---|
| 1.1.1 | React project scaffold (Next.js or Vite — confirm which), TypeScript config, ESLint/Prettier | Repo skeleton | — |
| 1.1.2 | State management setup (React Query/TanStack Query for server state + Zustand/Context for UI state) | Base architecture | 1.1.1 |
| 1.1.3 | API client layer (typed fetch/axios client generated/aligned to `fundloop-openapi.yaml`) | HTTP client + typed models | OpenAPI spec |
| 1.1.4 | Environment config (dev/staging/prod base URLs, `.env` handling) | Env setup | 1.1.1 |
| 1.1.5 | Design system: Tailwind config/tokens, typography, spacing, reusable component library | Theme + component kit | — |
| 1.1.6 | Routing setup (Next.js App Router / React Router) incl. protected-route wrapper | Router config | 1.1.1 |
| 1.1.7 | Auth token storage strategy (httpOnly cookie vs. secure storage) + CSRF considerations | Storage/session service | 1.1.1 |
| 1.1.8 | Responsive layout shell (nav, sidebar, mobile breakpoints) | App shell | 1.1.5 |

### 1.2 Auth & Onboarding
| WBS | Task | Endpoint(s) Used | Depends On |
|---|---|---|---|
| 1.2.1 | Signup page (phone, name, password) | `POST /auth/signup` | 1.1.3 |
| 1.2.2 | OTP verification page | `POST /auth/verify-phone`, `POST /auth/otp/resend` | 1.2.1 |
| 1.2.3 | Login page | `POST /auth/login` | 1.1.3 |
| 1.2.4 | Forgot / reset password flow | `POST /auth/forgot-password`, `POST /auth/reset-password` | 1.1.3 |
| 1.2.5 | Silent token refresh (interceptor) + auto-redirect to login on expiry | `POST /auth/token/refresh` | 1.2.3 |
| 1.2.6 | Account settings page — change password, logout | `POST /auth/change-password`, `POST /auth/logout` | 1.2.3 |
| 1.2.7 | "Current user" context/provider (roles per chama) | `GET /auth/me` | 1.2.3 |

### 1.3 Membership & Chama Management
| WBS | Task | Endpoint(s) Used | Depends On |
|---|---|---|---|
| 1.3.1 | Create-chama flow (name, contribution amount, rotation method) | `POST /chamas` | 1.2.7 |
| 1.3.2 | Join-via-invitation flow (invite link → landing page → join) | `POST /chamas/{id}/members`, invitation link handling | 1.2.7 |
| 1.3.3 | Invite-member page (Chairman/Admin only) | `POST /chamas/{id}/invitations` | 1.3.1, role-gating (1.3.6) |
| 1.3.4 | Member list/table with status/role filters, search, pagination | `GET /chamas/{id}/members` | 1.2.7 |
| 1.3.5 | Suspend-member action (Chairman/Admin only) | `PATCH /.../suspend` | 1.3.4 |
| 1.3.6 | Role-based UI gating (hook/HOC to hide/disable actions per §1.4 role matrix) | shared utility | 1.2.7 |
| 1.3.7 | Chama switcher (multi-chama membership support in nav) | `GET /auth/me` | 1.2.7 |

### 1.4 ROSCA Module
| WBS | Task | Endpoint(s) Used | Depends On |
|---|---|---|---|
| 1.4.1 | Start-cycle page (Treasurer only) | `POST /chamas/{id}/cycles` | 1.3.6 |
| 1.4.2 | Cycle dashboard — status, deadline, rotation order, progress bar | `GET` cycle detail | 1.4.1 |
| 1.4.3 | Contribution payment page + provider routing (Stripe/PayPal/M-Pesa checkout) | `POST /.../contributions` | 1.1.3 |
| 1.4.4 | Payout release page (Treasurer) + payout notification banner | `POST /.../payout` | 1.4.2 |
| 1.4.5 | Missed-contribution / penalty table | `GET /.../missed` | 1.4.2 |
| 1.4.6 | Position-swap request + voting UI | `POST /.../position-swap` | 1.4.2 |

### 1.5 Welfare Module
| WBS | Task | Endpoint(s) Used | Depends On |
|---|---|---|---|
| 1.5.1 | Submit-claim page (type, amount, evidence file upload) | `POST /.../welfare/claims` | 1.1.3, file upload service |
| 1.5.2 | Claim detail + disbursement status page | `GET /.../claims/{id}` | 1.5.1 |
| 1.5.3 | Voting UI (approve/reject) with live tally | `POST /.../claims/{id}/votes` | 1.5.2 |
| 1.5.4 | Chairman veto action | `POST /.../claims/{id}/veto` | 1.3.6 |

### 1.6 Governance Module
| WBS | Task | Endpoint(s) Used | Depends On |
|---|---|---|---|
| 1.6.1 | Propose rule-change page (Chairman only) | `POST /.../rules/proposals` | 1.3.6 |
| 1.6.2 | Proposal voting page with tally | `POST /.../proposals/{id}/votes` | 1.6.1 |

### 1.7 Finance / Wallet Module
| WBS | Task | Endpoint(s) Used | Depends On |
|---|---|---|---|
| 1.7.1 | Wallet balance + top-up page | `POST /wallets/me/deposits` | 1.1.3 |
| 1.7.2 | Transaction history / ledger table with filters, export | `GET /.../ledger` | 1.7.1 |
| 1.7.3 | Reversal action (Treasurer/Admin) | `POST /.../transactions/{id}/reversal` | 1.3.6 |
| 1.7.4 | Payment provider SDK/checkout integration (Stripe.js, PayPal SDK, M-Pesa STK push) | provider SDKs | 1.7.1 |

### 1.8 Notifications
| WBS | Task | Depends On |
|---|---|---|
| 1.8.1 | Web push notification setup (optional — service worker + FCM/OneSignal) | 1.1.1 |
| 1.8.2 | In-app notification center (event feed: contributions, payouts, claims, votes) | 1.1.3 |
| 1.8.3 | Real-time updates via polling or WebSocket/SSE for live tallies and dashboards | 1.1.3 |
| 1.8.4 | SMS-fallback awareness (read-only status, since SMS is server-sent) | — |

### 1.9 Frontend Testing & QA
| WBS | Task | Depends On |
|---|---|---|
| 1.9.1 | Component tests (Vitest/Jest + React Testing Library) for critical flows | modules 1.4–1.7 |
| 1.9.2 | Integration/E2E tests (Playwright/Cypress) against mocked API contract | 1.1.3 |
| 1.9.3 | Manual QA pass — role-permission edge cases (per §1.4 matrix) | all modules |
| 1.9.4 | Cross-browser + responsive layout pass (desktop/tablet/mobile web) | all modules |
| 1.9.5 | Accessibility audit (WCAG — forms, tables, focus states) | all modules |

### 1.10 Frontend Release
| WBS | Task | Depends On |
|---|---|---|
| 1.10.1 | Hosting setup (Vercel/Netlify or equivalent) + custom domain | 1.1.1 |
| 1.10.2 | Staging deploy pointed at staging API | Backend staging environment (§2.11) |
| 1.10.3 | SEO/meta basics, analytics wiring (if public-facing marketing pages exist) | 1.10.1 |
| 1.10.4 | Production release | Backend production environment |

---

## 2.0 BACKEND (Node.js / Express + PostgreSQL)

### 2.1 Project Setup & Infrastructure
| WBS | Task | Depends On |
|---|---|---|
| 2.1.1 | Repo scaffold (Express, TS config, ESLint/Prettier) | — |
| 2.1.2 | Environment/config management (dev/staging/prod) | 2.1.1 |
| 2.1.3 | Docker/local dev environment (Postgres, Redis) | 2.1.1 |
| 2.1.4 | CI pipeline (lint, test, build) | 2.1.1 |
| 2.1.5 | Logging & error-tracking setup | 2.1.1 |

### 2.2 Database Schema
| WBS | Task | Depends On |
|---|---|---|
| 2.2.1 | Finalize DB Schema doc (next in your doc sequence) | Business Rules + Workflow docs (done) |
| 2.2.2 | Migrations: Users, Chamas, Memberships, Invitations | 2.2.1 |
| 2.2.3 | Migrations: Cycles, ContributionRecords, Payouts, RotationPositions | 2.2.1 |
| 2.2.4 | Migrations: Claims, Votes, Disbursements | 2.2.1 |
| 2.2.5 | Migrations: Wallets, Transactions, LedgerEntries | 2.2.1 |
| 2.2.6 | Migrations: RuleProposals, AuditLog, Webhooks | 2.2.1 |
| 2.2.7 | Seed scripts for local/dev testing | 2.2.2–2.2.6 |

### 2.3 Auth Service
| WBS | Task | Endpoint(s) | Depends On |
|---|---|---|---|
| 2.3.1 | Signup + password hashing | `/auth/signup` | 2.2.2 |
| 2.3.2 | OTP generation/verification (SMS provider integration) | `/auth/verify-phone`, `/auth/otp/resend` | 2.3.1 |
| 2.3.3 | Login + JWT issuance | `/auth/login` | 2.3.1 |
| 2.3.4 | Refresh token rotation & revocation | `/auth/token/refresh`, `/auth/logout` | 2.3.3 |
| 2.3.5 | Forgot/reset password flow | `/auth/forgot-password`, `/auth/reset-password` | 2.3.1 |
| 2.3.6 | Change password | `/auth/change-password` | 2.3.3 |
| 2.3.7 | Current-user + roles-per-chama resolver | `/auth/me` | 2.4.x |
| 2.3.8 | Rate limiting on auth endpoints (brute force / OTP abuse) | all above | 2.3.1–2.3.6 |

### 2.4 Membership Service
| WBS | Task | Endpoint(s) | Depends On |
|---|---|---|---|
| 2.4.1 | Create chama + auto-assign Chairman/Treasurer roles | `POST /chamas` | 2.2.2 |
| 2.4.2 | Invitation issuance + expiry | `POST /.../invitations` | 2.4.1 |
| 2.4.3 | Join-via-invitation, uniqueness constraint (BR-MEM-002) | `POST /.../members` | 2.4.2 |
| 2.4.4 | Member list + filters | `GET /.../members` | 2.4.1 |
| 2.4.5 | Suspend member + cascade events (ROSCA/Welfare freeze) | `PATCH /.../suspend` | 2.4.1, event bus (2.10) |
| 2.4.6 | Role/permission middleware (enforces §1.4 matrix on every route) | all Role-restricted endpoints | 2.3.3 |

### 2.5 ROSCA Service
| WBS | Task | Endpoint(s) | Depends On |
|---|---|---|---|
| 2.5.1 | Start cycle + rotation order assignment (fixed/lottery) | `POST /.../cycles` | 2.4.6 |
| 2.5.2 | Contribution submission + idempotency + validation chain | `POST /.../contributions` | 2.6 (Finance atomic transfer) |
| 2.5.3 | Auto payout-trigger check on full contribution | internal | 2.5.2 |
| 2.5.4 | Payout release + recipient resolution (skip suspended/ineligible) | `POST /.../payout` | 2.5.3, 2.6 |
| 2.5.5 | Missed-contribution scheduler job + penalty application | `GET /.../missed` | 2.5.1 |
| 2.5.6 | Position-swap vote workflow | `POST /.../position-swap` | 2.8 (Governance vote engine) |

### 2.6 Finance Service
| WBS | Task | Endpoint(s) | Depends On |
|---|---|---|---|
| 2.6.1 | Wallet model + atomic debit/credit transfer primitive (BR-FIN-004) | shared internal service | 2.2.5 |
| 2.6.2 | Ledger entry writer (BR-FIN-005) | internal | 2.6.1 |
| 2.6.3 | Idempotency key middleware (BR-FIN-003) | all money endpoints | 2.6.1 |
| 2.6.4 | Deposit flow + payment gateway integration (M-Pesa/Stripe/PayPal) | `POST /wallets/me/deposits` | 2.6.1 |
| 2.6.5 | Gateway webhook handler (async payment confirmation) | internal | 2.6.4 |
| 2.6.6 | Transaction reversal (immutable + offsetting entry) | `POST /.../reversal` | 2.6.1, 2.4.6 |
| 2.6.7 | Ledger read/query endpoint with filters | `GET /.../ledger` | 2.6.2 |

### 2.7 Welfare Service
| WBS | Task | Endpoint(s) | Depends On |
|---|---|---|---|
| 2.7.1 | Claim submission + validation chain (type, evidence, 30% cap, one-active) | `POST /.../claims` | 2.4.6 |
| 2.7.2 | Vote recording + 70% tally logic | `POST /.../claims/{id}/votes` | 2.8 |
| 2.7.3 | Chairman veto | `POST /.../claims/{id}/veto` | 2.4.6 |
| 2.7.4 | Disbursement trigger on approval + insufficient-funds fallback | internal → `GET /.../claims/{id}` | 2.6.1 |

### 2.8 Governance Service
| WBS | Task | Endpoint(s) | Depends On |
|---|---|---|---|
| 2.8.1 | Generic vote engine (threshold config: 50%+1, 70%, 2/3) reused by Welfare/ROSCA/Governance | shared internal | 2.2.6 |
| 2.8.2 | Rule proposal creation | `POST /.../rules/proposals` | 2.8.1, 2.4.6 |
| 2.8.3 | Proposal voting + rule value update on majority | `POST /.../proposals/{id}/votes` | 2.8.2 |
| 2.8.4 | Rule-reload broadcast to dependent domains on `RuleChanged` | internal | 2.10 |

### 2.9 Notification Service
| WBS | Task | Depends On |
|---|---|---|
| 2.9.1 | SMS provider integration (M-Pesa-market SMS gateway) | 2.1.2 |
| 2.9.2 | Push notification integration (FCM) | 2.1.2 |
| 2.9.3 | Event-triggered notification templates (join, suspend, contribute, payout, claim, etc.) | 2.10 |

### 2.10 Event Bus & Audit
| WBS | Task | Depends On |
|---|---|---|
| 2.10.1 | Internal event bus (pub/sub between domains, per Cross-Domain Event Map) | 2.1.3 (Redis) |
| 2.10.2 | Audit log writer — subscribes to all domain events | 2.10.1 |
| 2.10.3 | Outbound webhook dispatcher + HMAC signing + retry/backoff | 2.10.1 |
| 2.10.4 | Webhook subscription management endpoint | `POST /.../webhooks` | 2.4.6 |

### 2.11 Backend Testing, DevOps & Release
| WBS | Task | Depends On |
|---|---|---|
| 2.11.1 | Unit tests per service (Auth, Membership, ROSCA, Welfare, Governance, Finance) | each service module |
| 2.11.2 | Integration tests against OpenAPI contract (contract testing) | fundloop-openapi.yaml |
| 2.11.3 | Load testing on contribution/payout endpoints (concurrency on atomic transfers) | 2.6.1 |
| 2.11.4 | Staging environment deploy | 2.1.3, 2.1.4 |
| 2.11.5 | Production environment deploy + monitoring/alerts | 2.11.4 |

---

## 3.0 CROSS-CUTTING MILESTONES

| Milestone | Marks | Unblocks |
|---|---|---|
| M1 — API contract frozen | `fundloop-openapi.yaml` finalized | Frontend can build against real shapes; Backend can implement to spec |
| M2 — Auth service live (staging) | 2.3.x complete | Frontend 1.2.x can integrate against real auth |
| M3 — Finance primitives live (staging) | 2.6.1–2.6.3 complete | ROSCA/Welfare backend work (2.5, 2.7) can integrate real money movement |
| M4 — Core domains feature-complete (staging) | 2.4–2.8 complete | Full frontend integration testing (1.9.2) |
| M5 — End-to-end staging pass | Frontend + Backend integrated on staging | Production release (1.10.4, 2.11.5) |

---

*Document version 1.0 — FundLoop Platform*
*Companion to: Domain Model → Business Rules → Workflows → API Design → (this) WBS → DB Schema*
