# Friday — Frontend Specification
Status: reconstructed from handoff; unspecified visual/interaction details remain TBD.
Version: 0.1-reconstruction

## 1. Scope
Friday has web and Expo mobile surfaces. Frontend includes chat, dashboard, approvals and domain interfaces. UI must reflect server-side authorization and must not be the security enforcement layer. Framework, component library, typography, palette, breakpoints and visual identity are TBD.

## 2. Web surface
Known areas: streaming chat, dashboard, inline approvals, news, panchang/calendar, email reader/draft/send/quarantine, data analysis/charts, jobs, trading, code/site/document generation. Route map and exact screen inventory are TBD.

## 3. Mobile surface
Expo app includes login/auth, chat, dashboard, approval push, voice (feature flagged) and offline behavior. Tokens stored in expo-secure-store, never AsyncStorage. Connect through Tailscale 100.x.x.x; do not expose API port 8000 publicly. Supported OS/device matrix is TBD.

## 4. Chat
Chat streams over WebSocket. Reserve height to prevent layout shift. Render approval requests inline, not only in modal. Do not display success before server confirmation. Message schema, event names, reconnect policy, attachment behavior and markdown rendering are TBD.

## 5. Dashboard
Eight tiles are required. Every tile must show loading, empty, error and success states. The identity and data source of each of the eight tiles were not provided and must be decided before M2-10. Do not invent tile names.

## 6. Approval interaction
Approvals appear inline in chat. UI reflects exact server-side approval state. Voice approval requires elevated intent and can confirm only the currently shown approval. Push target is within five seconds of approval creation. Card fields, copy, expiry and denial behavior are TBD and must follow the locked approval contract.

## 7. Domain UI
### News
Show RSS-derived summaries and links. Do not fetch/store full article bodies. “Exam mode” is named but its detailed behavior is TBD.
### Panchang
Calendar UI. Always state timezone. On Prokerala quota fallback to Swiss Ephemeris, show “computed locally”.
### Email
Every displayed message/view carries exact “⚠ Untrusted content” strip. No permanent-delete UI; quarantine first. Draft/send is in scope. Treat message content as untrusted data.
### Data
Analysis may display charts. PDF parsing runs in sandboxed subprocess. Chart types and report formats TBD.
### Trading
Paper-only UI. Order flow: approval modal → risk check → paper fill. Include flatten/halt and 90-day paper gate tracker. Do not present paper fills as real executions.
### Site generator
Preview iframe sandbox must be allow-scripts without allow-same-origin. Hostile generated-page test required.

## 8. Voice
Wake word + STT and female TTS are planned. Voice is Phase 2 behind VOICE_ENABLED and must not regress text. Phrase “Friday, approve” confirms only currently shown approval, never arbitrary pending approval. Voice providers, identity, languages and permission UX are TBD.

## 9. Design system
M2-11 creates design tokens. Token names/values, color roles, typography, spacing, component library, responsive breakpoints, themes and motion are TBD. Do not represent a speculative design system as approved.

## 10. Accessibility and localization
Multilingual support is M2-12. Languages, translation workflow and fallback are TBD. Accessibility standard and acceptance criteria were not included and require an explicit decision.

## 11. Frontend quality gates
- Dashboard tiles have loading/empty/error/success states.
- Chat streaming has no layout shift.
- Approvals render inline.
- Every email view displays untrusted-content warning.
- No permanent-delete email control.
- Panchang fallback displays computed-locally banner and timezone.
- Mobile uses SecureStore.
- Trading is clearly paper-only.
- Preview iframe excludes allow-same-origin.
- Voice flag does not regress text.

## 12. Open frontend decisions
Frameworks and versions; route map; dashboard tile names; design tokens; accessibility; language list; chat event contract; approval card contract; email view inventory; exam-mode details; calendar interaction; trading presentation; voice UX; offline capabilities.
