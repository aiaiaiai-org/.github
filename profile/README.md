<div align="center">

# aiaiaiai tech.

**Software should be able to explain itself. Ours does.**

[aiaiaiai.org](https://aiaiaiai.org) · `parent organization` · `machine-readable systems` · `browser-native AI` · `provider-agnostic cores`

</div>

---

## Organization

**aiaiaiai tech.** (also **4xAI tech.**) is the parent organization for the non-personal projects and organizations in this ecosystem.

- **Owner / founder:** [0x0sky](https://github.com/0x0sky)
- **GitHub:** [aiaiaiai-tech](https://github.com/aiaiaiai-tech)
- **Site:** [aiaiaiai.org](https://aiaiaiai.org)
- **Current form:** GitHub organization and operating identity
- **Long-term direction:** corporate parent; the exact legal structure is intentionally not a permanent technical invariant
- **Child branches:** [0xda-market](https://github.com/0xda-market) and **nilx.one**, whose current primary repository is [0x1](https://github.com/nilx-one/0x1)

GitHub represents organizations as peer namespaces. That technical equality does not define the ownership or governance model of this ecosystem.

```text
0x0sky                              owner / root identity
└── aiaiaiai tech. / 4xAI tech.    parent organization
    ├── 0xda-market                 digital commerce
    └── nilx.one                    protocol / ecosystem branch
        └── 0x1                     protocol product
```

Personal projects owned by `0x0sky` remain outside the corporate graph unless explicitly declared otherwise.

---

## What we build

| Project | Purpose |
|---|---|
| [**mind**](https://github.com/aiaiaiai-tech/mind) | Versioned, machine-readable organization context. The aiaiaiai tech. specialization of the neutral [`0x0sky/mind`](https://github.com/0x0sky/mind) contract. |
| [**mind-web**](https://github.com/aiaiaiai-tech/mind-web) | GitHub-native spatial projection of an identity and its `mind`, built in Rust/WASM with a WebGPU renderer and semantic fallback. |
| [**0xda-market**](https://github.com/0xda-market) | Provider-agnostic digital commerce and brokerage infrastructure. |
| [**0x1**](https://github.com/nilx-one/0x1) | Protocol product developed inside the `nilx.one` branch of the ecosystem. |

The projects share parent-level engineering principles where their semantics genuinely overlap, while remaining independently owned at the repository and product boundary.

---

## `mind` and `mind-web`

A **mind** is a versioned source of structured identity and organization context. It is authored, reviewable, diffable, and designed to be consumed by both people and software.

**mind-web** is not that source. It is the live spatial projection: GitHub provides provider identity and repository state, the selected `mind` provides authored context, and the client turns those inputs into an explorable world.

```text
GitHub identity + selected mind
             │
             ▼
          mind-web
             │
             ▼
      explorable world
```

This separation matters: source data stays independently versioned; visualization and interaction can evolve without redefining organizational truth.

---

## Principles

- **Contracts over assumptions.** Important behavior should be encoded in schemas, invariants, tests, and versioned state.
- **Provider-agnostic cores.** Domain logic should not be owned by a particular vendor, interface, transport, or deployment platform.
- **Project boundaries stay real.** Shared architecture belongs at the parent level only when the semantics are actually shared.
- **Local-first where it matters.** Browser-native inference and client-side computation are preferred when they improve privacy, latency, or ownership without distorting the system design.
- **Public systems should be inspectable.** Architecture, status, limitations, and ownership should be visible in the repositories that implement them.
- **Verified behavior over claims.** We document what is built and proven, not what a roadmap merely intends to become.

---

## Status

`mind` and `mind-web` are under active development as the organization-context and spatial-interface foundations of the ecosystem. `0xda-market` and `0x1` evolve as independent child products with their own domain contracts.

The canonical public identity for the parent organization is [aiaiaiai.org](https://aiaiaiai.org).

<div align="center">

[Site](https://aiaiaiai.org) · [mind](https://github.com/aiaiaiai-tech/mind) · [mind-web](https://github.com/aiaiaiai-tech/mind-web) · [0xda-market](https://github.com/0xda-market) · [0x1](https://github.com/nilx-one/0x1) · [0x0sky](https://github.com/0x0sky)

</div>
