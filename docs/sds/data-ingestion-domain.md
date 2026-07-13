# Data Ingestion Domain SDS

## Document Information
- Version: V1.1
- Status: Draft
- Domain: Data Ingestion Domain
- Scope: Signal Capture System

## 1. System Definition
### Identity
- This Domain is: Data Ingestion Domain
- Goal: Receive external market data, maintain fixed-size rolling windows, and publish unified change notifications
- System Role: Unique market data entry layer

### Description
- This Domain represents the stable behavior boundary for market data intake, historical initialization, real-time synchronization, and data integrity maintenance

### 中文理解
- 这个域是系统唯一的数据入口。
- 它负责接数据、维护窗口、做基础修复、发通知。
- 它不做行情解释，也不做交易决策。

### Constraint
- Do not describe implementation details.

## 2. Domain Boundary
### In Scope
- Fetch market data
- Normalize OHLC
- Maintain rolling windows
- Historical initialization
- Real-time synchronization
- Data integrity maintenance
- Data publication

### Out of Scope
- Market interpretation
- Structure detection
- State calculation
- Prediction logic
- Signal generation
- Trade execution

### Boundary Rule
- This Domain owns only the responsibilities explicitly listed in this document.

## 3. Managed Objects
### Owned Objects
- Ingestion Event
- Intake Status
- Source Connection
- Input Stream
- Capture Runtime
- Rolling Window
- Window Metadata

### Ownership Rule
- Each object must have exactly one Owner Domain.
- Non-owner Domains may read the object only.
- Non-owner Domains must not modify the object.

## 4. Runtime Status
### Status Items
- Health
- Ready
- Degraded
- Recovering

### Status Meaning
- Health indicates the current runtime condition of this Domain.
- Ready indicates whether this Domain can provide market data entry service.
- Degraded indicates the domain can still serve data, but data quality or freshness is reduced.
- Recovering indicates the domain is actively backfilling, repairing, or re-synchronizing data.

### Query Rule
- Runtime status must be queryable.
- Downstream systems must not infer runtime status indirectly.

## 5. Input
### Accepted Inputs
- External market data
- Ingestion change notifications
- Recovery requests
- Configuration updates
- Runtime control requests
- Replay or backfill commands

### Input Rule
- Input must be interpreted only within the Domain boundary.

## 6. Output
### Produced Outputs
- Standardized market data
- Candle update notification
- Tick update notification
- Timeframe refresh notification
- Resync notification
- Runtime status

### Output Rule
- Output must remain consistent with Domain responsibilities and ownership rules.

## 7. Responsibilities
### Long-Term Responsibilities
- Receive Data
- Normalize Data
- Maintain Rolling Windows
- Maintain Historical Initialization
- Maintain Real-Time Synchronization
- Maintain Data Integrity
- Publish Changes
- Provide Read Access

### Responsibility Rule
- Responsibilities must be written as verb phrases.
- Responsibilities must remain stable over time.

## 8. Behavior Rules
### Must
- Must accept market data from supported venues
- Must maintain fixed-size rolling windows
- Must keep window ownership isolated within this Domain
- Must publish change notifications when standardized data changes
- Must provide standardized data through an owner read path

### Must Not
- Must not interpret market meaning
- Must not detect structure
- Must not calculate market state
- Must not generate prediction outputs
- Must not generate final signals

### Behavior Rule
- Behavior rules describe only observable system behavior.
- Behavior rules must not describe implementation methods.

## 9. System Policy
### Current Policy
- Supported market list: configurable
- Supported symbol set: configurable
- Supported timeframe set: configurable
- Backfill policy: configurable
- Recovery policy: configurable

### Policy Rule
- Policy may change across versions.
- Policy must not rewrite the SDS behavior definition.

### Policy Source
- YAML
- Configuration file
- Version policy document

## 10. Interaction
### Internal Interaction
- Receive data through supported venue adapters
- Publish standardized data and change notifications to downstream domains

### External Interaction
- Event Notify + Data Pull

### Interaction Rule
- Events notify state changes only.
- Business data must be pulled from the owner domain.
- Events must not carry business objects.

## 11. Output Guarantee
### Guarantee
- This Domain guarantees stable market data entry and fixed-size rolling window maintenance for valid upstream inputs.

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
- More supported venues
- More supported symbols
- More timeframe options
- More complete recovery behavior

### Future Rule
- Future items must not weaken the current stable design.

