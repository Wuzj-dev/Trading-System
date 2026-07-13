# Market State Domain SDS

## Document Information
- Version: V1.2
- Status: Freeze
- Domain: Market State Domain
- Scope: Signal Capture System

## 中文说明
Market State Domain 负责承接 `Data Pipeline Domain` 通过 owner read path 提供的标准化数据，定义当前市场处于什么状态。
它不负责原始接入，不负责最终交易信号，不负责执行动作。
它的职责是把可用数据解释成稳定的市场状态描述，并向下游提供一致的状态结论。

- 输入来自 `Data Pipeline Domain`
- 本域不拥有原始数据接入
- 本域不负责最终信号决策
- 本域回答的是“市场现在是什么状态”
- 本域只负责环境，不负责结构，不负责预测

## 1. System Definition
### Identity
- This Domain is: Market State Domain
- Goal: Interpret standardized market data from the Data Pipeline Domain and maintain the current market environment snapshot
- System Role: Environment classification boundary

### Description
- This Domain represents the stable behavior boundary for maintaining the current market environment, session context, and multi-timeframe synchronization from standardized upstream data

### 中文理解
- 这个域负责把标准化后的数据转成“市场状态”。
- 它回答的是市场处于趋势、震荡、扩张、收缩、强势、弱势等哪一类状态。
- 它不直接告诉系统该买还是该卖。
- 它不解释结构事件，也不解释流动性位置。

### Constraint
- Do not describe implementation details.
- Do not describe programming language, framework, data structure, algorithm, or implementation method.

## 2. Domain Boundary
### In Scope
- Interpret standardized market data
- Maintain current market environment state
- Maintain session context
- Maintain multi-timeframe synchronization state
- Detect market state transitions
- Update state confidence or validity signals
- Expose current state for downstream readers
- Publish market state change notifications
- Retain state history needed for traceable transitions
- Provide runtime visibility for state service behavior

### Out of Scope
- Raw market intake ownership
- Data validation and normalization
- Trade execution
- Position management
- Final signal generation
- Market structure ownership
- Liquidity and imbalance ownership
- Prediction ownership
- Direct modification of upstream pipeline-owned objects

### 中文说明
- 输入范围是来自数据管道的标准化市场数据，以及配置和运行控制类输入。
- 输出范围是市场状态、状态变化通知、状态查询结果和运行可见性信息。
- 任何最终交易级别的结论，不属于这个域。
- `Market State` 是市场当前环境的统一快照，不是结构事件，也不是概率结果。

### Boundary Rule
- This Domain owns only the responsibilities explicitly listed in this document.

## 3. Managed Objects
### Owned Objects
- Market State
- Session State
- Timeframe Synchronization State
- State Transition
- State Confidence
- State History
- State Snapshot
- State Change Record

### 中文说明
- `Market State`：当前市场状态的主对象。
- `Session State`：当前处于哪个 session，以及 session 对状态解释的上下文。
- `Timeframe Synchronization State`：不同周期之间是否同步、共振或分歧。
- `State Transition`：从一个状态切换到另一个状态的记录。
- `State Confidence`：当前状态判断的稳定程度或可信度。
- `State History`：最近一段时间的状态演化轨迹。
- `State Snapshot`：某一时刻的状态快照，便于下游读取。
- `State Change Record`：状态发生变化时留下的记录，便于追踪和审计。

### Ownership Rule
- Each object must have exactly one Owner Domain.
- Non-owner Domains may read the object only.
- Non-owner Domains must not modify the object.

### State Scope Rule
- Each market state object must belong to exactly one supported scope.
- A supported scope must be explicitly configured and stable.
- A supported scope must at minimum identify the market, symbol, and timeframe set being classified.
- A market state must not mix results from unsupported or ambiguous scopes.

### Concept Ownership Rule
- Trend must be owned by this Domain.
- Market Regime must be owned by this Domain.
- Volatility must be owned by this Domain.
- Session must be owned by this Domain.
- This Domain must not own Sweep, MSS, BOS, or Liquidity Zones.

## 4. Runtime Status
### Status Items
- Health
- Ready
- Degraded
- Updating

### Status Meaning
- Health indicates the current runtime condition of this Domain.
- Ready indicates whether this Domain can provide current market state service.
- Degraded indicates the domain can still serve state reads, but state freshness or confidence is reduced.
- Updating indicates the domain is actively re-evaluating or refreshing the market state.

### 中文说明
- `Health` 说明服务本身是否正常。
- `Ready` 说明当前是否可以对外提供有效的市场状态服务。
- `Degraded` 说明还能服务，但状态可能偏旧、偏弱或待修复。
- `Updating` 说明市场状态正在刷新或重算，暂时不应被视为完全稳定。
- session 切换和周期分歧可能触发 `Updating` 或 `Degraded`。

### Query Rule
- Runtime status must be queryable.
- Downstream systems must not infer runtime status indirectly.

## 5. Input
### Accepted Inputs
- Standardized market data from Data Pipeline owner read path
- Pipeline change notifications
- Configuration updates
- Runtime control requests
- State refresh requests
- Audit or trace lookup requests

### Input Constraint
- The Domain may only consume standardized inputs from the Data Pipeline owner read path.
- The Domain must not consume raw OHLC directly from the ingestion layer.
- The Domain must not consume structural events, liquidity maps, or probability outputs as primary inputs.

### 中文说明
- 主输入必须来自 `Data Pipeline Domain` 的标准化输出。
- 标准化输出必须通过 owner read path 获取，不能直连内部缓存对象。
- 通知类输入用于提醒上游数据已经变化。
- 刷新类输入用于重新确认当前市场状态是否需要更新。

### Input Rule
- Input must be interpreted only within the Domain boundary.

## 6. Output
### Produced Outputs
- Market state
- Session state
- Timeframe synchronization state
- State transition notifications
- State snapshot
- State confidence
- Runtime status
- Trace or audit responses

### Output Ownership Rule
- Market state output owns trend, regime, volatility, and session context.
- Market state output must not own sweep, MSS, BOS, liquidity zones, or probability outputs.

### 中文说明
- `Market state` 是本域最主要的输出。
- `Session state` 说明当前市场处于哪个交易时段上下文。
- `Timeframe synchronization state` 说明不同周期是否同向、共振或分歧。
- `State transition notifications` 只表达状态变化，不承载完整原始业务对象。
- `State confidence` 让下游知道当前状态有多稳。

### Output Rule
- Output must remain consistent with Domain responsibilities and ownership rules.

### Output Schema Rule
- A market state output must include the current scope.
- A market state output must include the current trend state.
- A market state output must include the current session state.
- A market state output must include the current multi-timeframe synchronization state.
- A market state output must include confidence.
- A market state output must include version metadata.
- A market state output must include watermark metadata.
- A market state output must include the latest transition record when a state change has occurred.
- A market state output must not omit freshness-related metadata when runtime status is not Ready.

### 中文说明
- 市场状态输出不能只给一个结论词，必须带作用域和元数据。
- 版本和水位线是状态是否可用的重要组成部分。
- 发生过状态切换时，输出里必须能看见切换记录。

## 7. Responsibilities
### Long-Term Responsibilities
- Interpret Data
- Maintain State
- Maintain Session Context
- Maintain Multi-Timeframe Context
- Detect State Transition
- Update Confidence
- Publish State Changes
- Provide Read Access
- Retain State History
- Expose Runtime Visibility
- Support State Refresh
- Generate Trace Records

### 中文说明
- `Interpret Data`：把标准化数据映射为市场状态判断。
- `Maintain State`：持续维护当前市场处于什么状态。
- `Maintain Session Context`：维护当前 session 及其对市场状态的影响。
- `Maintain Multi-Timeframe Context`：维护不同周期之间的一致性、分歧和同步关系。
- `Detect State Transition`：识别状态是否从一种模式切换到另一种模式。
- `Update Confidence`：维护当前状态判断的稳定程度。
- `Publish State Changes`：在状态变化时对外发布通知。
- `Provide Read Access`：提供给允许的下游读取当前状态的入口。
- `Retain State History`：保留可追踪的状态演化记录。
- `Expose Runtime Visibility`：让外部能够看到当前运行是否可用。
- `Support State Refresh`：支持状态刷新、重估和再确认。
- `Generate Trace Records`：保留状态变化的追踪信息。

### Responsibility Rule
- Responsibilities must be written as verb phrases.
- Responsibilities must remain stable over time.

## 8. Behavior Rules
### Definition Rule
- Market State must be derived only from standardized data obtained through the Data Pipeline owner read path.
- Market State must be a versioned snapshot, not a free-form interpretation.
- Market State must be expressed through trend state, session state, multi-timeframe synchronization state, and confidence.
- Market State must not depend on raw cache objects, non-standardized inputs, or direct upstream internals.
- Market State must not infer structure events.
- Market State must not infer liquidity zones.
- Market State must not infer probability outputs.

### Supported Scope Rule
- Supported scope must be explicitly defined by configuration or system policy.
- Supported scope must be stable for the duration of the evaluated market state window.
- Supported scope must at minimum include a market identifier, a symbol identifier, and a timeframe set.
- Supported scope must not be inferred ad hoc from unrelated runtime context.

### Trend State
- Must determine trend state from standardized market data only
- Must use directional continuity, directional persistence, state stability, and confirmation window criteria as trend state criteria
- Must classify trend state as `Uptrend`, `Downtrend`, `Range`, `Transition`, or `Unknown`
- Must not use raw cache objects or non-standardized upstream data for trend classification

### Trend State Confirmation Rule
- `Uptrend` requires sustained upward directional continuity within the configured confirmation window.
- `Downtrend` requires sustained downward directional continuity within the configured confirmation window.
- `Range` requires the absence of a stable directional majority within the configured confirmation window.
- `Transition` requires a weakening prior state and an unconfirmed candidate state.
- `Unknown` applies when the confirmation window, scope, or standardized inputs are insufficient.

### Session State
- Must determine session state from configured market time rules only
- Must classify session state as `Asia`, `London`, `NY`, `Overlap`, `Cutover`, or `Unknown`
- Must treat session state as temporal context, not as directional market judgment
- Must not infer session state from volatility, momentum, or structure signals

### Session State Rule
- Session state must be derived only from configured time boundaries.
- Session state must change only when the current time crosses a defined session boundary.
- Session state must be `Cutover` when the current time is inside a configured boundary transition window.
- Session state must be `Unknown` when time rules are unavailable or invalid.

### Multi-Timeframe Sync State
- Must determine multi-timeframe synchronization from supported timeframe relationships only
- Must classify sync state as `Aligned`, `Mixed`, `Divergent`, `Transition`, or `Unknown`
- Must treat sync state as a consistency assessment, not as a final trading signal

### Multi-Timeframe Sync Rule
- `Aligned` requires supported timeframes to share the same directional bias or equivalent context.
- `Mixed` requires the majority of supported timeframes to agree while minor lag or weak divergence exists.
- `Divergent` requires material conflict between higher and lower timeframe context.
- `Transition` requires the supported timeframe set to be in a confirmed change state.
- `Unknown` applies when the supported timeframe set is incomplete or unavailable.

### State Transition
- Must change market state only when the new state satisfies the configured confirmation rule
- Must not switch state on isolated noise or unconfirmed fluctuation
- Must record previous state, new state, transition time, and transition basis for every state change
- Must identify whether a transition was caused by data update, session change, recovery, or configuration change

### Confidence
- Must assign confidence based on state stability, confirmation depth, timeframe agreement, and data completeness
- Must classify confidence as `High`, `Medium`, or `Low`
- Must reduce confidence when data is incomplete, stale, or in recovery
- Must reduce confidence when session state is `Cutover`
- Must reduce confidence when sync state is `Divergent`
- Must reduce confidence when watermark has not advanced to the latest reconciled boundary

### Confidence Rule
- `High` requires stable confirmation, complete supported scope data, and strong timeframe agreement.
- `Medium` requires a valid state with minor lag, minor divergence, or limited confirmation depth.
- `Low` applies when the state is valid but fragile, transitional, or partially degraded.

### Must
- Must consume standardized market data before evaluating market state
- Must maintain exactly one current market state for each supported scope
- Must maintain session context for each supported market scope
- Must maintain multi-timeframe synchronization context for each supported market scope
- Must expose the current market state when queried
- Must notify downstream consumers when the market state changes
- Must preserve traceable state transition history
- Must keep owned state objects under a single owner
- Must expose runtime status when queried

### 中文说明
- 只有在接收到标准化数据之后，才可以更新或确认市场状态。
- 当前状态必须保持单一口径，不能同时对同一作用域输出互相冲突的状态。
- session 变化和周期同步变化都属于状态的一部分，不是附属备注。
- 状态变化必须可追踪，不能只给结论不给过程。
- 置信度是状态可靠性，不是另一个独立结论。

### Must Not
- Must not own raw market intake behavior
- Must not generate final trading signals
- Must not execute trades
- Must not modify non-owned objects
- Must not hide runtime status from query access
- Must not describe implementation methods
- Must not bypass the Data Pipeline Domain ownership boundary
- Must not bypass the owner read path of the Data Pipeline Domain
- Must not let downstream domains mutate state objects directly
- Must not replace market state with final decision state

## 9. System Policy
### Current Policy
- Trend classification set: configurable
- Session classification set: configurable
- Multi-timeframe synchronization threshold: configurable
- State confirmation window: configurable
- Confidence threshold: configurable
- Degraded criteria: configurable

### Policy Rule
- Policy may change across versions.
- Policy must not rewrite the SDS behavior definition.

### Policy Source
- YAML
- Configuration file
- Version policy document

## 10. Interaction
### Internal Interaction
- Receive standardized data from Data Pipeline Domain through owner read path
- Publish state outputs to downstream analysis domains
- Exchange state visibility with monitoring or audit functions

### External Interaction
- Event Notify + Data Pull

### Interaction Rule
- Events notify state changes only.
- Business data must be pulled from the owner domain.
- Events must not carry business objects.

## 11. Output Guarantee
### Guarantee
- This Domain guarantees a queryable current market state for validated standardized inputs when Ready is true

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
- Must not collapse market state ownership into signal decision ownership.

## 13. Future
### Future Extension
- Future state categories
- Future confidence models
- Future transition rules

### Future Rule
- Future items must not weaken the current stable design.
