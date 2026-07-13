# Signal System Architecture Guard Rules

## Document Information
- Version: V1.0
- Status: Freeze
- Scope: Signal Detection System architecture guard rules

## 1. Purpose
This document defines strict domain boundaries and forbidden dependencies for the Signal Detection System.

## 中文说明
这份文档的作用是给整个系统加“护栏”。
它不是业务设计，而是架构约束，目的很明确：
- 防止架构塌陷
- 防止职责重叠
- 防止逻辑串层
- 防止上游和下游互相反向依赖

## 2. Core Principle

### Principle 1 - Single Direction Flow
Data must flow strictly in one direction:

Data → State → Structure → Prediction → Signal

No reverse dependency is allowed.

### 中文说明
数据流必须单向前进：
- 先有数据
- 再有状态
- 再有结构
- 再有预测
- 最后才有信号

不能反着来，也不能跨层倒查对方内部逻辑。

### Principle 2 - Domain Isolation
Each domain is only allowed to operate on its own responsibility.

No domain is allowed to:
- access internal logic of another domain
- modify upstream outputs
- reinterpret upstream semantics

### 中文说明
每个域只处理自己的职责：
- 不能进别的域内部
- 不能改别人的输出
- 不能重新解释别人的含义

### Principle 3 - No Cross-Logic Leakage
A domain cannot recalculate or reinterpret another domain's output.

It can only consume final outputs.

### 中文说明
一个域不能“重新算一遍”别的域的结论，也不能把别人的输出拿来当成自己的内部计算材料。
它只能消费明确输出的结果。

## 3. Domain Boundary Rules

### 3.1 Data Ingestion Domain
Allowed:
- fetch market data
- normalize OHLC
- maintain rolling windows

Forbidden:
- market interpretation
- structure detection
- state calculation
- prediction logic

### 中文说明
数据接入域只管接数据、整理数据、维护窗口，不负责分析和判断。

### 3.2 Event Bus Domain
Allowed:
- transmit events
- distribute candle / tick / state events

Forbidden:
- strategy logic storage
- market logic filtering
- transformation of meaning

### 中文说明
事件总线只负责传递，不负责理解，不负责过滤业务含义。

### 3.3 Market State Domain
Allowed:
- compute market environment
- calculate trend / range / distribution / expansion
- calculate scores for trend / liquidity / momentum / volatility

Forbidden:
- sweep detection
- MSS detection
- BOS detection
- predictive logic
- trade intent

### 中文说明
市场状态域只回答“市场处于什么环境”，不负责结构事件和预测。

### 3.4 Market Structure Domain
Allowed:
- detect ICT structural events
- sweep / BOS / MSS / breakouts
- convert price into events

Forbidden:
- probability estimation
- market regime classification
- continuation / reversal prediction
- signal generation

### 中文说明
结构域只负责识别结构事件，把价格动作转成结构事件，不负责预测和发信号。

### 3.5 Prediction Domain
Allowed:
- compute probabilities
- continuation / reversal / trap
- evaluate structure + state + behavior

Forbidden:
- raw data processing
- K-line parsing
- structure detection logic
- execution decisions

### 中文说明
预测域只吃前面域已经整理好的结果，输出概率，不碰原始数据，不做买卖决策。

### 3.6 Signal Decision Domain
Allowed:
- combine prediction outputs
- generate trading signal intent
- define entry / exit zones logically

Forbidden:
- structure recalculation
- market state recalculation
- raw price analysis
- data ingestion logic

### 中文说明
信号决策域是最后一层，只负责融合预测结果并产出信号意图，不回头重复计算上游内容。

## 4. Global Forbidden Rules

### Rule 1 - No Backward Dependency
- Prediction cannot access raw OHLC directly.
- Structure cannot access prediction output.
- Market State cannot access signal logic.

### 中文说明
禁止反向依赖：
- 预测域不能直接拿原始 K 线
- 结构域不能去看预测结果
- 状态域不能去读信号逻辑

### Rule 2 - No Duplication of Logic
- Sweep / MSS / BOS must only exist in Structure Domain.
- Trend / volatility must only exist in Market State Domain.

### 中文说明
同一个概念只能有一个 Owner：
- Sweep / MSS / BOS 只能在结构域定义
- 趋势 / 波动率只能在状态域定义

### Rule 3 - Single Source of Truth
Each concept has one owner domain.

| Concept | Owner |
|---|---|
| OHLC data | Data Ingestion |
| Sweep / MSS / BOS | Market Structure |
| trend / volatility | Market State |
| probabilities | Prediction |
| signals | Signal Decision |

### 中文说明
每个概念只有一个主责任域，别的域只能读结果，不能重新定义。

### Rule 4 - No Implicit Sharing
- No domain is allowed to infer missing data from another domain.
- Only consume explicit outputs.

### 中文说明
不能靠猜，不能靠补脑。
只能消费明确输出，不允许隐式推断别的域“应该是什么意思”。

## 5. Architecture Flow

Data Ingestion  
↓  
Event Bus  
↓  
Market State Domain  
↓  
Market Structure Domain  
↓  
Prediction Domain  
↓  
Signal Decision Domain

## 中文说明
这是推荐的主流程：
- 先接数据
- 再发事件
- 再算市场状态
- 再识别结构
- 再做概率预测
- 最后生成信号

## 6. System Stability Goal
This architecture guarantees:
- zero logic duplication
- strict separation of concerns
- deterministic behavior
- scalable ICT-based modeling

## 中文说明
这套护栏的最终目标是：
- 不重复造逻辑
- 不把职责混在一起
- 行为更可预测
- 方便以后扩展 ICT 模型

## 7. Forbidden
- Do not introduce reverse dependencies.
- Do not duplicate logic across domains.
- Do not let one domain redefine another domain's owned concept.
- Do not allow implicit sharing or hidden inference across domains.
- Do not describe implementation details.

## 8. Future
- Future extensions must preserve single-source ownership.
- Future changes must not weaken domain isolation.

