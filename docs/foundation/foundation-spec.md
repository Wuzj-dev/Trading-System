# Foundation Spec

## Document Information
- Version: V1.0
- Status: Freeze
- Scope: Trading signal detection program

## 1. Purpose
This document defines the stable foundation rules for the trading signal detection program.

## 2. Identity
The Foundation is the immutable reference for all design documents in this repository.

## 3. Scope
### In Scope
- Stable design principles
- Ownership rules
- Runtime rules
- Event interaction rules
- Change control rules

### Out of Scope
- Trading strategy implementation
- Signal calculation implementation
- Programming language choices
- Framework choices
- Data structure choices
- Algorithm choices

## 4. Core Principles
- Stable design takes priority over implementation convenience.
- SDS describes behavior only.
- Policy stores configurable rules and version differences.
- Every object must have exactly one Owner.
- Non-owner domains may read only.
- Events notify state changes only.
- Business data must be pulled from the owner domain.

## 5. Design Rules
- Foundation must remain stable.
- Foundation changes require ADR.
- Foundation must not be redefined by Domain SDS.
- Foundation must be referenced by all domain documents.

## 6. Ownership Rules
- Each object has exactly one owner.
- Only the owner may manage the object lifecycle.
- No other domain may modify that object.

## 7. Runtime Rules
- Each domain must expose Health.
- Each domain must expose Ready.
- Runtime status must be queryable.
- Downstream systems must not infer runtime state indirectly.

## 8. Interaction Rules
- Event Notify + Data Pull is required.
- Events notify only.
- Business data must be read from the owner domain.
- Events must not carry business objects.

## 9. Change Rules
- Stable principles change only through controlled review.
- Any Foundation change requires ADR.
- Policy changes do not rewrite Foundation.

## 10. Forbidden
- Do not define implementation details.
- Do not mix configurable rules into stable principles.
- Do not create cyclic dependencies.
- Do not allow cross-layer direct access that violates architecture rules.

## 11. Future
- Future extensions must preserve the stable design boundary.

