# ADR 0001: Versioning and Document Boundaries

## Status
Accepted

## Context
The repository needs a stable documentation workflow that separates foundation rules, domain behavior, configurable policy, and freeze review.

## Decision
- Use Foundation for stable system-level principles.
- Use SDS for stable domain behavior.
- Use Policy for configurable rules and version differences.
- Use Review for freeze gating.
- Use ADR for foundation-level decisions.

## Consequences
- Stable and configurable content remain separated.
- Versioning becomes easier to track.
- Future domain documents can follow the same structure.

