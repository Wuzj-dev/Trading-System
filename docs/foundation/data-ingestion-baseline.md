# Data Ingestion Domain Baseline

## Document Information
- Version: V1.0
- Status: Freeze
- Scope: Baseline requirement for domain document generation

## 1. Purpose
This document defines the baseline structure and behavioral requirement for the Data Ingestion Domain.

## 中文说明
这份文档不是单纯描述一个域的功能，而是把你当前这版 `Data Ingestion Domain` 提炼成“基础要求”。  
以后凡是生成新的系统文档、Domain SDS、边界说明、行为规则，都默认按这份结构来组织。

## 2. Baseline Role
- This document is the reference baseline for future generated documents.
- Future documents should follow the same architecture, terminology, and behavioral separation.
- Future documents may extend details, but must not break the baseline structure.

## 中文说明
你可以把它理解成“后续文档的母版”：
- 先照这个结构写
- 再根据具体 Domain 做增删改
- 不能把职责边界写乱
- 不能把分析、决策、接入混在一起

## 3. Required Architecture
All future documents generated from this baseline should follow the same architecture:

1. System Definition
2. Domain Boundary
3. Managed Objects
4. Runtime Status
5. Input
6. Output
7. Responsibilities
8. Behavior Rules
9. System Policy
10. Interaction
11. Output Guarantee
12. Forbidden
13. Future

## 中文说明
这 13 个章节后续尽量固定。
如果以后某个文档需要扩展，也应该在这套结构里补，而不是自己随意换顺序、删章节、改语义。

## 4. Core Baseline Requirements
- The domain is the unique market data entry layer.
- The domain receives data from multiple markets or venues.
- The domain maintains fixed-length windows.
- The domain supports historical initialization.
- The domain supports real-time synchronization.
- The domain maintains data completeness.
- The domain publishes unified events.

## 中文说明
这些是这份基础要求里最关键的几条：
- 它是唯一入口
- 它负责接入与维护
- 它要保证窗口固定长度
- 它要维护数据完整性
- 它要把统一事件流往下发

## 5. Supported Market Scope
- Crypto
- CFD / Forex

## 6. Supported Data Types
- Tick / Last Price
- OHLC K-line
- Depth is not enabled in this baseline

## 7. Fixed Window Requirement
- 4H → 100 bars
- 1H → 100 bars
- 15M → 100 bars
- 5M → 100 bars

## 中文说明
固定窗口是这个基础要求的核心之一。
它的意义是：
- 防止数据无限增长
- 防止窗口不稳定
- 防止后续分析域拿到不统一的数据长度
- 保证不同周期都维持统一视图

## 8. Core Responsibilities
- Data Ingestion
- Historical Initialization
- Real-Time Synchronization
- Multi-Timeframe Aggregation
- Data Integrity Maintenance
- Event Publishing

## 9. Behavior Rules
- Market → Ingestion → Event Bus → Downstream
- No market interpretation in this layer
- Real-time websocket is preferred over REST
- Automatic recovery is required
- Window isolation must be preserved

## 10. Forbidden
- Do not calculate signals
- Do not calculate structure
- Do not make strategy judgments
- Do not predict market direction
- Do not interpret market meaning

## 11. Extension Rule
- Future documents may be richer than this baseline.
- Future documents must keep the same stable architecture.
- Future documents must not move decision logic into the ingestion layer.

## 12. Document Separation Rule
- Each functional domain must have its own separate design document.
- Different functional domains must not share one SDS as the primary design document.
- A domain document may reference other domain documents, but it must not replace them.

## 中文说明
这一条非常重要，它是后续所有设计文档生成的硬要求：
- 一个功能域一份文档
- 不能把多个功能域混在同一份 SDS 里
- 可以相互引用
- 但每个域都必须有自己的独立设计文档

## 13. Domain Document Independence
- The identity, responsibilities, behavior rules, and ownership of one domain must be described in its own document.
- A domain document must remain readable and reviewable without requiring another domain's document to define its core meaning.

## 中文说明
这意味着：
- 你看 `Data Ingestion Domain SDS` 时，就应该主要理解它自己
- 你看 `Market State Domain SDS` 时，也应该主要理解它自己
- 彼此可以交互，但不能互相代替

## 14. Downstream Price Access Rule
- Downstream functional domains must obtain price and K-line data through the owner domain's readable interface or published events.
- Downstream domains must not access raw market sources directly.
- Downstream domains must not rebuild their own independent market data source for the same owned market scope.
- Downstream domains may read the latest standardized K-line data for their own analysis, but they must not modify the owner domain's window.

## 中文说明
后续功能域如果要拿价格和 K 线，规则要很清楚：
- 不能自己直连交易所去抓同一份数据
- 必须从 `Data Ingestion Domain` 提供的可读接口或事件流获取
- 只能读，不能改
- 不能自己再建一套重复的数据入口

## 15. K-line Data Access Meaning
- Price and K-line data are owned by the ingestion layer for the system's entry scope.
- Analysis domains consume standardized data as read-only inputs.
- The ingestion layer remains the single source of truth for the captured market window.

## 中文说明
这个意思是：
- `Data Ingestion Domain` 负责维护真实的市场窗口
- 其他域只消费这个窗口，不自己重建
- 这样可以保证数据一致性，避免不同域看到不同版本的 K 线

