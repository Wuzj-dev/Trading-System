# Deprecated: moved to SDS

This document has been moved to:
- [docs/sds/market-structure-domain.md](../sds/market-structure-domain.md)

The foundation directory only keeps stable baseline documents.

## Document Information
- Version: V1.0
- Status: Draft
- Domain: Market Structure Domain
- Scope: Trading signal detection program

## 1. System Definition
### Identity
- This Domain is: Market Structure Domain
- Goal: Recognize stable and meaningful market structure changes for downstream analysis
- System Role: Structure recognition layer between standardized market data and final signal decision

### Description
This Domain represents the system boundary responsible for identifying structure changes, structure breaks, structure shifts, and continuation conditions in market behavior.

中文说明：
- 这个域只负责“识别结构变化”，不负责最终下单。
- 它关注的是市场结构是否发生了破坏、转折、延续或重新组织。
- 它是结构分析专用窗口，不是执行窗口，也不是数据入口。
- 这是 ICT 核心里的结构分析窗口。

## 1.1 Window Outline
### Purpose
识别市场“结构变化”

### Included Capabilities
- BOS（结构延续）
- MSS（结构转向）
- swing high / low

### Excluded Responsibilities
- 不判断交易
- 不生成信号

### Essence
趋势与结构识别系统

### Constraint
- Do not describe implementation details.
- Do not describe programming language, framework, data structure, algorithm, or implementation method.

## 2. Domain Boundary
### In Scope
- Recognize market structure change
- Maintain structure state
- Detect structure break behavior
- Detect structure shift behavior
- Detect continuation-aligned structure behavior
- Expose structure outputs for downstream domains

### Out of Scope
- Raw market data intake
- Data validation and normalization
- Liquidity and imbalance interpretation
- Reversal or continuation decision ownership
- Final signal generation
- Trade execution

### Boundary Rule
- This Domain owns only the responsibilities explicitly listed in this document.

## 3. Managed Objects
### Owned Objects
- Structure Pattern
- Structure State
- Structure Transition

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
- Ready indicates whether this Domain can provide structure analysis output.

### Query Rule
- Runtime status must be queryable.
- Downstream systems must not infer runtime status indirectly.

## 5. Input
### Accepted Inputs
- Standardized market data
- Market state outputs
- Upstream change notifications

### Input Rule
- Input must be interpreted only within the Domain boundary.

## 6. Output
### Produced Outputs
- Structure recognition result
- Structure state update
- Structure change notification

### Output Rule
- Output must remain consistent with Domain responsibilities and ownership rules.

## 7. Responsibilities
### Long-Term Responsibilities
- Recognize structure changes
- Maintain structure state
- Track structure breaks
- Track structure shifts
- Track continuation-aligned structure behavior
- Notify downstream domains of structure changes
- Provide read access to structure outputs

### Responsibility Rule
- Responsibilities must be written as verb phrases.
- Responsibilities must remain stable over time.

## 8. Behavior Rules
### Must
- Must recognize structure changes from standardized inputs only
- Must keep structure ownership isolated within this Domain
- Must provide structure outputs in a stable and traceable way

### Must Not
- Must not own raw data intake
- Must not generate final trading signals
- Must not modify objects owned by other domains
- Must not perform trade execution

### Behavior Rule
- Behavior rules describe only observable system behavior.
- Behavior rules must not describe implementation methods.

## 9. System Policy
### Current Policy
- Default value: Structure recognition is enabled for supported markets
- Configurable parameter: Supported symbol and timeframe set
- Version-specific rule: Structure definitions may expand by policy, but ownership rules must remain unchanged

### Policy Rule
- Policy may change across versions.
- Policy must not rewrite the SDS behavior definition.

### Policy Source
- YAML
- Configuration file
- Version policy document

## 10. Interaction
### Internal Interaction
- Reads standardized data from Data Pipeline Domain
- Publishes structure changes to Signal Decision Domain
- Cooperates with Market State Domain as a peer analysis domain

### External Interaction
- Event Notify + Data Pull

### Interaction Rule
- Events notify state changes only.
- Business data must be pulled from the owner domain.
- Events must not carry business objects.

## 11. Output Guarantee
### Guarantee
This Domain guarantees that structure outputs are derived from standardized inputs and remain consistent with its ownership boundary.

### Guarantee Boundary
- Unless Policy states otherwise, guarantees apply only when Ready is true.

## 12. Forbidden
### Forbidden Behaviors
- Must not modify non-owned objects
- Must not introduce circular dependencies
- Must not bypass architecture dependency rules
- Must not redefine Foundation
- Must not mix Policy into SDS behavior definitions
- Must not describe implementation details

## 13. Future
### Future Extension
- Structure subtype refinement
- Multi-timeframe structure alignment
- Structure confidence scoring

### Future Rule
- Future items must not weaken the current stable design.
