# Signal Capture System Definition

## Document Information
- Version: V1.0
- Status: Draft
- Scope: Real-time market signal capture system

## 0. 基础定义
这一部分用于先把系统里最核心、最容易混淆的概念讲清楚。  
你可以把它理解成“统一语言说明书”。后面所有 Domain、SDS、Policy、ADR 都要在这套语言下讨论，避免同一个词在不同地方有不同意思。

### 0.1 系统是什么
- 英文名：`Signal Capture System`
- 中文名：`实时市场信号捕获系统`
- 直白理解：这是一个面向实时市场的信号识别和信号生成系统。它不是交易执行系统，而是“先看懂市场，再决定是否发信号”的系统。

### 0.2 系统做什么
- 持续接收市场数据
- 识别市场当前状态
- 分析结构变化
- 分析流动性与失衡
- 判断反转或延续倾向
- 最后生成交易信号

### 0.3 系统不做什么
- 不直接下单
- 不管理仓位
- 不负责组合管理
- 不把实现细节写进设计文档

### 0.4 为什么要分 Domain
- 因为一个大系统如果只有一个“总逻辑”，后面很容易混乱
- 分 Domain 的目的，是把责任切清楚
- 每个 Domain 只管一类稳定职责，后续设计和开发都更容易维护

### 0.5 这套系统的核心思路
- 先接入数据
- 再整理数据
- 再识别状态和结构
- 再分析流动性和方向倾向
- 最后由决策层输出信号

### 0.6 术语说明
- `State`：市场当前处于什么状态
- `Structure`：市场结构发生了什么变化
- `Liquidity`：流动性在哪里
- `Imbalance`：买卖力量是否失衡
- `Reversal`：是否可能反转
- `Continuation`：是否可能延续
- `Signal`：最终输出的交易信号

## 1. System Definition
### Identity
- System Name: Signal Capture System
- Chinese Name: 实时市场信号捕获系统
- Positioning: A real-time signal capture and generation system based on ICT / SMC structure recognition

### 解释
- “Signal Capture System” 的意思不是单纯抓数据，而是抓到数据以后，还要继续理解数据背后的结构和行为。
- “基于 ICT / SMC” 的意思是，这个系统会围绕市场结构、流动性、失衡、反转、延续这些维度来做分析。
- “实时” 的意思是，这个系统关注的是当前市场正在发生什么，而不是只看历史报表。

### Goal
- Continuously capture real-time market data
- Maintain market state awareness
- Detect structure changes and liquidity behavior
- Fuse multi-layer domain information
- Generate stable and traceable trading signals

### 目标解释
- “持续捕获数据”表示系统要不断接收新数据，不能只在某个时刻看一次。
- “维护市场状态意识”表示系统要知道当前市场是趋势、震荡、扩张，还是其他状态。
- “检测结构变化”表示系统要关注市场是否出现了破坏、转折、延续等变化。
- “融合多层信息”表示最终信号不是只看一个指标，而是综合多个 Domain 的结论。
- “稳定和可追踪”表示信号不能凭空出现，必须能说明为什么会产生。

### System Role
- This system is the behavioral boundary for market signal capture and decision generation.

### 解释
- 这句话的意思是：这个系统只定义“应该表现出什么行为”，不去写“怎么实现”。
- 它像一条边界线，规定哪些行为属于系统内，哪些行为不属于系统内。

### Constraint
- Do not describe implementation details.

## 2. System Scope
### In Scope
- Market data capture
- Data validation and standardization
- Market state maintenance
- Market structure analysis
- Liquidity and imbalance analysis
- Reversal and continuation tendency analysis
- Signal decision generation

### Out of Scope
- Trade execution
- Position management
- Portfolio management
- Order routing
- Broker-specific implementation details

## 3. Domain List
1. Data Ingestion Domain
2. Data Pipeline Domain
3. Market State Domain
4. Market Structure Domain
5. Liquidity & Imbalance Domain
6. Reversal / Continuation Domain
7. Signal Decision Domain

## 4. Domain Boundary Summary
### 4.1 Data Ingestion Domain
- Owns external data intake behavior
- Does not judge market meaning
- Does not produce final trading signals

### 中文理解
- 这个域就像系统入口，负责把外部市场数据接进来。
- 它只做“接收”，不做“解释”。
- 它不判断行情是涨是跌，也不直接发交易信号。

### 4.2 Data Pipeline Domain
- Owns validation, standardization, caching, and distribution behavior
- Does not assign market semantics
- Does not make decision-level judgments

### 中文理解
- 这个域负责把“原始数据”变成“可用数据”。
- 它会检查数据是否完整、格式是否统一、是否要缓存、应该分发给谁。
- 它不负责告诉系统市场现在处于什么状态。

### 4.3 Market State Domain
- Owns the current market state behavior
- Does not generate final signals
- Does not own raw data intake

### 中文理解
- 这个域负责回答一个问题：市场现在是什么状态？
- 比如是趋势、震荡、收敛、扩张、弱势、强势。
- 它不负责直接下结论说“买”或“卖”。

### 4.4 Market Structure Domain
- Owns structure change recognition behavior
- Does not execute trades
- Does not own data ingestion behavior

### 中文理解
- 这个域专门看“结构有没有变”。
- 它关注的是结构破坏、结构转折、结构延续这些事情。
- 它不是最终决策层。

### 4.5 Liquidity & Imbalance Domain
- Owns liquidity and imbalance recognition behavior
- Does not generate final signals
- Does not manage market state ownership

### 中文理解
- 这个域负责看流动性在哪里、有没有被消耗、买卖力量是否失衡。
- 它更像是在看市场“力的分布”和“压力点”。
- 它不直接决定最终信号方向。

### 4.6 Reversal / Continuation Domain
- Owns directional tendency behavior
- Does not replace signal decision behavior
- Does not own upstream data ingestion

### 中文理解
- 这个域负责判断市场更像“要反转”还是“要延续”。
- 它属于方向倾向层，不是最终决策层。
- 它输出的是倾向，不是最终交易动作。

### 4.7 Signal Decision Domain
- Owns final signal decision behavior
- Fuses information from other domains
- Does not own upstream analysis domains

### 中文理解
- 这个域是最后一层。
- 它把前面所有域的结论综合起来，最终决定是否输出交易信号。
- 它是“决策者”，不是“数据入口”，也不是“底层分析器”。

## 5. Boundary Rules
- Each domain owns one stable responsibility area.
- Each object must have one owner.
- Non-owner domains may read only.
- Non-owner domains must not modify owned objects.
- Data movement between domains must follow event notify + data pull.

### 规则解释
- “一个 Domain 只拥有一块稳定职责区域”表示职责不能随意交叉。
- “一个对象只能有一个 Owner”表示不能多个域同时改同一个对象。
- “非 Owner 只能读”表示其它域可以看结果，但不能改结果。
- “Event Notify + Data Pull”表示事件只负责提醒，真正的数据要到 Owner 那里去取。
