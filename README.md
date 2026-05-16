# LumenGuard

[![Rust](https://img.shields.io/badge/Rust-2024-DEA584?logo=rust)](https://www.rust-lang.org/)
[![Stellar](https://img.shields.io/badge/Stellar-ecosystem-blue?logo=stellar)](https://www.stellar.org/)
[![Status: Early Development](https://img.shields.io/badge/status-Early%20Development-yellow?style=flat)](https://github.com/placeholder/LumenGuard)

**Modular, open-source custody and signing infrastructure for the Stellar ecosystem.**

LumenGuard provides secure transaction orchestration, policy-based authorization, audit logging, and signer isolation for institutional Stellar applications.

> **Early Development:** Interfaces and security guarantees evolve as components mature and receive additional review.

---

## Vision

LumenGuard’s long-term goal is to become **foundational security infrastructure for the Stellar ecosystem**.

Over time, the project aims to establish clear, composable primitives for custody, authorization, signing, and auditability—so organizations can adopt a consistent security posture across anchors, treasury systems, custodial applications, and institutional-grade Stellar deployments.

---

## Problem

Operational security for blockchain custody frequently fails in predictable ways:

- **Insecure hot wallets**
  Private keys are handled directly in general-purpose application runtimes, CI pipelines, or ad-hoc operational scripts.

- **Custom signing scripts**
  Each team implements signing orchestration differently, producing fragile flows that are difficult to test, review, and audit.

- **Poor auditability**
  Many systems lack durable, reviewable records of *who requested what*, *which policy was applied*, and *why a signing decision was made*.

- **Lack of policy enforcement**
  Authorization rules (allowed operations, memo requirements, account permissions, time windows, and workflow constraints) are often implicit or scattered.

- **Fragmented custody tooling**
  Custody is implemented as one-off stacks rather than modular infrastructure, making improvements expensive and inconsistent.

---

## Solution

LumenGuard provides a security-engineering approach to custody and signing infrastructure:

- **Isolated signing services**
  Signing runs in dedicated components with explicit trust boundaries to reduce key exposure.

- **Policy engines**
  Centralized, auditable authorization decisions that gate signing requests based on explicit rules.

- **Secure transaction orchestration**
  Controlled workflows for constructing, validating, and submitting Stellar transactions.

- **Audit logging**
  Records requests, decisions, and signing outcomes to support operational review and incident response.

- **Multi-sig support**
  Coordination primitives for multi-signature authorization flows.

- **Modular infrastructure primitives**
  Components separated by responsibility so organizations can adopt, test, and evolve parts of the system without rewriting everything.

---

## Architecture Overview

Major components:

- **API service**: entry point for custody requests and orchestration workflows
- **Signer service**: signing execution in an isolated component
- **Policy engine**: authorization rules and decision output
- **Stellar adapter**: Stellar-specific transaction mapping and integration
- **Audit system**: logging of requests, policy decisions, and signing events

### ASCII Architecture Diagram

```text
                +-----------------------------+
                |  Clients / Ops / Anchors   |
                |  (custody & signing reqs)  |
                +--------------+--------------+
                               |
                               v
                        +------+------+
                        | API Service |
                        | (orchestration)
                        +------+------+
                               |
                               | policy check (request + context)
                               v
                        +------+------+
                        | Policy Engine |
                        +------+------+
                               |
                               | validated intent
                               v
                        +------+------+
                        | Stellar Adapter |
                        | (Tx mapping)    |
                        +------+------+
                               |
                               | signing request (isolated)
                               v
                        +------+------+
                        | Signer Service |
                        | (isolation)     |
                        +------+------+
                               |
                               | signing result
                               v
                        +------+------+
                        | Audit System |
                        | (records)     |
                        +--------------+
```

---

## Monorepo Structure

LumenGuard is a Rust **Cargo workspace** organized under `crates/`.

```text
LumenGuard/
  Cargo.toml                 # workspace definition
  crates/
    api/                     # API service / orchestration
    signer/                  # signer service (isolated execution)
    stellar-adapter/         # Stellar integration layer
    policy-engine/           # policy evaluation and authorization
    audit/                   # audit logging system
    config/                  # configuration primitives
    shared/                  # shared types and utilities
```

---

## Features

### Secure signing
- Dedicated signing execution boundary
- Controlled orchestration flow separated from key handling

### Policy-based authorization
- Explicit authorization decisions before signing
- Centralized policy evaluation
- Auditable policy outcomes

### Modular custody primitives
- Separation of concerns across orchestration, policy, signing, and audit
- Infrastructure primitives intended for incremental adoption

### Audit trails
- Request → decision → signing outcome traceability
- Event records designed for operational review

### Multi-sig orchestration
- Workflow primitives for multi-signature authorization flows

### Developer-first APIs
- Rust-native interfaces intended to be composable and reviewable

### Rust-native architecture
- Memory safety through Rust
- Strong typing for representing security-critical boundaries

---

## Security Philosophy

LumenGuard is built with a security-engineering mindset:

- **Memory safety**: rely on Rust guarantees to reduce classes of memory errors.
- **Signer isolation**: keep signing execution separate from orchestration and client-facing logic.
- **Least privilege**: minimize the capabilities of each component.
- **Defense in depth**: combine policy, orchestration controls, signing boundaries, and auditability.
- **Reproducibility**: prefer deterministic patterns where practical to improve review and verification.
- **Explicit trust boundaries**: design components so that security review has clear targets.

---

## Threat Model

LumenGuard aims to protect against:

- **Private key exfiltration**
  Reduce the likelihood that signing keys are accessible to general application components.

- **Unauthorized transaction signing**
  Prevent signing from occurring unless authorization and workflow constraints are satisfied.

- **Operational misuse**
  Enforce consistent rules and provide audit evidence that supports monitoring and investigation.

- **Tampering and lack of accountability**
  Improve traceability of custody actions across the request and decision pipeline.

- **Ad-hoc, unreviewable signing procedures**
  Replace bespoke scripts with structured, testable, auditable infrastructure primitives.

This project is evolving; security properties will be documented and validated as components mature.

---

## Quick Start

### Prerequisites
- Rust toolchain (stable)

### Build
```bash
cargo build
```

### Run components (workspace crates)
```bash
cargo run -p api
cargo run -p signer
```

### Development notes
- Keep signing execution isolated in your deployment topology.
- Treat policy configuration and audit storage as security-critical components.

---

## Roadmap

A realistic phased approach:

1. **MVP signing service**
   - Establish the core signing workflow and service boundaries
   - Define signing request/response contracts

2. **Policy engine**
   - Implement baseline authorization rules
   - Produce auditable policy decisions

3. **Multi-sig orchestration**
   - Coordinate multi-signature workflows
   - Ensure traceable outcomes across signing steps

4. **HSM integrations**
   - Integrate hardware-backed key custody
   - Preserve signing isolation and explicit trust boundaries

5. **MPC support**
   - Enable threshold custody models
   - Align policy evaluation and audit with MPC workflows

6. **Observability tooling**
   - Security-aligned structured logging
   - Operational metrics and tracing patterns

---

## Contributing

Contributions are welcome from engineers and security practitioners.

Long-term, LumenGuard aims to:
- keep interfaces stable where security depends on them,
- document security-critical decisions and invariants,
- and build a shared foundation for safer custody and signing on Stellar.

Recommended contribution areas:
- policy engine rule modeling and tests
- audit event schemas and integrity validation
- signer workflow hardening and integration tests
- Stellar adapter correctness and edge case coverage

---

## License

Licensed under **Apache-2.0**.

