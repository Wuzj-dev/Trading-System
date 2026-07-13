# Deprecated: moved to SDS

This document has been moved to:
- [docs/sds/prediction-domain.md](../sds/prediction-domain.md)

The foundation directory only keeps stable baseline documents.

## Document Information
- Version: V1.0
- Status: Draft
- Domain: Prediction Domain
- Scope: Trading signal detection program

## 1. Purpose
Prediction Domain is the analysis layer responsible for modeling the probability of future market behavior.

This domain does not define market structure and does not define market state. It produces probabilistic directional conclusions for downstream decision layers.

中文说明：
- 这个域负责“未来行为概率建模”。
- 它回答的是未来更可能延续、反转，还是出现 trap。
- 它不负责定义结构，也不负责定义市场状态。

## 2. Domain Identity
This domain is the system boundary for future behavior probability modeling.

It sits between upstream analysis and final signal decision making.

中文说明：
- 这个域位于分析层中段，介于结构/流动性等上游分析结果和最终决策层之间。
- 它的核心是概率判断，不是结构定义。

## 3. Core Responsibilities

### 3.1 Continuation Probability Modeling
- Estimate whether the current market context is more likely to continue in the prevailing direction
- Expose continuation probability outputs for downstream consumption

中文说明：
- 这里关注的是“延续概率”。
- 它不是说一定会继续，而是给出延续的概率判断。

### 3.2 Reversal Probability Modeling
- Estimate whether the current market context is more likely to reverse
- Expose reversal probability outputs for downstream consumption

中文说明：
- 这里关注的是“反转概率”。
- 它不是直接下结论反转，而是输出反转发生的可能性。

### 3.3 Trap Probability Modeling
- Estimate whether the current market context is more likely to trap directional participants
- Expose trap probability outputs for downstream consumption

中文说明：
- 这里关注的是“trap 概率”。
- 它用来描述市场是否更像在诱导错误方向的参与者。

### 3.4 Probability Synthesis
- Combine relevant upstream analysis outputs into a coherent probability view
- Maintain a traceable probability summary for downstream use

中文说明：
- 这个域会把上游分析结果融合成统一的概率视角。
- 它输出的不是结构定义，而是概率摘要。

## 4. Domain Boundary

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
- Defining market structure
- Defining market state

### Boundary Rule
- This Domain owns only the responsibilities explicitly listed in this document.

## 5. Managed Objects

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

## 6. Runtime Status

### Status Items
- Health
- Ready

### Status Meaning
- Health indicates the current runtime condition of this Domain.
- Ready indicates whether this Domain can provide probability outputs.

### Query Rule
- Runtime status must be queryable.
- Downstream systems must not infer runtime status indirectly.

## 7. Input

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

## 8. Output

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

## 9. Responsibilities
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

## 10. Behavior Rules

### Must
- Must model probabilities from standardized upstream outputs only
- Must keep probability ownership isolated within this Domain
- Must provide probability outputs in a stable and traceable way
- Must not define market structure
- Must not define market state

### Must Not
- Must not own raw data intake
- Must not generate final trading signals
- Must not modify objects owned by other domains
- Must not perform trade execution
- Must not replace upstream analysis ownership

### Behavior Rule
- Behavior rules describe only observable system behavior.
- Behavior rules must not describe implementation methods.

## 11. Interaction

### Internal Interaction
- Reads standardized market data from Data Pipeline Domain
- Reads outputs from Market State Domain, Market Structure Domain, and Liquidity & Imbalance Domain
- Publishes probability outputs to Signal Decision Domain

### External Interaction
- Event Notify + Data Pull

### Interaction Rule
- Events notify state changes only.
- Business data must be pulled from the owner domain.
- Events must not carry business objects.

## 12. Output Guarantee

### Guarantee
This Domain guarantees that probability outputs are derived from standardized inputs and remain consistent with its ownership boundary.

### Guarantee Boundary
- Unless Policy states otherwise, guarantees apply only when Ready is true.

## 13. Forbidden

### Forbidden Behaviors
- Must not modify non-owned objects
- Must not introduce circular dependencies
- Must not bypass architecture dependency rules
- Must not redefine Foundation
- Must not mix Policy into SDS behavior definitions
- Must not describe implementation details

## 14. Future

### Future Extension
- Probability calibration refinement
- Multi-timeframe probability alignment
- Confidence scoring refinement
- Probability drift monitoring

### Future Rule
- Future items must not weaken the current stable design.
