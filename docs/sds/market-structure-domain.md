# Market Structure Domain SDS

## Document Information
- Version: V1.0
- Status: Draft
- Domain: Market Structure Domain
- Scope: Signal Capture System

## 1. System Definition
### Identity
- This Domain is: Market Structure Domain
- Goal: Convert standardized market data into structure events
- System Role: Structure event boundary

### Description
- This Domain represents the stable behavior boundary for identifying structure changes, structure breaks, structure shifts, and continuation conditions in market behavior

### 中文理解
- 这个域只负责“识别结构变化”，不负责最终下单。
- 它关注的是市场结构是否发生了破坏、转折、延续或重新组织。
- 它是结构分析专用窗口，不是执行窗口，也不是数据入口。
- 它只负责把价格行为转换成结构事件，不负责解释环境或预测未来。

### Constraint
- Do not describe implementation details.

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
- Trend judgment
- Market regime judgment
- Liquidity zone ownership
- Probability ownership
- Final signal generation
- Trade execution

### Boundary Rule
- This Domain owns only the responsibilities explicitly listed in this document.

## 3. Managed Objects
### Owned Objects
- Structure Pattern
- Structure State
- Structure Transition
- Structure Event

### Concept Ownership Rule
- Sweep must be owned by this Domain.
- BOS must be owned by this Domain.
- MSS must be owned by this Domain.
- This Domain must not own Trend, Market Regime, Volatility, Session, Liquidity Zones, or Probability.

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
- Upstream change notifications
- Configuration updates
- Runtime control requests

### Input Rule
- Input must be interpreted only within the Domain boundary.
- Input must be standardized market data or upstream notifications only.
- Input must not be raw ingestion internals, liquidity maps, or probability outputs as primary structure sources.

## 6. Output
### Produced Outputs
- Structure recognition result
- Structure state update
- Structure change notification

### Output Ownership Rule
- Structure output owns Sweep, BOS, and MSS.
- Structure output must not own Trend, Liquidity Zones, or Probability.

### Output Rule
- Output must remain consistent with Domain responsibilities and ownership rules.

## 7. Responsibilities
### Long-Term Responsibilities
- Recognize Structure Changes
- Maintain Structure State
- Track Structure Breaks
- Track Structure Shifts
- Track Continuation-Aligned Structure Behavior
- Notify Changes
- Provide Read Access

### Responsibility Clarification
- Sweep detection belongs here.
- BOS detection belongs here.
- MSS detection belongs here.
- Structure events are the only structure-level concepts owned here.

### Responsibility Rule
- Responsibilities must be written as verb phrases.
- Responsibilities must remain stable over time.

## 8. Behavior Rules
### Must
- Must recognize structure changes from standardized inputs only
- Must keep structure ownership isolated within this Domain
- Must provide structure outputs in a stable and traceable way
- Must own Sweep, BOS, and MSS as structure concepts
- Must not reinterpret trend, liquidity zones, or probability outputs

### Must Not
- Must not own raw data intake
- Must not generate final trading signals
- Must not modify objects owned by other domains
- Must not perform trade execution
- Must not own Trend, Market Regime, Volatility, Session, Liquidity Zones, or Probability

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

### Interaction Clarification
- Market State may provide environment context, but Structure must still own its own structure events.
- Structure must not depend on prediction outputs to define Sweep, BOS, or MSS.

### External Interaction
- Event Notify + Data Pull

### Interaction Rule
- Events notify state changes only.
- Business data must be pulled from the owner domain.
- Events must not carry business objects.

## 11. Output Guarantee
### Guarantee
- This Domain guarantees that structure outputs are derived from standardized inputs and remain consistent with its ownership boundary.

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
- Structure subtype refinement
- Multi-timeframe structure alignment
- Structure confidence scoring

### Future Rule
- Future items must not weaken the current stable design.
