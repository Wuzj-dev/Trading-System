# Data Ingestion Domain Design

## Document Information
- Version: V1.1
- Status: Draft
- Domain: Data Ingestion Domain
- Scope: Trading signal detection program

## 1. Purpose
Data Ingestion Domain is the single market data entry layer of the system.
It receives external market data from supported venues, maintains fixed-size time-series windows, performs integrity maintenance, and publishes change notifications to downstream domains.

This domain is responsible for getting market data into the system reliably.
It is not responsible for interpreting market meaning or producing trading signals.

中文注释：
- 这个域是系统唯一的数据入口。
- 它负责接数据、维护窗口、做基础修复、发通知。
- 它不做行情解释，也不做交易决策。

## 2. Domain Identity
This domain is the system's only trusted ingress path for live and historical market data.
All downstream analysis depends on the data maintained here or derived from it.

中文注释：
- “trusted ingress path” 可以理解成系统内部认可的主入口。
- 下游分析都应该基于这里的数据，不应该自己再去连交易所重复取数。

## 3. Supported Markets
### 3.1 Crypto
- Binance Perpetual
  - BTCUSDT
  - ETHUSDT

### 3.2 CFD / Forex
- Pepperstone
  - XAUUSD
  - EURUSD
  - DXY

中文注释：
- 这里列的是 V1.1 先支持的市场范围。
- 以后新增交易对象，应该先改这个列表。
- DXY 这里作为观察标的保留，不一定等同标准交易对。

## 4. Supported Data Types
- Tick / Last Price
- OHLC K-line as the primary series data
- Depth is out of scope for V1.1

中文注释：
- Tick 是实时价格变化，频率高。
- K 线是主数据，后续分析主要靠它。
- Depth 暂时不做，避免范围膨胀。

## 5. Fixed Window Requirement
Every supported symbol and timeframe must maintain a fixed-size rolling window.

### 5.1 Required Windows
- 4H: 100 candles
- 1H: 100 candles
- 15M: 100 candles
- 5M: 100 candles

### 5.2 Window Rules
- Window length must remain fixed.
- Window growth without bound is forbidden.
- Missing candles are not allowed to remain unresolved.
- Duplicate candles are not allowed to remain in the active window.
- Old data must not contaminate the current window.

### 5.3 Window Structure
Each window should be understood as one logical container for one `symbol + timeframe` pair.

Recommended properties:
- `symbol`
- `timeframe`
- `capacity`
- `candles`
- `lastUpdatedAt`
- `lastClosedCandleTime`
- `syncStatus`
- `gapStatus`

中文注释：
- 一个窗口只对应一个交易对加一个周期，比如 `BTCUSDT + 5M`。
- `capacity` 固定就是 100。
- `candles` 是窗口里的 K 线数组。
- `syncStatus` 用来表示当前同步是否健康。
- `gapStatus` 用来表示有没有缺口或断层。

### 5.4 Candle Ordering
- Candles must be stored in ascending chronological order.
- Each candle timestamp must be unique inside one window.
- The newest candle is the active update target.

中文注释：
- K 线必须按时间从旧到新排。
- 同一个窗口里，同一时间戳只能出现一次。
- 最新一根通常是实时更新中的那根。

## 6. Core Responsibilities

### 6.1 Data Intake
- Connect to Binance and Pepperstone websocket feeds.
- Subscribe to configured symbols.
- Support dynamic subscription configuration from YAML.

中文注释：
- 第一责任是稳定拿到数据。
- YAML 配置是为了让订阅对象可配置，不要写死在代码里。

### 6.2 Historical Initialization
- Pull historical K-line data at startup.
- Initialize all required timeframe windows with 100 candles.
- Ensure the initial state is aligned across supported timeframes.

中文注释：
- 启动时不能空窗口，否则下游没有基础数据。
- “aligned” 的意思是不同周期的起始边界要尽量对齐。

### 6.3 Real-Time Synchronization
- Receive websocket price updates in real time.
- Update the active candle and rolling windows accordingly.
- Keep the latest time-series state available for downstream consumers.

中文注释：
- 这里不是简单存 tick，而是把实时变化反映到 K 线里。
- 当前周期没结束时，更新当前 K 线。
- 当前周期结束时，封口并开启下一根。

### 6.4 Multi-Timeframe Aggregation
- Use 1m as the base aggregation layer when aggregation is required.
- Aggregate Tick data into 1m and then into 5m, 15m, 1h, and 4h windows.
- Preserve chronological order during aggregation.

中文注释：
- 1m 作为基础层，意思是更高周期尽量从统一底座生成。
- 这样可以减少各周期各算各的导致的不一致。
- 时间顺序不能乱，否则高周期会错开高低开收。

### 6.5 Data Integrity Maintenance
- Detect missing candles.
- Detect duplicate candles.
- Detect non-contiguous timestamps.
- Repair windows automatically when possible.
- Remove invalid records from active windows.
- Keep each window at the required fixed length.

中文注释：
- 这是这个域的核心职责。
- 缺失表示中间本该有的数据没收到。
- 重复表示同一时间点的数据写了多次。
- 非连续表示时间轴跳了，比如 10:00 后直接到 10:10。
- 自动修复的前提是有补数据来源。

### 6.6 Data Publication
- Publish domain change notifications to the Event Bus.
- Notify downstream domains when new market data or repaired windows are available.

中文注释：
- 这里发出去的是通知，不是完整业务对象。
- 下游如果要数据，仍然要按 owner pull 的方式从这里取。

## 7. Behavior Rules

### 7.1 One-Way Data Flow
Market Data -> Data Ingestion -> Event Bus -> Downstream Domains

中文注释：
- 数据先进入接入域，再通知下游。
- 不能让下游反向直接改上游窗口，否则一致性会被破坏。

### 7.2 No Interpretation Rule
This domain must not perform market interpretation.
- No trend analysis
- No structure recognition
- No signal generation
- No strategy judgment

中文注释：
- 接入层看到的是“数据”，不是“结论”。
- 这里不能越权做趋势、结构、信号和策略判断。

### 7.3 Real-Time Priority
- Websocket input takes priority over REST polling.
- Minimize data latency whenever possible.

中文注释：
- websocket 更适合实时更新，因为它是推送式的。
- REST 更适合补数据和初始化，不应该抢实时优先级。

### 7.4 Automatic Recovery
- Reconnect websocket feeds automatically when disconnected.
- Backfill historical data after interruption.
- Repair timestamp gaps automatically when possible.

中文注释：
- 目标是让窗口尽快回到健康状态。
- 数据源短暂断开时，系统不能一直停摆，要能自己补回来。

### 7.5 State Isolation
- Each symbol and timeframe window must be maintained independently.
- Historical state must not leak into other windows.
- Active window state must always remain consistent.

中文注释：
- `BTCUSDT 5M` 和 `BTCUSDT 1H` 是两个不同窗口。
- 一个窗口的修复不应该影响另一个窗口。

## 8. Interaction Contract

### 8.1 Input Sources
- Binance websocket
- Pepperstone websocket
- Historical REST backfill

中文注释：
- websocket 负责实时流。
- REST backfill 负责补历史缺口和启动初始化。

### 8.2 Downstream Notification
The domain emits change notifications for:
- Candle updates
- Tick updates
- Timeframe refresh events
- Resync events

These notifications must follow the Foundation rule of event notify + data pull.
Events are notifications only; business data remains owned by this domain and must be pulled from it by downstream domains.

中文注释：
- 事件的职责只是“告诉别人发生变化了”。
- 事件本身不应该承载大块业务数据。
- 真正的数据仍然归这个域所有，下游必须来拉。

Recommended `MarketEvent` fields:
- `eventId`
- `eventType`
- `symbol`
- `timeframe`
- `source`
- `occurredAt`
- `windowVersion`
- `syncStatus`
- `payloadRef`

中文注释：
- `eventId`：事件唯一编号，用于追踪。
- `eventType`：事件类型，比如 `CandleEvent`、`TickEvent`。
- `symbol`：哪个交易对。
- `timeframe`：哪个周期。
- `source`：数据来自哪里。
- `occurredAt`：事件发生时间。
- `windowVersion`：窗口版本号，便于判断是否是新状态。
- `syncStatus`：当前是否需要补数据或重同步。
- `payloadRef`：业务数据引用，不建议把大对象直接塞进事件里。

### 8.3 Output Consumers
- Data Pipeline Domain
- Market State Domain
- Other downstream analysis domains that read standardized data through allowed dependencies

中文注释：
- 下游消费方不应该自己重建窗口。
- 它们应该基于这个域提供的状态继续分析。

## 9. Internal Modules

### 9.1 Data Validator Module
Responsibilities:
- Candle continuity checks
- Timestamp correction
- Data cleaning
- Window repair
- Fixed-length enforcement

中文注释：
- 这个模块负责判断数据能不能进窗口。
- 它更像门卫和修理工，不做市场判断。

### 9.2 Backfill Controller
Responsibilities:
- Historical catch-up after disconnect
- Missing range recovery

中文注释：
- 当实时流有断点时，这个模块负责补回来。
- 它通常会和 REST 数据源配合工作。

### 9.3 Time Synchronization Layer
Responsibilities:
- Unify timestamps across sources
- Align time boundaries for aggregation

中文注释：
- 不同数据源的时钟可能不完全一致。
- 这个层负责把它们拉到同一时间标准上。

### 9.4 Window Manager
Responsibilities:
- Maintain rolling 100-candle windows
- Enforce fixed window length
- Remove stale entries

中文注释：
- 这是接入域里最关键的状态维护组件之一。
- 它保证窗口一直是“最近 100 根”，而不是无限增长的仓库。

## 10. Recommended Data Structures

### 10.1 Candle
Recommended candle fields:
- `symbol`
- `timeframe`
- `openTime`
- `closeTime`
- `open`
- `high`
- `low`
- `close`
- `volume`
- `isClosed`
- `source`

中文注释：
- `openTime`：K 线开始时间。
- `closeTime`：K 线结束时间。
- `open/high/low/close`：开高低收。
- `volume`：成交量。
- `isClosed`：是否已经收盘。
- `source`：数据来源。

### 10.2 WindowState
Recommended window fields:
- `windowId`
- `symbol`
- `timeframe`
- `capacity`
- `candles`
- `headTime`
- `tailTime`
- `lastUpdatedAt`
- `syncStatus`
- `gapStatus`
- `revision`

中文注释：
- `windowId`：窗口唯一标识。
- `headTime`：最旧一根 K 线时间。
- `tailTime`：最新一根 K 线时间。
- `revision`：窗口版本号，表示被更新了多少次。

## 11. Design Principles
- Single-direction data flow
- High reliability first
- Data authenticity first
- Event-driven integration
- Clear ownership boundaries

中文注释：
- 高可靠性优先，意思是宁可慢一点，也不能让窗口乱掉。
- 数据真实性优先，意思是尽量保留真实市场时间序列，不要过度猜测。
- 所有权边界清晰，意思是这个域只管自己的窗口，不跨域代管别人的状态。

## 12. Domain Position
Data Ingestion Domain is:
- The only trusted entry point for market data
- The base data source for all downstream trading logic
- Not a decision-making layer

中文注释：
- 这个域不负责告诉系统“买还是卖”。
- 它只负责让后面的系统有一份干净、连续、可追踪的数据。

## 13. Forbidden Behavior
- Do not calculate signals.
- Do not calculate structure.
- Do not perform strategy evaluation.
- Do not predict market direction.
- Do not own decision logic.
- Do not let invalid data remain in the active window.

中文注释：
- 这里是硬约束，不是建议。
- 一旦窗口出现污染，就必须进入修复流程。

## 14. Backfill Workflow

### 14.1 Detect Gap
- 发现 websocket 中断
- 发现时间戳跳跃
- 发现 K 线缺失

中文注释：
- 先判断哪里坏了，再决定补哪里。

### 14.2 Plan Recovery Range
- 计算缺失时间区间
- 计算需要补的 symbol 和 timeframe
- 选择可用的历史数据源

中文注释：
- 关键是明确补哪个时间段，不要盲目重拉全量数据。

### 14.3 Fetch Missing Data
- 从 REST 或历史接口拉取缺口数据
- 按时间顺序写入临时区

中文注释：
- 先拉数据，不要直接覆盖活动窗口。
- 临时区的意义是防止脏数据污染主窗口。

### 14.4 Validate Before Commit
- 检查重复
- 检查连续性
- 检查时间边界
- 检查是否与现有窗口冲突

中文注释：
- 这一步相当于提交前审核。
- 只有通过校验的数据，才允许进入主窗口。

### 14.5 Commit to Window
- 替换或补齐活动窗口中的缺口
- 重新裁剪为固定长度
- 更新窗口版本号

中文注释：
- 这一步才是真正把数据写回主窗口。
- 写回后，窗口仍然必须保持 100 根固定长度。

### 14.6 Emit Resync Event
- 发布 ResyncEvent
- 通知下游窗口已恢复

中文注释：
- 这表示数据已经修好了，下游可以重新读取了。

## 15. Event Definitions
下面是字段级别建议，方便你后面落实现阶段接口。

### 15.1 MarketEvent
MarketEvent is the top-level market change event.

Recommended fields:
- `eventId`
- `eventType`
- `symbol`
- `timeframe`
- `source`
- `occurredAt`
- `windowVersion`
- `syncStatus`
- `payloadRef`

中文注释：
- 这是总事件壳，主要用于通知“有变化发生了”。
- 它本身不建议承载大量 K 线数组。
- 真正的数据内容建议通过 `payloadRef` 或 owner pull 获取。

### 15.2 CandleEvent
CandleEvent represents a change on one candle.

Recommended fields:
- `eventId`
- `symbol`
- `timeframe`
- `candleTime`
- `isClosed`
- `open`
- `high`
- `low`
- `close`
- `volume`
- `source`
- `occurredAt`

中文注释：
- 这个事件更具体，表示某根 K 线被新数据更新了。
- `isClosed` 用来区分这根 K 线是否已经结束。
- 它适合通知下游“这一根变了，重新读一下”。

### 15.3 TickEvent
TickEvent represents the latest price update.

Recommended fields:
- `eventId`
- `symbol`
- `price`
- `volume`
- `source`
- `occurredAt`
- `sequenceNo`

中文注释：
- TickEvent 更偏实时流。
- 它通常用于驱动当前 K 线更新。
- `sequenceNo` 可以帮助识别顺序和缺失。

### 15.4 TimeframeUpdateEvent
TimeframeUpdateEvent represents a timeframe window update.

Recommended fields:
- `eventId`
- `symbol`
- `timeframe`
- `windowVersion`
- `headTime`
- `tailTime`
- `syncStatus`
- `occurredAt`

中文注释：
- 这个事件用于告诉下游：某个周期的窗口已经刷新。
- 它比 CandleEvent 更像“窗口级通知”。

### 15.5 ReSyncEvent
ReSyncEvent represents a backfill or resynchronization action.

Recommended fields:
- `eventId`
- `symbol`
- `timeframe`
- `gapStartTime`
- `gapEndTime`
- `repairMethod`
- `windowVersion`
- `occurredAt`

中文注释：
- 这个事件很重要，因为它说明当前窗口是通过补数据修复过的。
- `gapStartTime` 和 `gapEndTime` 表示缺口范围。
- `repairMethod` 可以写 `backfill`、`rebuild` 或 `partial_patch`。

## 16. Data Quality Rules

### 16.1 Duplicate Handling
- If the same timestamp appears twice, keep the latest valid record.
- Mark the window as needing validation if conflict occurs.

中文注释：
- 如果同一时间戳重复出现，通常保留最新、最完整的那条。
- 但要记录冲突状态，不能静默覆盖。

### 16.2 Missing Candle Handling
- If a candle is missing, try backfill first.
- If backfill fails, mark gap status and notify downstream.

中文注释：
- 缺失 K 线优先补。
- 实在补不上，也必须告诉下游这个窗口有问题。

### 16.3 Out-of-Order Handling
- Reorder by timestamp before commit.
- Reject records that cannot be safely ordered.

中文注释：
- 时间乱序数据要先排序再提交。
- 如果排序后仍然不安全，就不要写进活动窗口。

## 17. Open Questions
下面这些点如果你要继续细化，建议后面再补：
- 是否允许 `MarketEvent` 直接带少量 candle 摘要
- 1m 是否必须永久保存，还是只作为聚合中间层
- 历史初始化时，是否允许不同 timeframe 使用不同回溯深度
- 重复数据的“最新有效”判定规则
- Pepperstone 的具体数据接口形式

## 18. Notes on Boundary Alignment
This document defines the behavior of the Data Ingestion Domain only.
If implementation details require broader standardization, they should be documented in the Data Pipeline Domain or in a separate runtime specification.

中文注释：
- 这份文档只定义 Data Ingestion Domain 自己的行为。
- 如果实现层面需要更广泛的标准化，应该写到 Data Pipeline Domain，或者单独写运行时规格文档。
