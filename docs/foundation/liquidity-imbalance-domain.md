# Deprecated: moved to SDS

This document has been moved to:
- [docs/sds/liquidity-imbalance-domain.md](../sds/liquidity-imbalance-domain.md)

The foundation directory only keeps stable baseline documents.

## Document Information
- Version: V1.0
- Status: Draft
- Domain: Liquidity & Imbalance Domain
- Scope: Trading signal detection program

## 1. Purpose
Liquidity & Imbalance Domain is the analysis layer responsible for identifying where market liquidity is located and how it is consumed.

This domain does not decide final trade direction by itself. It produces liquidity and imbalance interpretations for downstream directional and decision layers.

中文说明：
- 这个域负责识别市场“流动性在哪里”以及“流动性被如何消耗”。
- 它更像一个“市场燃料地图系统”。
- 它不直接做最终买卖决策。

## 2. Domain Identity
This domain is the system boundary for liquidity mapping and imbalance interpretation.

It sits between market structure observation and directional decision making.

中文说明：
- 这个域处在“结构识别”之后、“最终决策”之前。
- 它的价值是把结构信息进一步转成流动性与失衡视角。

## 3. Core Responsibilities

### 3.1 Liquidity Mapping
- Identify liquidity pools and likely liquidity zones
- Track areas where liquidity is resting, clustering, or being targeted
- Expose liquidity map outputs for downstream consumption

中文说明：
- Liquidity mapping 就是把“哪里有流动性”标出来。
- 这里关注的是 buy-side / sell-side liquidity、订单聚集区、以及潜在吸引位。

### 3.2 Liquidity Consumption
- Detect when liquidity has been consumed or swept
- Track whether liquidity events are leaving meaningful market response
- Maintain a consumption-aware liquidity state

中文说明：
- “被消耗”通常表示市场触及了某个流动性区域后，相关挂单被成交、扫走或快速失衡穿透。
- 这个域要记住这种变化，而不是只看静态位置。

### 3.3 Sweep and Imbalance Pattern Detection
- Detect liquidity sweep behavior
- Detect FVG / IFVG patterns in relation to liquidity consumption
- Detect OB behavior in relation to price reaction and imbalance

中文说明：
- 这个域不仅看流动性本身，也看流动性被扫之后留下了什么结构痕迹。
- FVG / IFVG、OB 都是帮助理解“消耗后市场怎么走”的重要模式。

### 3.4 Imbalance Interpretation
- Detect buy-side and sell-side pressure imbalance
- Track whether one side is dominating the other side
- Expose imbalance state for downstream tendency analysis

中文说明：
- Imbalance 关注的是“买卖力量是否不对称”。
- 它不是简单看涨跌，而是看哪一边更强、更急、更失衡。

### 3.5 Pressure and Reaction Assessment
- Identify whether a liquidity event caused rejection, continuation, or absorption-like behavior
- Track the market reaction around key liquidity areas
- Preserve a traceable interpretation of the observed behavior

中文说明：
- 这个域不只看“碰到了哪里”，也看“碰到以后怎么反应”。
- 市场可能拒绝、延续，也可能表现出吸收特征。

## 4. Domain Boundary

### In Scope
- Recognize liquidity behavior
- Maintain liquidity map state
- Recognize imbalance behavior
- Maintain imbalance state
- Expose liquidity and imbalance outputs

### Out of Scope
- Raw market data intake
- Data validation and normalization
- Market state ownership
- Structure change ownership
- Final signal generation
- Trade execution

### Boundary Rule
- This Domain owns only liquidity and imbalance interpretation responsibilities.
- This Domain must not replace upstream structure ownership or downstream decision ownership.

## 5. Managed Objects

### Owned Objects
- Liquidity Map
- Liquidity Zone
- Imbalance State
- Imbalance Event

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
- Ready indicates whether this Domain can provide liquidity and imbalance outputs.

### Query Rule
- Runtime status must be queryable.
- Downstream systems must not infer runtime status indirectly.

## 7. Input

### Accepted Inputs
- Standardized market data
- Market structure outputs
- Market state outputs
- Upstream change notifications

### Input Rule
- Input must be interpreted only within the Domain boundary.

## 8. Output

### Produced Outputs
- Liquidity map update
- Liquidity sweep or consumption notification
- Imbalance state update
- Imbalance interpretation result

### Output Rule
- Output must remain consistent with Domain responsibilities and ownership rules.

## 9. Behavior Rules

### Must
- Must interpret liquidity and imbalance from standardized inputs only
- Must keep liquidity and imbalance ownership isolated within this Domain
- Must provide outputs in a stable and traceable way

### Must Not
- Must not own raw data intake
- Must not generate final trading signals
- Must not modify objects owned by other domains
- Must not perform trade execution

### Behavior Rule
- Behavior rules describe only observable system behavior.
- Behavior rules must not describe implementation methods.

## 10. Interaction

### Internal Interaction
- Reads standardized market data from Data Pipeline Domain
- Reads structure outputs from Market Structure Domain
- Publishes liquidity and imbalance outputs to Reversal / Continuation Domain and Signal Decision Domain

### External Interaction
- Event Notify + Data Pull

### Interaction Rule
- Events notify state changes only.
- Business data must be pulled from the owner domain.
- Events must not carry business objects.

## 11. Output Guarantee

### Guarantee
This Domain guarantees that liquidity and imbalance outputs are derived from standardized inputs and remain consistent with its ownership boundary.

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
- Liquidity zone subtype refinement
- Imbalance confidence scoring
- Multi-timeframe liquidity alignment
- Sweep quality scoring
- FVG / IFVG / OB interaction refinement

### Future Rule
- Future items must not weaken the current stable design.
