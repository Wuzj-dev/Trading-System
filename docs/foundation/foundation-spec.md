# Foundation Spec

## Document Information
- Version: V1.0
- Status: Freeze
- Scope: Repository-wide foundation for all domain specifications and policy documents.

## 1. Purpose
This document defines the stable system-level principles that all other design documents must follow.

## 2. Identity
The Foundation is the immutable reference for system design.

## 3. Scope
### In Scope
- System-level principles
- Design boundaries
- Ownership rules
- Event and interaction rules
- Runtime rules
- Change and freeze rules

### Out of Scope
- Domain-specific behavior
- Configurable runtime values
- Implementation details
- Framework, language, data structure, and algorithm choices

## 4. Core Principles
- Stable design takes priority over convenience.
- Configurable rules must be managed independently from SDS.
- SDS describes behavior only.
- Policy may change without rewriting SDS.
- Every object must have exactly one owner.
- Non-owner domains may read but must not modify.
- Event delivery is for notification only.
- Business data must be pulled from the owner domain.

## 5. Terminology
- **Foundation**: Stable system-level principles and constraints.
- **SDS**: AI-executable specification that describes domain behavior.
- **Policy**: Versioned and configurable rules that may change over time.
- **ADR**: Architecture decision record used to approve foundation changes.
- **Owner**: The only domain allowed to manage a given object.
- **Runtime**: The current operational state of a domain.

## 6. Design Rules
- Foundation must remain stable over time.
- Foundation changes require ADR.
- Foundation must not be redefined by any domain.
- Foundation must be referenced by all domain specifications.

## 7. Relationship to SDS
- SDS must follow Foundation.
- SDS must not redefine Foundation.
- SDS must only describe observable behavior.

## 8. Relationship to Policy
- Policy stores configurable values, defaults, and version differences.
- Policy may evolve across versions.
- Policy must not overwrite stable SDS behavior definitions.

## 9. Relationship to ADR
- Any Foundation change must be recorded in ADR.
- ADR explains why a Foundation rule changes.
- ADR does not replace Foundation or SDS.

## 10. Relationship to Review
- Review checks whether Foundation, SDS, and Policy are aligned.
- Review confirms readiness for Freeze.

## 11. Forbidden
- Do not define implementation details.
- Do not mix stable behavior with configurable policy.
- Do not allow multiple owners for the same object.
- Do not allow cyclic dependencies.
- Do not allow cross-layer direct access that violates architecture rules.

## 12. Future
- Future additions must not weaken the current stable design.
- Future items should be added only through controlled review.

