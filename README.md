# epoch-attestor

## Epoch Attestation & Finalization Module

`epoch-attestor` implements the **attestation layer** of the Epoch protocol.

It is responsible for collecting, validating, and submitting attestations that assert the finality of decisions defined by `epoch-core`.

This module **does not** execute assets, settle funds, or perform cryptographic proof verification.  
Its role is to **observe, attest, and signal** — not to execute.

---

## Purpose

The Epoch protocol separates **decision finality** from **decision execution**.

`epoch-attestor` exists to:

- Allow independent actors (attestors) to attest to decisions
- Aggregate attestations off-chain or on-chain as appropriate
- Emit canonical finalization signals consumable by verifiers and gateways

This design enables **horizontal scalability**, **cross-chain compatibility**, and **fault isolation**.

---

## Responsibilities

`epoch-attestor` is responsible for:

- Attestor registration and identity management
- Submission of attestations for epoch decisions
- Enforcement of epoch lifecycle constraints (via `epoch-core`)
- Emission of finalization events once thresholds are met
- Providing a deterministic interface for relayers and verifiers

---

## Non-Responsibilities

This module explicitly does **not**:

- Perform cryptographic verification of aggregated proofs
- Execute asset transfers or contract calls
- Determine protocol rules or finality semantics
- Interact directly with destination chains

These concerns belong to **`epoch-verifier`** and **`epoch-gateway`**.

---

## Design Principles

### Protocol Conformance
All attestations must conform to schemas and invariants defined in `epoch-core`.

### Attestation, Not Execution
Attestors assert observations. They do not trigger irreversible actions.

### Aggregation Flexibility
Attestation aggregation may occur:

- Off-chain (preferred for performance)
- On-chain (for transparency or liveness guarantees)

Aggregation strategy is intentionally decoupled from protocol semantics.

### Relayer-Friendly
Finalization is emitted as deterministic events, optimized for:

- Indexers
- Relayers
- Cross-chain bridges

---

## Typical Workflow

1. An epoch decision is defined according to `epoch-core`
2. Attestors observe the decision outcome
3. Attestors submit attestations referencing the canonical decision hash
4. Attestations are aggregated until threshold conditions are met
5. A **Finalized** event is emitted
6. Downstream modules consume the finalization signal

---

## Interfaces

`epoch-attestor` exposes:

- Attestation submission interfaces
- Finalization events
- Attestor metadata and scoring (optional, implementation-specific)

It does **not** expose execution hooks.

---

## Security Model

- Assumes **Byzantine-fault tolerant** attestor sets
- Relies on downstream verification (`epoch-verifier`) for cryptographic validity
- Supports multiple trust models:
  - Weighted attestations
  - Reputation-based scoring
  - Committee-based finalization

The protocol does not assume a single trusted attestor.

---

## Relationship to Other Modules

- **epoch-core**  
  Defines epoch structure, decision identity, and finality semantics.

- **epoch-verifier**  
  Verifies that finalization claims are valid and cryptographically sound.

- **epoch-gateway**  
  Executes finalized decisions on destination systems.

`epoch-attestor` provides the **signal**, not the **authority**.

---

## Implementation Notes

- Chain-specific implementations (e.g. Solana Anchor programs) may live in subdirectories
- Off-chain aggregators are first-class citizens
- Multiple independent attestor implementations are encouraged

---

## Status

⚠️ **Developer Preview**

This module is under active development.  
Interfaces may evolve prior to a production release.

---

## Why This Design Works

- Matches real-world bridge & oracle architectures
- Avoids single-chain lock-in
- Enables ZK, BLS, or light-client verification downstream
- Cleanly supports ETFs, banking rails, and regulated flows
