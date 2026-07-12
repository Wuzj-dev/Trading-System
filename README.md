# Trading Signal Detection Documentation Repository

## Purpose
This repository is organized around a stable documentation-first workflow for the trading signal detection program, including system design, domain design, policy management, review gating, and implementation handoff.

## Core Rule
- Foundation defines immutable system-level principles.
- SDS defines stable domain behavior.
- Policy defines configurable runtime rules.
- ADR records architecture decisions.
- Review gates the transition from design to implementation.

## Directory Overview
- `docs/foundation/` for stable system-level specification.
- `docs/sds/` for domain system design specification templates and domain documents.
- `docs/policy/` for configurable rules and versioned policies.
- `docs/adr/` for architecture decision records.
- `docs/review/` for freeze and readiness checks.
- `src/` for implementation.
- `tests/` for verification.
- `configs/` for environment and runtime configuration.
- `.github/` for collaboration templates and workflow automation.

## Initial Version
- Version: V1.0
- Status: Freeze
- Scope: Trading signal detection design documentation

## Versioning
- `v1.0.0-docs`: initial repository documentation baseline
- `v1.0.1-foundation`: foundation specification baseline
- `v1.0.2-sds`: trading signal detection domain SDS baseline
- `v1.0.3-review`: freeze checklist baseline
- `v1.1.1-system-definition`: signal capture system definition baseline

## Key Design Documents
- [Signal Capture System Definition](docs/foundation/signal-capture-system-definition.md)
- [Domain Boundary Table](docs/foundation/domain-boundaries.md)
- [Domain Dependency Map](docs/foundation/domain-dependency-map.md)

## Working Sequence
1. Define or update Foundation.
2. Write or update SDS.
3. Separate configurable rules into Policy.
4. Record architecture decisions in ADR.
5. Run Freeze Review.
6. Start implementation only after Freeze is passed.

## Non-Negotiable Constraints
- Do not write implementation details into Foundation or SDS.
- Do not mix Policy into stable behavior definitions.
- Do not start domain design before Foundation is ready.
- Do not start implementation before Review and Freeze are complete.
