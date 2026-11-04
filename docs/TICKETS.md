# Friday — Delivery Tickets
Status: reconstructed from supplied handoff. Total: 78 tickets.
Important: the original priority, size, detailed dependency graph and verbatim Given/When/Then criteria were not supplied. They are not fabricated here. The ticket order and known requirements below are preserved.

## Global workflow
For each ticket: read its approved acceptance criteria; confirm dependencies Done; paste criteria verbatim; show one complete planned test and file paths; wait for Boss's “go”; write test first and confirm expected failure; implement smallest passing change; run lint, typecheck and tests; wire audit for relevant actions; verify UI states; update .env.example without secrets; inspect for secrets; commit; report files/tests/summary/commit. If unclear, stop and ask. If estimate exceeds 2x, ask whether to split. No scope creep.

## M0 — Foundation (8)
Goal: repo boots, docker compose up brings up stack, CI green, /health returns 200, migrations run.
1. M0-1 Init pnpm + Turbo monorepo.
2. M0-2 Python app skeleton (uv).
3. M0-3 Docker Compose (postgres, redis, api, web).
4. M0-4 Postgres schema bootstrap + Alembic.
5. M0-5 CI: lint + typecheck + tests.
6. M0-6 .env.example + config loader (fail-closed); env holds paths, not secret values.
7. M0-7 README + LICENSE + CONTRIBUTING; license choice TBD.
8. M0-8 Logging + error middleware + response envelope; redact secrets.
Commit convention: chore(M0-#): title.
Exit: all eight Done; stack boots, CI green, /health 200, migrations run; Boss confirms before M1.

## M1 — Trust layer (12; mandatory)
Goal: login + TOTP, RBAC, audit hash chain, Vault, tool registry, approval gate, snapshots and injection wrapper.
1. M1-1 Password hashing.
2. M1-2 JWT.
3. M1-3 TOTP.
4. M1-4 RBAC.
5. M1-5 Elevated token.
6. M1-6 Audit chain. CLI friday audit verify; tampering fails loudly; tamper test.
7. M1-7 Vault. Sealed Vault means degraded API, never plaintext fallback; test it.
8. M1-8 Secrets broker.
9. M1-9 Tool registry. register_tool(name, schema, permissions, handler); permission fields role, elevated, approval, risk; no default-open tools.
10. M1-10 Approval gate. No destructive tool without matching approvals DB row; test bypass is blocked.
11. M1-11 Snapshots.
12. M1-12 Injection wrapper. Signed delimiters; escape delimiter-in-payload attempts and re-verify checksum on read; corpus test.
Commit convention: feat(M1-#): title.
Exit: all 12 Done; Boss runs M1 acceptance script and confirms audit, approvals, Vault and registry before M2.

## M2 — Brain (14)
Goal: WebSocket chat, orchestrator calls four tools, dashboard eight tiles/all states, kill switch end-to-end.
1. M2-1 Orchestrator loop.
2. M2-2 Ollama fallback.
3. M2-3 Chat API + WS.
4. M2-4 Chat UI. Inline approvals; reserve height to prevent layout shift.
5. M2-5 Memory.
6. M2-6 Shell tool.
7. M2-7 File tools.
8. M2-8 Web fetch.
9. M2-9 Weather.
10. M2-10 Dashboard. Eight tiles; loading/empty/error/success for each; no blank screens.
11. M2-11 Design tokens.
12. M2-12 Multilingual.
13. M2-13 Budget guard. Default $3/day; test $0.01.
14. M2-14 Kill switch. Halt workers before API; test under load.
All tools route through approval gate when approval is not never.
Commit convention: feat(M2-#): title.
Exit: Boss confirms chat, four tools and kill switch end-to-end before M3.

## M3 — Domains (12)
Goal: news (exam mode), panchang calendar, email and data analysis with charts; four sub-agents end-to-end.
1. M3-1 RSS ingest. Never fetch/store full article bodies.
2. M3-2 Summarizer. RSS + summary + link only.
3. M3-3 News UI.
4. M3-4 Panchang. Always state timezone; Prokerala quota fallback to Swiss Ephemeris and show computed locally.
5. M3-5 Calendar UI.
6. M3-6 OAuth.
7. M3-7 Reader + untrusted banner. Exact “⚠ Untrusted content” strip on every message view; test all views.
8. M3-8 Draft/send.
9. M3-9 Spam quarantine. No permanent delete UI; quarantine first.
10. M3-10 Analysis. PDF in sandboxed subprocess; malicious PDF test.
11. M3-11 Briefing scheduler.
12. M3-12 Jobs API.
Commit convention: feat(M3-#): title.
Exit: Boss confirms all four domains end-to-end from web app before M4.

## M4 — Mobile + Voice (10)
Goal: Expo app, Tailscale, approval push, wake word/STT, female TTS, voice approval.
1. M4-1 Expo skeleton.
2. M4-2 Mobile auth (SecureStore). SecureStore, never AsyncStorage; test by inspection.
3. M4-3 Mobile chat.
4. M4-4 Mobile dashboard.
5. M4-5 Push notifications. Synthetic approval push within five seconds.
6. M4-6 Tailscale config. 100.x.x.x; never expose :8000 publicly; lint flags 0.0.0.0 binding in mobile config.
7. M4-7 Wake word + STT.
8. M4-8 Female TTS.
9. M4-9 Voice approval. Elevated intent; “Friday, approve” confirms only currently shown approval.
10. M4-10 Offline.
Voice is Phase 2 behind VOICE_ENABLED; text path must not regress.
Commit convention: feat(M4-#): title.
Exit: Boss confirms mobile login via Tailscale, push within five seconds and voice approval end-to-end before M5.

## M5 — Cyber (8)
Goal: security monitoring, integrity, scans, incident draft, nightly audit verification, backups and retention.
1. M5-1 Security agent skeleton.
2. M5-2 Failed-login detector.
3. M5-3 File-integrity monitor.
4. M5-4 Trivy + nmap + Semgrep. Local machine only; enforce target allowlist; never scan machines Friday does not own.
5. M5-5 Incident report. Draft for Boss; never auto-notify external parties.
6. M5-6 Audit chain verifier. Nightly; catches simulated tamper.
7. M5-7 Backups. Restore in scratch environment; checksum match.
8. M5-8 Retention purge. Audit 24 months; other data per SECURITY.md §9.
Commit convention: feat(M5-#): title.
Exit: Boss confirms scratch restore/checksum, nightly tamper detection and security alert reached phone before M6.

## M6 — Trading + Build + Launch (14)
Goal: paper trading, generators, code agent, E2E, deploy and v0.1.0.
1. M6-1 Kite auth (read-only + paper flag). TRADING_LIVE=false; real orders refused; test.
2. M6-2 Paper ledger.
3. M6-3 Risk engine. Hard-coded defaults; Boss may lower runtime limits, never raise without decision note; test raise-block.
4. M6-4 Trading UI.
5. M6-5 Order → approval → execute. Every order: approval modal → risk check → paper fill; E2E test.
6. M6-6 Flatten & halt.
7. M6-7 90-day paper gate tracker. Test with 89 days remaining.
8. M6-8 Code agent (tree-sitter/LSP).
9. M6-9 Site generator. Preview iframe sandbox allow-scripts, not allow-same-origin; hostile page test.
10. M6-10 Document generator.
11. M6-11 Plugins.
12. M6-12 E2E suite. Login, chat, approval, kill, paper order, resume; green in CI before M6-14.
13. M6-13 Deploy script.
14. M6-14 Launch readiness. Checklist 100% green; v0.1.0 target.
Commit convention: feat(M6-#): title.

## Definition of Done
Acceptance criteria pass; tests written first and passing; lint/typecheck/tests pass; audit wired where relevant; UI states complete where relevant; .env.example updated when configuration changes without secret values; no secrets in code/env/model context/logs; no scope creep. Exact commands and coverage thresholds TBD.

## Missing original ticket metadata
Priority, size estimate, full dependency edges, verbatim Given/When/Then acceptance criteria, exact test blocks/file paths and milestone acceptance scripts were not included. They must be added from source or explicitly authored and approved before ticket work. Do not claim these reconstructed summaries are verbatim original criteria.
