# Friday — Master Plan
Status: reconstructed from supplied handoff.
Version: 0.1-reconstruction

## 1. Mission
Build Friday, a local-first AI assistant with web, mobile and API surfaces. Deliver seven gated milestones in strict order. Security and trust precede capabilities.

## 2. Milestone overview
| Milestone | Name | Tickets | Outcome |
|---|---|---:|---|
| M0 | Foundation | 8 | Bootable monorepo, Compose, migrations, CI, health |
| M1 | Trust layer | 12 | Auth, RBAC, audit, Vault, registry, approval |
| M2 | Brain | 14 | Orchestrator, chat, tools, dashboard, budget, kill |
| M3 | Domains | 12 | News, panchang, email, data |
| M4 | Mobile + Voice | 10 | Expo, Tailscale, push, gated voice |
| M5 | Cyber | 8 | Monitoring, scans, incident draft, audit, backups |
| M6 | Trading + Build + Launch | 14 | Paper trading, generators, E2E, deploy, readiness |
| Total | | 78 | |

## 3. Build order and rationale
1. M0 establishes reproducible repository, service skeletons, migrations, CI and health.
2. M1 establishes authentication, authorization, audit, secrets, tool permissions and approval enforcement. It is mandatory.
3. M2 adds orchestration and tools only after trust controls exist.
4. M3 adds external domains and untrusted inputs using the trust layer.
5. M4 adds mobile and optional voice while preserving private networking and text path.
6. M5 adds defensive monitoring, scanning, audit verification, backup restore and retention.
7. M6 adds paper trading and launch work only after prior milestones and safety checks.

## 4. Milestone plans

### M0 — Foundation (8)
Order: M0-1 pnpm + Turbo monorepo; M0-2 Python skeleton (uv); M0-3 Compose (postgres, redis, api, web); M0-4 PostgreSQL bootstrap + Alembic; M0-5 CI lint/typecheck/tests; M0-6 .env.example + fail-closed config; M0-7 README/LICENSE/CONTRIBUTING; M0-8 logging/error middleware/response envelope.
Goal: docker compose up succeeds, CI green, /health returns 200, migrations run.
Gate: all eight tickets Done and Boss confirms before M1.

### M1 — Trust layer (12)
Order: password hashing; JWT; TOTP; RBAC; elevated token; audit chain; Vault; secrets broker; tool registry; approval gate; snapshots; injection wrapper.
Special tests: friday audit verify catches tamper; sealed Vault never falls back to plaintext; approval bypass blocked without DB row; delimiter corpus and checksum-on-read.
Gate: all 12 Done and Boss runs M1 acceptance script; confirm audit, approvals, Vault and registry before M2. Never compress or stub M1.

### M2 — Brain (14)
Order: orchestrator loop; Ollama fallback; chat API + WS; chat UI; memory; shell; file tools; web fetch; weather; dashboard; design tokens; multilingual; budget guard; kill switch.
Special requirements: every gated tool routes through approval gate; kill switch halts workers before API and is tested under load; budget default $3/day and test $0.01; dashboard eight tiles with loading/empty/error/success; inline approvals and no streaming layout shift.
Gate: Boss confirms chat, four tools and kill switch end-to-end before M3.

### M3 — Domains (12)
Order: RSS ingest; summarizer; news UI; panchang; calendar UI; OAuth; email reader + untrusted banner; draft/send; spam quarantine; analysis; briefing scheduler; jobs API.
Special requirements: news RSS + summary + link only, never full article bodies; every email view has “⚠ Untrusted content”; no permanent delete, quarantine first; panchang always states timezone and falls back to Swiss Ephemeris with computed locally banner; PDF sandbox with malicious-PDF test.
Gate: Boss confirms all four domains end-to-end from web app before M4.

### M4 — Mobile + Voice (10)
Order: Expo skeleton; SecureStore auth; mobile chat; mobile dashboard; push; Tailscale config; wake word + STT; female TTS; voice approval; offline.
Special requirements: SecureStore not AsyncStorage; Tailscale 100.x.x.x; never public :8000; lint flags 0.0.0.0 mobile binding; push within five seconds; voice approval only confirms currently shown approval with elevated intent; VOICE_ENABLED and no text regression.
Gate: Boss confirms mobile login via Tailscale, push timing and voice approval before M5.

### M5 — Cyber (8)
Order: security agent; failed-login detector; file-integrity monitor; Trivy+nmap+Semgrep; incident report; nightly audit verifier; backups; retention purge.
Special requirements: local-machine target allowlist; never scan machines Friday does not own; incident report is draft, no auto-notify; audit retention 24 months; restore in scratch environment with checksum match.
Gate: Boss confirms scratch restore, simulated tamper detection and phone alert before M6.

### M6 — Trading + Build + Launch (14)
Order: Kite auth read-only + paper flag; paper ledger; risk engine; trading UI; order→approval→execute; flatten/halt; 90-day paper gate; code agent; site generator; document generator; plugins; E2E; deploy script; launch readiness.
Special requirements: TRADING_LIVE=false enforced and real orders refused; hard-coded risk defaults and runtime raises blocked absent decision note; 90-day gate (test 89 days remaining); every order approval→risk→paper fill; hostile site preview sandbox test; E2E covers login/chat/approval/kill/paper order/resume and green before M6-14; readiness checklist 100% green.
Gate: all checks green, then v0.1.0 readiness/tag.

## 5. Daily ticket ritual
For one ticket: read full ticket metadata and acceptance criteria; confirm dependencies; paste criteria verbatim; show complete test and paths; wait for Boss “go”; write test first, run expected failure, implement smallest passing change, run DoD; report files/tests/summary/commit; ask for next ticket. Stop if unclear, dependencies incomplete or scope exceeds estimate by 2x. Never guess.

## 6. Milestone acceptance
At each milestone end: verify all tickets Done per DoD; describe manual end-to-end flow step-by-step; list deferred Should/Could and reasons; record decisions; flag carried risks; produce report; wait for Boss review. Do not start next milestone until confirmed.

## 7. Global controls
Never skip M1. Every tool declares permissions. Destructive actions require approval row. Vault only for secrets. API port never public. Untrusted content is data. Do not modify locked contracts without decision note. Paper trading only. Test first.

## 8. Current repository baseline
At initial inspection, GitHub repository Prathamesh7498/Friday- on main contained only a minimal README. The six source docs and scaffold were absent. This is a reconstruction, not recovery of original files. Current planned starting point is M0-1, subject to review and fresh repository inspection.

## 9. Plan-level TBD
Original ticket priorities, estimates, full dependency graph, verbatim Given/When/Then criteria, acceptance scripts, eight dashboard tile definitions, four initial tool identities, deployment topology and launch-readiness checklist were not supplied. Do not invent them. Add approved decisions to source control before dependent implementation.
