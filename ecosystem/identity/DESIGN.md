# ECO-0001 — Shared identity and contextual authentication

**Scope:** aiaiaiai ecosystem, including 0x1, Prism, and future independent products  
**Status:** cross-product design principles agreed in discussion on 2026-10-19; implementation is incremental  
**Owner:** parent ecosystem; each product retains ownership of its own experience and runtime

This is the shared design reference for identity and authentication *across products*, not a claim that central SSO, passkeys, transactional email, or any proposed host is deployed. Product-specific protocols and implementations remain authoritative within their boundaries.

## 1. One identity, independent products

- A person should have **one underlying identity**, not a separate 0x1 person and Prism person. Their product memberships, sessions, permissions, onboarding states, and presentation can differ.
- **Private, stable internal user identifier:** Only the Identity Service knows the internal identifier that maintains the user's account continuity. It is **never shown or disclosed to the user**, including in own-account/profile views; no other product, client, external identity provider, email, log, public API, or third-party integration may receive or persist this internal value. This is a service-internal account identifier, **not** a new user-visible name, and must not be conflated with a protocol identity key or secret.
- **`pub_dress` is not the internal ID.** It is the user's public, human-readable, mutable identifier and a native login lookup value. It resolves inside the Identity Service to the stable private user identifier. Changing a permitted part of `pub_dress` preserves the same user identity, authorization continuity, and history; a public address or email alone is never authentication proof.
- The exact `pub_dress` format, case sensitivity, uniqueness, discriminator continuity, slug rotation, and provider-binding invariants remain **owned by [nilx-one/0x1's identity specification](https://github.com/nilx-one/0x1/blob/master/documents/04-identity.md)**; this document does not redefine them.
- Other products consume authenticated decisions and explicit authorization contracts from Identity. If a product needs persistent account association, use a *product-scoped, revocable/rotatable opaque reference* designed by Identity, **not** its private internal user identifier, and do not infer identity from `pub_dress` alone. The exact reference protocol remains to be designed.
- A verified provider account maps to the same person through its stable provider subject. Matching display names, Telegram usernames, or email strings do **not** authorize an implicit account merge, takeover, or provider link. Linking an existing identity requires proof of both sides and explicit user action.
- Authentication authority, registration challenges, provider bindings, and session security belong to the common **Identity** boundary. Prism Hub, 0x1, and bot clients consume the authority through explicit interfaces and keep independent product state. No cross-product database, cookie, or token sharing.
- **Identity Service** is a technical/service boundary, **not** an invented public identity brand such as "aiaiaiai ID". User-facing language should refer to signing in, the account, or `pub_dress` as appropriate. The terminology and experience of 0x1 (Bond, Avaia, xPing) must not become mandatory concepts for users of Prism or other products.

## 2. Native foundation, available methods, and rollout

The agreed design is **native `pub_dress` authentication + passkeys**, with optional externally verified methods and email; no product should accumulate sign-in buttons merely because an OAuth provider exists.

| Method | Intended ecosystem role | Verified implementation state on 2026-10-19 |
| --- | --- | --- |
| `pub_dress` + password | Native baseline; no game-specific UI required | Implemented in 0x1 Identity Service |
| Passkey / WebAuthn | Shared, product-neutral sign-in | **Planned**, not verified as implemented |
| Verified email + one-time code/link | Shared authentication and address linking, prominent in Prism | **Planned**, not implemented in checked Identity Service |
| Telegram | Contextually prominent for 0x1 and appropriate Telegram-host flows | Existing 0x1 provider authentication |
| Discord | Contextually relevant to 0x1, not a mandatory Prism entry point | Existing 0x1 provider authentication |
| GitHub | May authenticate to 0x1, but repository integration is a separate feature | Identity-only provider flow exists; full integration **not** implemented |
| LinkedIn and others | Optional future additions driven by real product value | **Not committed** for implementation |

**Product emphasis is not a hard provider ban.** A 0x1 user can choose to add email or other supported methods; a Prism user with the same underlying identity should be able to enter 0x1 without being registered as a different person. When a method is not supported, do not present it as functional.

The *initial display and supported capabilities* may be tailored by product, but a product must not silently grant new external integrations or account privileges just because a provider was used for authentication. GitHub repository access is explicitly **not** implied by Sign in with GitHub.

## 3. Client-contextual intent

A product initiates a clear authentication **intent** and communicates *where and why* a person arrived. The Identity boundary verifies the requesting client and provides consistent security semantics.

Illustrative request shape; **not** a published endpoint/schema:

```json
{
  "intent": "register",
  "client_id": "prism",
  "identifier": "user@example.com",
  "locale": "uk",
  "entry_context": "web"
}
```

- `client_id` identifies a **server-registered** ecosystem client; it selects authorized redirects, configured available methods, approved sender identity, and appropriate experience/branding. A browser-provided label does not establish client authority.
- `intent` distinguishes registration, sign-in, email verification or linking, and recovery. It represents the user's requested action, **not** evidence or authorization to complete it.
- `entry_context` (e.g., web, Telegram bot, explicit invitation) may inform onboarding or the return destination but is not an independently trusted identity proof.
- `locale` chooses supported language; never changes verification rules.
- Authentication failures have typed, security-safe outcomes. A malformed public address may get specific format guidance; invalid credentials must not reveal whether a named account exists.
- Redirects and callbacks must be allowlisted per client. A cross-product authorization protocol, if introduced, should use standard, reviewed flows (for example authorization code + PKCE) instead of passing session cookies or passwords between products.
- Neutral login origin such as `auth.aiaiaiai.org` is a **proposal**, not an active endpoint or a settled hostname contract.

## 4. Product-owned authentication experience

**Security behavior is invariant; presentation is product-owned.**

| Context | Experience |
| --- | --- |
| 0x1 | A playful entry point may show xPing for errors, progress, verified email, or passkey completion. Bond/Avaia and game-specific UI belong **after** choosing 0x1, never as requirements of neutral registration. |
| Prism | Calm, professional, potentially B2B onboarding. Prefer email and passkeys when available; no forced Bond, Avaia, gameplay, or Discord-style positioning. |
| Other products | Their own visual language and supported methods, without forking the identity or verification engine. |

An xPing toast and a Prism inline error may render the *same normalized result*. Presentation components receive safe result codes and non-secret metadata, **never** passwords, email challenges, provider access tokens, or unnecessary personal data.

Cross-product promotion is **not** part of neutral login. A product may introduce another product only within a meaningful, already chosen product experience and without coercion. Prism and 0x1 retain independent audiences and product responsibilities.

**Naming and bot entry point:** Never present a Prism or Prism Hubot login as **“Sign in with 0x1”** or **“Sign in with aiaiaiai ID”**. The first treats a product as an identity provider; the second invents an unapproved, misleading public identifier. Use plain actions such as **“Sign in”**, **“Sign in using `pub_dress`”**, or **“Connect an existing account”** as fits the actual flow. Neither launching 0x1 nor creating a Bond/game profile is required. Preserve usable existing bot scenarios that do not need a new shared account; do not force registration merely to receive an existing service.

## 5. Contextual transactional email and sender domains

The email challenge is **issued, verified, rate-limited, expired, and consumed exclusively by Identity**. A product-specific renderer may change language, tone, design, and approved sender; it cannot change verification or issue its own parallel identity.

Illustrative, **not-yet-provisioned** sender options:

| Client context | Example From | Example presentation |
| --- | --- | --- |
| Prism | `prism@aiaiaiai.org` | `aiaiaiai × Prism`; restrained business-style verification email |
| 0x1 | `n-reply@nilx.one` | `aiaiaiai × 0x1`; playful but unambiguous verification email |
| Ecosystem security | A centrally approved aiaiaiai sender (**address undecided**) | Account-wide security/recovery notification without pretending to be a single product |

Sender addresses, domains, return-path configuration, locales, and templates must be **selected by trusted server-side client configuration**. The requester cannot arbitrarily set the From address. A sender is eligible only after control and actual transport authorization are verified; SPF, DKIM, DMARC, and deliverability must be configured per sending domain.

One transactional email transport may support several verified ecosystem domains. A branded From header **does not** imply a separate mailbox server, account database, or Identity deployment. Account-wide security notices may deliberately use a neutral ecosystem sender while accurately naming the affected product.

Email confirmation, sign-in, and email-to-existing-account linking must be designed as distinct authorized transitions. A matching mailbox must not silently merge accounts.

## 6. Prism Porter boundary

Reuse an existing capability instead of creating another mail-specific templating stack, **provided the auth path remains independent of the availability of Prism Hub and its publishing workflows**.

```text
Product client
   -> Identity (validated client + intent; creates/owns challenge)
   -> presentation port [candidate: Prism Porter renderer]
   -> transactional email adapter (authorized sender, transport, delivery result)
   -> mailbox

User returns -> Identity verifies single-use challenge -> product session
```

- [Prism Porter](https://github.com/aiaiaiai-org/prism-porter) currently owns transport-neutral rendering, route resolution, and delivery-intent construction, **not physical email delivery, identity state, or transport secrets**. It already has a `mail.invitation` renderer; adapting it to a versioned identity-email artifact (working name `identity.email-challenge.v1`) is a **proposed integration**, not completed work.
- Identity retains challenge issuance, security policy, rate limits, validation, consumption, and the allowed product/sender mapping. The renderer must never decide authentication success and must not persist sensitive challenge data in ordinary publishing logs or durable delivery artifacts.
- A narrow **transactional email adapter** would own actual sending, provider credentials, delivery errors, and any retry policy safe for short-lived challenges.
- [Prism Mail](https://github.com/aiaiaiai-org/prism-mail) is currently scoped to inbound mail intelligence/digests. It must **not** be silently treated as an existing transactional email sender.
- Sending failure is observable, not success. Avoid a required runtime dependency from login onto Prism Hub or an unrelated publishing-control plane.

## 7. Boundaries, implementation state, and open implementation choices

This document records shared direction and constraints, **not a declaration that future features have shipped**:

- Already exists: 0x1 native `pub_dress` + password authentication, 0x1 external provider auth paths, Prism Porter rendering primitives.
- Not yet delivered as an ecosystem capability: neutral shared issuer/SSO and product clients, passkeys, verified email login and transactional email delivery, contextual email templates/senders, Prism migration to shared authentication.
- Open engineering decisions: how to migrate/introduce the private service-internal user identifier without exposing it or confusing it with 0x1 protocol identity keys; product-scoped authorization references and existing Prism Hub canonical identity migration; issuer hostname and client registration mechanism; passkey attestation/recovery policy; email code vs link UX; template/artifact wire schema, secret handling and transport; sender verification and bounce handling; full GitHub integration; future providers.
- Product-specific implementation requirements remain with [0x1](https://github.com/nilx-one/0x1), [0x1 Web Identity Service](https://github.com/nilx-one/web/tree/master/services/identity), [Prism Hub](https://github.com/aiaiaiai-org/prism-hub), and [Prism Porter](https://github.com/aiaiaiai-org/prism-porter). This document does not supersede their executable contracts.
- Planned work should be delivered incrementally in focused PRs, without assuming a proposed method, domain, or sender is live.

**Design invariant:** *Identity determines what is true and who can authenticate; each product owns how authentication feels; Porter may format external communication, but does not own identity or deliver mail.*

---

<!-- © 2026 aiaiaiai · aiaiaiai.org -->
