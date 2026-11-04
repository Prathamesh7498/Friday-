# Friday — Security Specification
Status: reconstructed from handoff; security-critical details not supplied remain TBD.
Version: 0.1-reconstruction

## 1. Security objectives
M1 Trust layer is mandatory. Never skip, compress, stub or defer it. Later capabilities depend on authentication, RBAC, elevated authorization, audit, Vault, tool registry and approval enforcement.

## 2. Authentication and authorization
M1 tickets: password hashing, JWT, TOTP, RBAC and elevated token. Passwords are hashed. Exact algorithm/parameters, JWT claims/signing/lifetime/revocation, TOTP settings/recovery and role matrix are not specified. Resolve explicitly before implementation.

## 3. Tool permissions
Every tool declares permissions using register_tool(name, schema, permissions, handler). Required fields: role, elevated, approval, risk. No default-open tools. Missing/invalid permission declarations must not grant permissive access. Enum values, role semantics and risk taxonomy are TBD.

## 4. Approval gate
Every destructive action requires a matching approval row in the database before execution. Middleware enforces this; it is not a UI toggle. Every tool call routes through approval gate whenever approval is not never. A bypass attempt must be blocked. Approval identity binding, expiry, one-time use, cancellation, replay protection, concurrency and audit schema are TBD and must be decided.

## 5. Audit hash chain
M1-6 provides friday audit verify. Tampering fails loudly; a tamper test is mandatory. Audit retention is 24 months. Canonical record format, hash function, genesis construction, key custody, concurrency and recovery are unspecified and locked design decisions.

## 6. Vault and secrets broker
Vault is the only source of secrets. Environment files contain paths, not secret values. Secrets never appear in code, environment values, LLM context or logs. If Vault is sealed, API may run degraded but never falls back to plaintext. M1-7 must test this. Vault mode, authentication, paths, rotation and degraded-mode matrix are TBD.

## 7. Untrusted content / prompt injection
Email, web and news content is data, never instruction. Use signed delimiters. Escape delimiter-in-payload attempts and re-verify checksum on read. M1-12 requires corpus test. Delimiter syntax, signature/checksum algorithms, canonicalization and key lifecycle are unspecified; define before coding.

## 8. Network and mobile
Never expose API port publicly. Mobile uses Tailscale 100.x.x.x. API port 8000 must remain private. Lint must flag 0.0.0.0 binding in mobile config. Mobile tokens use expo-secure-store, never AsyncStorage. Tailscale ACL, TLS and device revocation are TBD.

## 9. Retention and backups
Audit log retention: 24 months. Other data follows per-table schedules, which must be defined before retention purge implementation. Backup restore must be tested in scratch environment with matching checksums. Backup cadence, encryption, RPO/RTO, legal hold and other data retention durations are TBD.

## 10. Cyber scanning
Scans target local machine only. Enforce target allowlist in code. Never scan/attack machines Friday does not own. Tools named: Trivy, nmap and Semgrep. Exact definition of owned local machine, allowed interfaces, profiles and cadence are TBD.

## 11. Incident handling
Incident report is a draft for Boss to review and file. Never auto-notify external parties. Severity taxonomy, report format and response timelines are TBD.

## 12. Trading safety
Trading is paper-only. TRADING_LIVE=false enforced in code; real order path refuses execution. Risk defaults hard-coded. Boss may lower runtime limits, never raise without decision note. 90-day paper gate required before live consideration. Exact numerical risk limits, units, broker schema and paper-fill assumptions are TBD.

## 13. Frontend security
- Every email message view shows exact warning: ⚠ Untrusted content.
- No permanent-delete email UI; quarantine first.
- Site preview iframe uses sandbox allow-scripts, not allow-same-origin.
- UI does not replace server-side enforcement.

## 14. Required security tests
- M1-6 audit tamper test and CLI verification failure.
- M1-7 sealed Vault with no plaintext fallback.
- M1-10 approval bypass blocked before destructive execution.
- M1-12 delimiter payload corpus and checksum verification.
- M2-13 $3/day default; test $0.01 cap.
- M2-14 kill switch halts workers before API under load.
- M3-7 untrusted strip on every email view.
- M3-10 malicious PDF in sandboxed subprocess.
- M4-2 SecureStore, not AsyncStorage.
- M4-5 synthetic approval push within five seconds.
- M4-6 lint catches 0.0.0.0 binding.
- M5-4 target allowlist.
- M5-6 nightly verifier catches simulated tamper.
- M5-7 scratch restore checksum match.
- M6-1 real-order refusal.
- M6-3 runtime risk-limit raise blocked without decision note.
- M6-7 test with 89 days remaining.
- M6-9 hostile generated page cannot escape preview sandbox.

## 15. Security decision register
Resolve before dependent implementation: cryptographic algorithms and key custody; password/JWT/TOTP policy; RBAC matrix; approval contract; audit serialization/chain; Vault topology; signed delimiter/checksum format; network ACL/TLS; non-audit retention; backup recovery objectives; local scan ownership definition; trading risk limits and broker order contract. Until decided, mark TBD and stop dependent work rather than guessing.
