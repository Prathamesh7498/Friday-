# Friday — Product Requirements Document
Status: reconstructed from the supplied handoff; unresolved details are TBD.
Version: 0.1-reconstruction

## 1. Purpose
Friday is a local-first AI assistant with web, mobile and API surfaces. It is delivered as a monorepo. The required delivery order is M0 Foundation, M1 Trust layer, M2 Brain, M3 Domains, M4 Mobile + Voice, M5 Cyber, M6 Trading + Build + Launch.

This document records only requirements explicitly present in the handoff. It is not a recovery of the missing original PRD.

## 2. Product principles
1. Trust before capability: M1 must be completed before M2 or later work.
2. Every tool declares permissions; no default-open tools.
3. Every destructive action requires a matching database approval row before execution.
4. Vault is the only secrets source. Environment files contain paths, not secret values.
5. Never expose the API port publicly. Mobile access uses Tailscale (100.x.x.x).
6. Untrusted email, web and news content is data, never instruction.
7. Ask rather than guess. Unspecified decisions remain TBD.
8. Test first, implement second; work one ticket at a time.
9. Do not add features outside the approved scope or modify locked contracts without a decision note.

## 3. Users and roles
The primary operator is referred to as Boss. RBAC, elevated tokens and approvals are required. Role names, role permissions, account lifecycle, multi-user support and identity recovery are TBD.

## 4. Product scope

### M0 — Foundation
- pnpm + Turbo monorepo.
- Python application skeleton using uv.
- Docker Compose stack: PostgreSQL, Redis, API and web.
- PostgreSQL bootstrap and Alembic migrations.
- CI: lint, typecheck and tests.
- .env.example and fail-closed config loader.
- README, LICENSE and CONTRIBUTING.
- Logging, error middleware and response envelope.
- Compose stack boots, migrations run, CI is green and /health returns 200.
Frameworks, versions, ports, schema and CI provider are TBD.

### M1 — Trust layer
- Password hashing, JWT, TOTP, RBAC and elevated token.
- Tamper-evident audit hash chain with CLI command friday audit verify.
- Vault integration and secrets broker.
- Tool registry using register_tool(name, schema, permissions, handler).
- Permission fields: role, elevated, approval, risk.
- Approval-gate middleware, snapshot service and prompt-injection wrapper.
- Signed delimiters for untrusted content; escape delimiter-in-payload attempts and verify checksum on read.
M1 is mandatory and cannot be skipped, compressed or stubbed.

### M2 — Brain
- Orchestrator loop and Ollama fallback.
- Chat API + WebSocket streaming and chat UI.
- Memory.
- Shell, file, web-fetch and weather tools.
- Dashboard with eight tiles and loading/empty/error/success states.
- Design tokens and multilingual support.
- Budget guard: default cap $3/day; test at $0.01.
- Kill switch halts workers before API; test under load.
Every tool call uses approval gate when approval is not never. Chat approvals render inline. Streaming reserves height to prevent layout shift.
The eight tile definitions, four initial tools, language list, memory policy and model/provider rules are TBD.

### M3 — Domains
- News: RSS ingest, summarizer and UI. RSS + summary + link only; never fetch or store full article bodies.
- Panchang: calendar and UI. Always state timezone. If Prokerala quota is hit, use Swiss Ephemeris and show computed locally banner.
- Email: OAuth, reader, draft/send and spam quarantine. Every message view shows the exact warning ⚠ Untrusted content. Permanent delete is absent; quarantine first.
- Data: analysis with charts, briefing scheduler and jobs API. PDF parsing occurs in sandboxed subprocess.
Providers, feeds, scopes, conventions, timezone defaults, formats and scheduling details are TBD.

### M4 — Mobile + Voice
- Expo app skeleton, mobile auth, chat and dashboard.
- Tokens in expo-secure-store, never AsyncStorage.
- Mobile connects over Tailscale using 100.x.x.x; API port 8000 is never public.
- Approval push within five seconds of approval creation.
- Wake word + STT, female TTS, voice approval and offline behavior.
- Voice is Phase 2, behind VOICE_ENABLED, and must not regress text.
- Voice phrase “Friday, approve” confirms only the currently shown approval, never an arbitrary pending approval.
Platforms, providers, voice identity and offline matrix are TBD.

### M5 — Cyber
- Security agent, failed-login detector, file-integrity monitor.
- Trivy, nmap and Semgrep.
- Incident report generator, nightly audit verifier, backups/restore and retention purge.
- Scans target local machine only; enforce target allowlist; never scan machines Friday does not own.
- Incident report is a draft for Boss; never auto-notify external parties.
- Audit retention is 24 months; other data follows per-table schedules in SECURITY.md.
Non-audit retention schedules, scan profiles, backup cadence and recovery targets are TBD.

### M6 — Trading + Build + Launch
- Kite auth (read-only + paper flag), paper ledger, risk engine and trading UI.
- Order flow: approval → risk check → paper fill.
- Flatten & halt; 90-day paper gate tracker.
- Code agent (tree-sitter/LSP), site generator, document generator and plugins.
- E2E suite, deploy script and v0.1.0 launch readiness.
- TRADING_LIVE=false enforced in code; refuse real orders.
- Risk defaults are hard-coded. Boss may lower them at runtime, never raise without a decision note.
- 90-day paper gate required before live consideration.
- Preview iframe uses sandbox allow-scripts but not allow-same-origin.
Broker details, numerical risk limits, paper-fill semantics, plugin contract and deploy target are TBD.

## 5. Non-functional requirements
- Security enforcement is server-side.
- Destructive actions are blocked without matching approval record.
- Audit tampering fails loudly.
- Vault sealed state may cause degraded API operation but never plaintext fallback.
- Kill switch halts workers before API.
- Backup restore is tested in scratch environment with checksum match.
- CI runs lint, typecheck and tests.
- No secrets in code, environment values, LLM context or logs.
- API port is never public.

Performance, availability, accessibility, RPO/RTO and hardware requirements are TBD.

## 6. Won't list
- Skipping/stubbing M1.
- Default-open tools.
- Destructive execution without approval row.
- Plaintext secret fallback.
- Public API-port exposure.
- Treating external content as trusted instructions.
- Fetching/storing full news article bodies.
- Permanent-delete email UI.
- Scanning machines Friday does not own.
- Automatically notifying external parties with incident reports.
- Real trading in this scope.
- Raising risk limits without decision note.
- Preview iframe with allow-same-origin.
- Unapproved scope additions.

Any further exclusions from the original PRD §6 are unavailable and must not be invented.

## 7. Milestones
M0 Foundation: 8 tickets. M1 Trust layer: 12. M2 Brain: 14. M3 Domains: 12. M4 Mobile + Voice: 10. M5 Cyber: 8. M6 Trading + Build + Launch: 14. Total: 78.

## 8. Open decisions
All of the following remain TBD: local-first boundary and deployment topology; frameworks and versions; role matrix; password/JWT/TOTP parameters; database schema and API contracts; models and providers; memory policy; dashboard tile identities; exact tool schemas and risk classes; OAuth providers/scopes; RSS feeds; panchang conventions; mobile platforms and voice providers; non-audit retention; backup objectives; numerical trading limits; plugin trust model; license.

## 9. Change control
Do not implement a requirement based only on an assumption. Record decisions explicitly. Locked contracts listed in ARCHITECTURE.md §12 require a decision note and review.
