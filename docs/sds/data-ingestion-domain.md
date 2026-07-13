# Data Ingestion Domain SDS

## Document Information
- Version: V1.1
- Status: Draft
- Domain: Data Ingestion Domain
- Scope: Signal Capture System

## 1. System Definition
### Identity
- This Domain is: Data Ingestion Domain
- Goal: Receive, retrieve, process, and publish external market data into the system through one unified entry path
- System Role: Unique market data entry layer and unique market data producer

### Description
- This Domain represents the stable behavior boundary for market data intake, retrieval, historical initialization, real-time synchronization, data integrity maintenance, and unified event publication

### 中文理解
- 这个域是系统唯一的数据入口。
- 它也是系统唯一的市场数据生产者。
- 它负责接数据、维护窗口、做基础修复、发通知。
- 它不做行情解释，也不做交易决策。

### Constraint
- Do not describe implementation details.

## 2. Domain Boundary
### In Scope
- Receive external market data
- Retrieve external market data on internal request
- Parse and validate market data
- Normalize OHLC and related market payloads
- Standardize timestamps
- Map source payloads into internal data models
- Maintain rolling windows
- Historical initialization
- Bootstrap lifecycle
- Real-time synchronization
- Historical and realtime merge
- Data integrity maintenance
- Data publication
- Service readiness maintenance
- Health monitoring

### Out of Scope
- Data caching ownership
- Data persistence ownership
- Data query ownership
- Replay ownership
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
- Ready State
- Health State
- Bootstrap State

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

### 中文理解
- `Ready` 不是进程启动了就算 ready，而是已经具备稳定提供市场数据服务的能力。
- `Health` 用来描述整体运行健康度，不只是 websocket 是否在线。
- `Recovering` 表示正在补数据、重连或修复窗口。

### Query Rule
- Runtime status must be queryable.
- Downstream systems must not infer runtime status indirectly.

### Ready Conditions
- Websocket connection must be established for enabled realtime sources
- REST access must be available for required retrieval paths
- All configured symbols must complete initialization
- The first valid K-line snapshot must be available before the domain is considered ready

### 中文理解
- `Ready` 前不能假定这个域已经可用。
- 至少要先连上实时源、能拉历史源、完成 symbol 初始化，并准备好第一份可用窗口。

## 5. Input
### Accepted Inputs
- External market data
- Internal market data retrieval requests
- Recovery requests
- Configuration updates
- Runtime control requests
- Replay or backfill commands

### Input Rule
- Input must be interpreted only within the Domain boundary.

## 6. Output
### Produced Outputs
- Standardized market data
- K-line snapshot notification
- Candle update notification
- Tick update notification
- Timeframe refresh notification
- Resync notification
- Connection status notification
- Data quality notification
- Runtime status

### Output Rule
- Output must remain consistent with Domain responsibilities and ownership rules.

## 7. Responsibilities
### Long-Term Responsibilities
- Receive Data
- Retrieve Data
- Process Data
- Normalize Data
- Maintain Rolling Windows
- Maintain Bootstrap
- Maintain Historical Initialization
- Maintain Real-Time Synchronization
- Maintain Data Integrity
- Publish Changes
- Provide Read Access
- Maintain Ready State
- Maintain Health State

### 中文理解
- `Receive Data`：接收外部实时市场数据。
- `Retrieve Data`：按内部需求拉取历史、初始化或缺失数据。
- `Process Data`：完成解析、校验、时间统一和内部模型映射。
- `Maintain Bootstrap`：负责系统启动初始化阶段的统一生命周期。
- `Publish Changes`：只负责广播统一通知，不指定接收者，也不等待响应。
- `Maintain Ready State`：持续判断当前是否已经具备稳定服务能力。
- `Maintain Health State`：持续跟踪连接、同步、处理和发布是否健康。

### Responsibility Rule
- Responsibilities must be written as verb phrases.
- Responsibilities must remain stable over time.

## 8. Behavior Rules
### Must
- Must accept market data from supported venues
- Must be the only domain that directly accesses external market data sources for its owned scope
- Must maintain fixed-size rolling windows
- Must retrieve historical, initialization, or missing data through approved upstream source paths
- Must merge historical and realtime data into one consistent owned market window
- Must keep window ownership isolated within this Domain
- Must publish change notifications when standardized data changes
- Must broadcast unified domain events without binding to a specific consumer
- Must provide standardized data through an owner read path
- Must maintain runtime Ready and Health states
- Must complete bootstrap before claiming readiness

### Must Not
- Must not interpret market meaning
- Must not detect structure
- Must not calculate market state
- Must not generate prediction outputs
- Must not generate final signals
- Must not own downstream cache or persistence responsibilities
- Must not require downstream domains to access market sources directly

### Behavior Rule
- Behavior rules describe only observable system behavior.
- Behavior rules must not describe implementation methods.

### 中文理解
- 这个域对系统内部来说，必须是唯一外部市场入口。
- 下游域不能绕过它去直接连交易所拿同一份数据。
- 这个域可以广播事件，但不负责指定“谁来消费”。

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
- Retrieve history or missing data through approved source access paths
- Publish standardized data and change notifications to downstream domains

### External Interaction
- Event Notify + Data Pull

### Interaction Rule
- Events notify state changes only.
- Business data must be pulled from the owner domain.
- Events must not carry business objects.

### 中文理解
- 事件只负责提醒“有变化发生了”。
- 真正的业务数据仍然要从 owner domain 读取。
- 下游是否消费事件，由下游自己决定。

## 11. Output Guarantee
### Guarantee
- This Domain guarantees stable market data entry, unified processing, and fixed-size rolling window maintenance for valid upstream inputs.

### Guarantee Boundary
- Unless Policy states otherwise, guarantees apply only when Ready is true.

### 中文理解
- 只有在 `Ready = true` 时，才能认为这个域已经具备稳定输出能力。
- `Ready` 之前的数据准备过程不能被当成稳定服务承诺。

## 12. Forbidden
- Must not modify non-owned objects
- Must not introduce circular dependencies
- Must not bypass architecture dependency rules
- Must not redefine Foundation
- Must not mix Policy into SDS behavior definitions
- Must not describe implementation details
- Must not return ownership of event consumption decisions
- Must not depend on downstream acknowledgement before publishing domain events

## 13. Future
### Future Extension
- More supported venues
- More supported symbols
- More timeframe options
- More complete recovery behavior
- More detailed data quality rules
- More lifecycle and readiness policies

### Future Rule
- Future items must not weaken the current stable design.
