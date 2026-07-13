# Prediction Domain SDS

## Document Information
- Version: V1.0
- Status: Draft
- Domain: Prediction Domain
- Scope: Signal Capture System

## 1. System Definition
### Identity
- This Domain is: Prediction Domain
- Goal: Model the probability of future market behavior
- System Role: Probability modeling layer between upstream analysis and final signal decision

### Description
- This Domain represents the stable behavior boundary for continuation, reversal, and trap probability modeling

### 中文理解
- 这个域负责“未来行为概率建模”。
- 它回答的是未来更可能延续、反转，还是出现 trap。
- 它不负责定义结构，也不负责定义市场状态。

### Constraint
- Do not describe implementation details.

## 2. Domain Boundary
### In Scope
- Model continuation probability
- Model reversal probability
- Model trap probability
- Maintain probability outputs
- Expose probability summary

### Out of Scope
- Raw market data intake
- Data validation and normalization
- Market state ownership
- Market structure ownership
- Liquidity and imbalance ownership
- Final signal generation
- Trade execution

### Boundary Rule
- This Domain owns only the responsibilities explicitly listed in this document.

## 3. Managed Objects
### Owned Objects
- Continuation Probability
- Reversal Probability
- Trap Probability
- Probability Context
- Probability Summary

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
- Ready indicates whether this Domain can provide probability outputs.

### Query Rule
- Runtime status must be queryable.
- Downstream systems must not infer runtime status indirectly.

## 5. Input
### Accepted Inputs
- Market state outputs
- Market structure outputs
- Liquidity and imbalance outputs
- Upstream change notifications
- Configuration updates
- Runtime control requests
- Trace lookup requests

### Input Rule
- Input must be interpreted only within the Domain boundary.
- Input must come from upstream owned outputs only.
- Input must not include raw OHLC, raw tick data, or direct ingestion internals as primary prediction sources.

## 6. Output
### Produced Outputs
- Continuation probability
- Reversal probability
- Trap probability
- Probability summary
- Probability change notification
- Runtime status

### Output Rule
- Output must remain consistent with Domain responsibilities and ownership rules.

### Output Schema Rule
- A probability output must include the current scope.
- A probability output must include continuation probability.
- A probability output must include reversal probability.
- A probability output must include trap probability.
- A probability output must include confidence.
- A probability output must include version metadata.
- A probability output must include freshness metadata when runtime status is not Ready.

## 7. Responsibilities
### Long-Term Responsibilities
- Model Continuation Probability
- Model Reversal Probability
- Model Trap Probability
- Synthesize Probability View
- Maintain Probability Summary
- Notify Changes
- Provide Read Access
- Expose Runtime Visibility

### Responsibility Rule
- Responsibilities must be written as verb phrases.
- Responsibilities must remain stable over time.

## 8. Behavior Rules
### Must
- Must model probabilities from standardized upstream outputs only
- Must keep probability ownership isolated within this Domain
- Must provide probability outputs in a stable and traceable way
- Must not define market structure
- Must not define market state
- Must not own liquidity zones

### Must Not
- Must not own raw data intake
- Must not generate final trading signals
- Must not modify objects owned by other domains
- Must not perform trade execution
- Must not replace upstream analysis ownership
- Must not calculate sweep, BOS, or MSS
- Must not calculate trend from raw OHLC

### Behavior Rule
- Behavior rules describe only observable system behavior.
- Behavior rules must not describe implementation methods.

## 9. System Policy
### Current Policy
- Probability calibration refinement: configurable
- Confidence scoring refinement: configurable
- Multi-timeframe probability alignment: configurable

### Policy Rule
- Policy may change across versions.
- Policy must not rewrite the SDS behavior definition.

### Policy Source
- YAML
- Configuration file
- Version policy document

## 10. Interaction
### Internal Interaction
- Reads outputs from Market State Domain, Market Structure Domain, and Liquidity & Imbalance Domain
- Publishes probability outputs to Signal Decision Domain

### External Interaction
- Event Notify + Data Pull

### Interaction Rule
- Events notify state changes only.
- Business data must be pulled from the owner domain.
- Events must not carry business objects.

## 11. Output Guarantee
### Guarantee
- This Domain guarantees that probability outputs are derived from standardized inputs and remain consistent with its ownership boundary.

### Guarantee Boundary
- Unless Policy states otherwise, guarantees apply only when Ready is true.

## 12. Forbidden
- Must not modify non-owned objects
- Must not introduce circular dependencies
- Must not bypass architecture dependency rules
- Must not redefine Foundation
- Must not mix Policy into SDS behavior definitions
- Must not describe implementation details

## 13. Future
### Future Extension
- Probability calibration refinement
- Confidence scoring refinement
- Probability drift monitoring

### Future Rule
- Future items must not weaken the current stable design.
