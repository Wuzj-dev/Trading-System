# Data Pipeline Domain SDS

## Document Information
- Version: V1.1
- Status: Freeze
- Domain: Data Pipeline Domain
- Scope: Signal Capture System

## 中文说明
Data Pipeline Domain 是市场数据接入与下游分析之间的标准化数据分发层。
它不拥有原始市场接入，不解释市场含义，不生成信号。
它的职责是消费接入通知，拉取上游已拥有的数据，完成标准化与对齐，并向下游分发稳定可用的数据。

- 入口仍然是 `Data Ingestion Domain`
- 本域不负责行情判断
- 本域不负责信号生成
- 本域的作用是把实时变化的数据整理成下游可稳定使用的数据
- 本域是接入层和分析层之间的桥，不是决策层

## 1. System Definition
### Identity
- This Domain is: Data Pipeline Domain
- Goal: Consume ingestion notifications, pull upstream owned data, normalize and align market data, and distribute standardized outputs to downstream analysis domains
- System Role: Standardized data distribution bridge between ingestion and analysis domains

### Description
- This Domain represents the stable behavior boundary for consuming upstream change notifications, reconciling owned upstream data, and producing analysis-ready standardized data

### 中文理解
- 核心任务不是“看懂行情”，而是“把行情整理好”。
- 先接收上游变更通知，再拉取上游最新已拥有的数据。
- 随后完成时间对齐、结构对齐、标准化和分发。
- 下游拿到的是统一口径、稳定可用的数据，而不是原始碎片数据。

### Constraint
- Do not describe implementation details.
- Do not describe programming language, framework, data structure, algorithm, or implementation method.

## 2. Domain Boundary
### In Scope
- Consume ingestion notifications
- Pull latest owned data from upstream domains when notified
- Validate data completeness and ordering
- Reconcile timing and structural consistency
- Normalize data into domain-consistent formats
- Maintain pipeline cache and delivery state
- Repair or mark recoverable data gaps when possible
- Detect stale, duplicated, missing, or out-of-order records
- Maintain replay and recovery eligibility for recent pipeline data
- Distribute standardized data to downstream domains
- Publish change notifications when pipeline state changes
- Generate operational and audit logs for pipeline behavior

### Out of Scope
- Raw market intake ownership
- Market interpretation
- Trend analysis
- Structure recognition
- Liquidity analysis
- Signal generation
- Trade execution
- Position management
- Ownership of upstream ingestion objects
- Direct modification of upstream raw data ownership
- Final decision making for any downstream signal

### 中文说明
- 输入范围只包括上游通知、上游已拥有数据、恢复请求和配置类控制。
- 输出范围只包括标准化结果、状态查询结果和变更通知。
- 任何“市场到底意味着什么”的判断，都不属于这个域。

### Boundary Rule
- This Domain owns only the responsibilities explicitly listed in this document.

## 3. Managed Objects
### Owned Objects
- Pipeline State
- Standardized Data
- Cache State
- Delivery State
- Validation Status
- Recovery State
- Watermark State
- Log Records

### 中文说明
- `Pipeline State`：当前管道整体是否健康、是否可交付。
- `Standardized Data`：已经统一格式、统一口径的数据。
- `Cache State`：缓存中保留的数据范围和有效性。
- `Delivery State`：数据是否已准备好交付给下游。
- `Validation Status`：校验结果和异常标记。
- `Recovery State`：最近一次恢复、补齐、重放的状态。
- `Watermark State`：当前处理到哪个时间边界，便于判定是否落后或缺口未清。
- `Log Records`：用于记录运行、校验、恢复、分发和异常追踪信息的日志对象。

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
- Ready indicates whether this Domain can provide standardized pipeline service.
- Degraded indicates the domain can still serve data, but data quality or freshness is reduced.
- Recovering indicates the domain is actively backfilling, repairing, or re-synchronizing data.

### 中文说明
- `Health` 说明当前进程和服务本身是否正常。
- `Ready` 说明当前是否已经具备可供下游稳定消费的能力。
- `Degraded` 说明还能工作，但存在延迟、缺口、回补中等情况。
- `Recovering` 说明正在补数据或重建状态，暂时不应被视为完全稳定。

### Query Rule
- Runtime status must be queryable.
- Downstream systems must not infer runtime status indirectly.

## 5. Input
### Accepted Inputs
- Upstream market data
- Ingestion change notifications
- Recovery requests
- Configuration updates
- Runtime control requests
- Replay or backfill commands
- Data quality alerts
- Log query requests
- Audit and trace lookup requests

### 中文说明
- 上游市场数据是主输入。
- 通知类输入用于感知上游状态变化，不替代真实数据。
- 恢复类输入用于补齐缺失、修正断点或重建缓存。
- 配置类输入只影响策略参数，不改变域边界。

### Input Rule
- Input must be interpreted only within the Domain boundary.

## 6. Output
### Produced Outputs
- Standardized market data
- Pipeline change notifications
- Validation results
- Recovery status
- Runtime status
- Data quality summary
- Delivery readiness signal
- Log records
- Audit trail responses

### 中文说明
- 标准化市场数据是供下游消费的主输出。
- 变更通知只负责提醒状态变化，不承载完整业务对象。
- 校验结果和恢复状态用于让外部系统知道当前数据是否可靠。
- `Delivery readiness signal` 用于表达数据是否已经达到交付条件。

### Output Rule
- Output must remain consistent with Domain responsibilities and ownership rules.

## 7. Responsibilities
### Long-Term Responsibilities
- Receive Data
- Validate Data
- Normalize Data
- Maintain Cache
- Maintain Delivery State
- Repair Data Gaps
- Notify Changes
- Provide Read Access
- Track Watermarks
- Support Replay
- Expose Data Quality
- Emit Logs
- Retain Audit Trails
- Provide Traceability

### 中文说明
- `Receive Data`：接收并接管上游输入。
- `Validate Data`：检查完整性、顺序和可用性。
- `Normalize Data`：统一格式、字段语义和输出口径。
- `Maintain Cache`：保留近期可用数据，支持快速读取和恢复。
- `Maintain Delivery State`：维护下游是否可交付、是否待修复。
- `Repair Data Gaps`：在允许时补齐缺口、修正断层。
- `Notify Changes`：向下游发出状态变化提醒。
- `Provide Read Access`：为允许的下游读取标准数据提供入口。
- `Track Watermarks`：记录当前处理边界，避免重复处理或遗漏。
- `Support Replay`：支持近期数据回放和重建。
- `Expose Data Quality`：向外暴露当前质量摘要和风险状态。
- `Emit Logs`：为关键生命周期事件、异常和恢复动作生成日志。
- `Retain Audit Trails`：保留可追踪的审计痕迹，便于排障和复盘。
- `Provide Traceability`：让外部能够通过日志关联关键状态变化。

### Responsibility Rule
- Responsibilities must be written as verb phrases.
- Responsibilities must remain stable over time.

## 8. Behavior Rules
### Must
- Must consume ingestion notifications before pulling upstream owned data
- Must pull the latest owned data from upstream domains when notified
- Must preserve ordering and identity rules for standardized outputs
- Must keep owned pipeline objects under a single owner
- Must expose runtime status when queried
- Must notify downstream domains when standardized data changes
- Must preserve the latest valid data version for each supported symbol and timeframe
- Must not mark data as ready until validation and delivery rules are satisfied
- Must keep recovery actions traceable through status or change notifications
- Must emit logs for key lifecycle events, validation failures, recovery actions, and delivery state changes
- Must retain log records long enough to support audit and troubleshooting needs

### 中文说明
- 标准化输出必须保持顺序、唯一性和可追踪性。
- 通知只是触发条件，真正的数据仍然要按 owner read 的方式拉取。
- 数据没有经过校验或交付条件不满足时，不能对外宣称已经 ready。
- 恢复动作必须留痕，避免后续无法解释数据是如何补回来的。
- 日志不是业务对象，但必须能够还原关键处理路径。
- 日志应当服务于审计、排障和状态追踪，而不是替代业务数据本身。

### Must Not
- Must not perform market interpretation
- Must not generate trading signals
- Must not redefine upstream ingestion responsibilities
- Must not modify non-owned objects
- Must not hide runtime status from query access
- Must not describe implementation methods
- Must not let downstream domains mutate standardized objects directly
- Must not silently discard unrecoverable data without surfacing a status change
- Must not collapse multiple ownership boundaries into one pipeline object
- Must not use logs as a substitute for business state ownership
- Must not expose sensitive upstream raw payloads through logs unless Policy explicitly allows it

## 9. System Policy
### Current Policy
- Validation strictness: configurable
- Cache retention: configurable
- Recovery behavior: configurable
- Distribution policy: configurable
- Freshness threshold: configurable
- Backfill depth: configurable
- Retry policy: configurable
- Log retention: configurable
- Log verbosity: configurable
- Audit retention: configurable

### 中文说明
- 验证严格度决定是否允许轻微异常数据进入缓冲或直接拒绝。
- 缓存保留时间决定最近数据可以保留多久、支持多深的回放。
- 恢复行为决定断点后是自动补齐、等待人工介入，还是进入降级模式。
- 分发策略决定哪些下游可以拿到哪些级别的数据。
- 新鲜度阈值决定数据延迟到什么程度算可用、可降级或不可用。
- 回补深度决定最多允许向前追溯多少窗口。
- 重试策略决定在失败后如何重新拉取或重放。
- 日志保留决定可追溯范围。
- 日志级别决定记录多少运行细节。
- 审计保留决定审计与排障历史可以保留多久。

### Policy Rule
- Policy may change across versions.
- Policy must not rewrite the SDS behavior definition.

### Policy Source
- YAML
- Configuration file
- Version policy document

## 10. Interaction
### Internal Interaction
- Receive data from Data Ingestion Domain
- Provide standardized outputs to analysis domains
- Publish change notifications to downstream consumers
- Exchange recovery signals with Data Ingestion Domain
- Provide data quality and delivery state to monitoring consumers

### 中文说明
- 这个域和接入域之间是前后衔接关系，不是并列竞争关系。
- 它向分析域输出的是标准化数据，向监控侧输出的是质量和交付状态。
- 恢复信号用于协调上游补数和本域补齐动作。

### External Interaction
- Event Notify + Data Pull

### Interaction Rule
- Events notify state changes only.
- Business data must be pulled from the owner domain.
- Events must not carry business objects.

## 11. Output Guarantee
### Guarantee
- This Domain guarantees standardized and queryable pipeline behavior for validated inputs when Ready is true
- This Domain guarantees that downstream consumers can obtain the latest standardized data through the owner read path when Ready is true

### Guarantee Boundary
- Unless Policy states otherwise, guarantees apply only when Ready is true.

### 中文说明
- 只要 `Ready = true`，下游就应当能够按 owner read 的方式拿到可用的标准化数据。
- 如果处于 `Degraded` 或 `Recovering`，系统仍可输出，但必须同时暴露状态。

## 12. Forbidden
### Forbidden Behaviors
- Must not modify non-owned objects.
- Must not introduce circular dependencies.
- Must not bypass architecture dependency rules.
- Must not redefine Foundation.
- Must not mix Policy into SDS behavior definitions.
- Must not describe implementation details.
- Must not use pipeline logic to replace analysis or decision domains
- Must not hide quality degradation behind a normal-looking output

## 13. Future
### Future Extension
- Additional standardized output formats
- Broader recovery scenarios
- Expanded distribution targets
- Hot standby pipeline
- Cross-venue normalization support

### Future Rule
- Future items must not weaken the current stable design.
