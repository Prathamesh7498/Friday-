# Friday — Architecture
Status: reconstructed from handoff; implementation details not stated remain TBD.
Version: 0.1-reconstruction

## 1. Goals
Friday is a local-first AI assistant with web, mobile and API surfaces. The build order is mandatory. Trust controls are enforced at server/tool boundaries, not merely in UI.

**Mandatory milestone gate:** M1 Trust layer must be completed in full before M2 begins. M1 must never be skipped, compressed, stubbed or deferred. The M1 exit criteria and Boss acceptance gate in MASTER_PLAN.md §4 govern progression.

## 2. Repository shape
The handoff establishes this high-level layout:
/
├── apps/
├── packages/
├── infra/
├── docs/
├── README.md
├── CLAUDE.md
├── .gitignore
├── .env.example
└── docker-compose.yml

The six governing documents belong in docs/. Exact app/package names, ownership and dependency graph are TBD.

## 3. Confirmed technology constraints
- Monorepo: pnpm + Turbo.
- Python application: uv.
- Containers: Docker Compose.
- Initial services: PostgreSQL, Redis, API and web.
- Database migrations: Alembic.
- Mobile: Expo.
- Secure mobile token storage: expo-secure-store, not AsyncStorage.
- Realtime chat: WebSocket.
- AI fallback: Ollama.
- Code agent: tree-sitter/LSP.
- Security tooling: Trivy, nmap, Semgrep.
Frameworks, versions, API framework, image tags and exact package names are TBD.

## 4. Logical components
- Web client: chat, dashboard, approvals and domain views.
- Expo mobile client: auth, chat, dashboard, push, optional voice and offline mode.
- API: auth, authorization, business APIs, WebSocket and health endpoint.
- Orchestrator: model interaction and tool invocation.
- Tool registry: name, schema, permissions and handler.
- Approval gate: enforces approval policy and database approval rows.
- Audit service: hash chain and verification CLI.
- Vault/secrets broker: controlled secret retrieval.
- Snapshot service.
- PostgreSQL/Alembic persistence and Redis.
- Background workers, domain services, cyber agent and paper-trading subsystem.
Internal service boundaries and protocols are TBD.

## 5. Tool contract
Every tool is registered through:
register_tool(name, schema, permissions, handler)

Permission fields are role, elevated, approval and risk. No default-open tools. Exact types, enum values, policy precedence and validation behavior are TBD and must be decided before implementation.

Invocation requirements:
1. Resolve registered tool.
2. Validate input against schema.
3. Evaluate caller authorization and declared permissions.
4. Route through approval gate whenever approval is not never.
5. For destructive actions, require a matching database approval row before execution.
6. Record relevant audit events.
7. Execute only after checks pass.
Transaction boundaries, expiry, replay and concurrency behavior are TBD.

## 6. Authentication and authorization
M1 includes password hashing, JWT, TOTP, RBAC and elevated token. Exact token claims, lifetimes, refresh/revocation, password policy, recovery, TOTP parameters and role matrix are TBD. Authorization must be server-side.

## 7. Audit
Audit is a tamper-evident hash chain with CLI command friday audit verify. Tampering fails loudly. Canonical serialization, hash algorithm, genesis record, key management, concurrency and recovery are locked details to define before M1-6.

## 8. Secrets/configuration
Vault is the only secrets source. Environment files contain paths, not secret values. Secrets never appear in source, environment values, LLM context or logs. If Vault is sealed, API may run degraded but never falls back to plaintext. Config loader fails closed. Vault mode, auth method, path scheme and degraded capability matrix are TBD.

## 9. Network
Never expose API port publicly. Mobile uses Tailscale 100.x.x.x. M4 names API port 8000 and requires it remain private. Add lint check against 0.0.0.0 binding in mobile config. Exact bind address, TLS, ACLs, DNS and deployment topology are TBD. Private networking does not replace authentication.

## 10. Domain boundaries
- News: RSS, summary and link only; no full article bodies fetched or stored.
- Email: untrusted content, visible warning on every view, quarantine instead of permanent delete.
- PDF: sandboxed subprocess.
- Panchang: timezone stated; Prokerala quota fallback to Swiss Ephemeris with computed locally banner.
- Trading: paper-only; code refuses real orders while TRADING_LIVE=false.

## 11. Workers and kill switch
Kill switch halts workers before API and is tested under load. Worker supervisor, drain timeout, in-flight work and restart semantics are TBD.

## 12. Locked contracts
Do not modify without explicit decision note:
1. Audit hash chain.
2. Authentication token flow.
3. Approval contract.
4. Broker order schema.
5. Risk-limit semantics.
The canonical schemas/details must be recorded before dependent implementation.

## 13. Delivery/testing
M0 requires Compose stack, migrations, CI lint/typecheck/tests and /health 200. M6 E2E covers login, chat, approval, kill, paper order and resume. Test frameworks, coverage thresholds and CI provider are TBD.

## 14. Open architecture decisions
Frameworks/versions; workspace package names; API routes and WebSocket events; DB ERD; Redis use; permission enums; token lifecycle; audit crypto details; Vault topology; snapshot format; worker supervision; Tailscale ACLs; observability; deployment topology. Do not fill these by assumption.
