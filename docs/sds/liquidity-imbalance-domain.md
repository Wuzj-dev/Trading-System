# Liquidity & Imbalance Domain SDS

## Document Information
- Version: V1.0
- Status: Draft
- Domain: Liquidity & Imbalance Domain
- Scope: Signal Capture System

## 1. System Definition
### Identity
- This Domain is: Liquidity & Imbalance Domain
- Goal: Identify where market liquidity is located and expose liquidity zones for downstream consumption
- System Role: Liquidity zone mapping layer

### Description
- This Domain represents the stable behavior boundary for liquidity zone mapping and liquidity concentration tracking

### 中文理解
- 这个域负责识别市场“流动性在哪里”以及“流动性被如何消耗”。
- 它只负责把流动性区域标出来。
- 它不解释结构事件，也不做预测。

### Constraint
- Do not describe implementation details.

## 2. Domain Boundary
### In Scope
- Recognize liquidity zone behavior
- Maintain liquidity map state
- Maintain liquidity zone state
- Expose liquidity zone outputs
- Track resting liquidity
- Track liquidity clusters
- Track EQH / EQL regions

### Out of Scope
- Raw market data intake
- Data validation and normalization
- Market state ownership
- Structure change ownership
- Prediction ownership
- Final signal generation
- Trade execution
- Sweep detection
- MSS detection
- BOS detection
- Trend judgment

### Boundary Rule
- This Domain owns only liquidity zone mapping responsibilities.

## 3. Managed Objects
### Owned Objects
- Liquidity Map
- Liquidity Zone
- Liquidity Cluster
- Resting Liquidity

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
- Ready indicates whether this Domain can provide liquidity zone outputs.

### Query Rule
- Runtime status must be queryable.
- Downstream systems must not infer runtime status indirectly.

## 5. Input
### Accepted Inputs
- Standardized market data
- Market structure outputs
- Market state outputs
- Upstream change notifications

### Input Clarification
- Standardized OHLC candles are the primary input.
- Structure outputs may be consumed only as context.
- Market state may be consumed only as environment context.

### Input Rule
- Input must be interpreted only within the Domain boundary.

## 6. Output
### Produced Outputs
- Liquidity zone update
- Liquidity map update
- Liquidity state update
- Liquidity zone notification
- Liquidity map snapshot

### Output Schema Rule
- A liquidity output must include liquidity zones.
- A liquidity output must include liquidity map state.
- A liquidity output must include dominant liquidity side when available.
- A liquidity output must include strength or confidence metadata.
- A liquidity output must include version metadata.
- A liquidity output must include freshness metadata when runtime status is not Ready.

### Output Rule
- Output must remain consistent with Domain responsibilities and ownership rules.

## 7. Responsibilities
### Long-Term Responsibilities
- Map Liquidity Zones
- Track Resting Liquidity
- Track Liquidity Clusters
- Track EQH / EQL Regions
- Notify Changes
- Provide Read Access

### Responsibility Clarification
- This Domain is a zone mapping layer.
- It does not own sweep, BOS, or MSS.
- It does not own trend or prediction.

### Responsibility Rule
- Responsibilities must be written as verb phrases.
- Responsibilities must remain stable over time.

## 8. Behavior Rules
### Must
- Must map existing liquidity zones from standardized inputs only
- Must keep liquidity zone ownership isolated within this Domain
- Must provide outputs in a stable and traceable way
- Must not interpret structure events as liquidity ownership

### Must Not
- Must not own raw data intake
- Must not generate final trading signals
- Must not modify objects owned by other domains
- Must not perform trade execution
- Must not detect sweep
- Must not detect MSS
- Must not detect BOS
- Must not calculate trend
- Must not predict future movement

### Behavior Rule
- Behavior rules describe only observable system behavior.
- Behavior rules must not describe implementation methods.

## 9. System Policy
### Current Policy
- Liquidity zone subtype refinement: configurable
- Imbalance confidence scoring: configurable
- Multi-timeframe liquidity alignment: configurable

### Policy Rule
- Policy may change across versions.
- Policy must not rewrite the SDS behavior definition.

### Policy Source
- YAML
- Configuration file
- Version policy document

## 10. Interaction
### Internal Interaction
- Reads standardized market data from Data Pipeline Domain
- Reads structure outputs from Market Structure Domain
- Publishes liquidity zone outputs to Prediction Domain and Signal Decision Domain

### Interaction Clarification
- Structure outputs may help locate liquidity, but structure ownership remains in Market Structure Domain.
- Prediction may consume liquidity outputs, but liquidity mapping remains owned here.

### External Interaction
- Event Notify + Data Pull

### Interaction Rule
- Events notify state changes only.
- Business data must be pulled from the owner domain.
- Events must not carry business objects.

## 11. Output Guarantee
### Guarantee
- This Domain guarantees that liquidity and imbalance outputs are derived from standardized inputs and remain consistent with its ownership boundary.

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
- Sweep quality scoring
- Liquidity zone subtype refinement
- Multi-timeframe liquidity alignment
- Resting liquidity confidence scoring
- EQH / EQL clustering refinement

### Future Rule
- Future items must not weaken the current stable design.
