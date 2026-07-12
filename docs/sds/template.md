# Domain SDS Template

## Document Information
- Version: V1.0
- Status: Draft / Freeze / Active
- Purpose: Standard template for writing domain SDS documents.

## Writing Rule
This template defines what a Domain SDS must contain and how it must be written.

## 1. System Definition
### Identity
- This Domain is: `[Domain Name]`
- Goal: `[What this Domain exists to achieve]`
- System Role: `[Role in the overall system]`

### Description
- This Domain represents: `[Stable identity statement]`

### Constraint
- Do not describe implementation details.
- Do not describe programming language, framework, data structure, algorithm, or implementation method.

## 2. Domain Boundary
### In Scope
- `[Behavior or responsibility included in this Domain]`

### Out of Scope
- `[Behavior owned by other Domains]`

### Boundary Rule
- This Domain owns only the responsibilities explicitly listed in this document.

## 3. Managed Objects
### Owned Objects
- `[Object 1]`
- `[Object 2]`

### Ownership Rule
- Each object must have exactly one Owner Domain.
- Non-owner Domains may read the object only.
- Non-owner Domains must not modify the object.

## 4. Runtime Status
### Status Items
- Health
- Ready

### Status Meaning
- Health indicates the current runtime condition of this Domain.
- Ready indicates whether this Domain can provide its external service.

### Query Rule
- Runtime status must be queryable.
- Downstream systems must not infer runtime status indirectly.

## 5. Input
### Accepted Inputs
- `[Input Type 1]`
- `[Input Type 2]`

### Input Rule
- Input must be interpreted only within the Domain boundary.

## 6. Output
### Produced Outputs
- `[Output Type 1]`
- `[Output Type 2]`

### Output Rule
- Output must remain consistent with Domain responsibilities and ownership rules.

## 7. Responsibilities
### Long-Term Responsibilities
- Receive Data
- Validate Data
- Maintain Runtime
- Generate Logs
- Notify Changes
- Provide Read Access

### Responsibility Rule
- Responsibilities must be written as verb phrases.
- Responsibilities must remain stable over time.

## 8. Behavior Rules
### Must
- Must `[behavior statement]`
- Must `[behavior statement]`

### Must Not
- Must not `[forbidden behavior statement]`
- Must not `[forbidden behavior statement]`

### Behavior Rule
- Behavior rules describe only observable system behavior.
- Behavior rules must not describe implementation methods.

## 9. System Policy
### Current Policy
- Default value: `[value]`
- Configurable parameter: `[parameter]`
- Version-specific rule: `[rule]`

### Policy Rule
- Policy may change across versions.
- Policy must not rewrite the SDS behavior definition.

### Policy Source
- YAML
- Configuration file
- Version policy document

## 10. Interaction
### Internal Interaction
- `[Interaction with other Domains]`

### External Interaction
- `[Interaction with external systems]`
- Event Notify + Data Pull

### Interaction Rule
- Events notify state changes only.
- Business data must be pulled from the owner domain.
- Events must not carry business objects.

## 11. Output Guarantee
### Guarantee
- This Domain guarantees `[guarantee statement]`

### Guarantee Boundary
- Unless Policy states otherwise, guarantees apply only when Ready is true.

## 12. Forbidden
### Forbidden Behaviors
- Must not modify non-owned objects.
- Must not introduce circular dependencies.
- Must not bypass architecture dependency rules.
- Must not redefine Foundation.
- Must not mix Policy into SDS behavior definitions.
- Must not describe implementation details.

## 13. Future
### Future Extension
- `[future capability]`

### Future Rule
- Future items must not weaken the current stable design.

