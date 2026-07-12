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

