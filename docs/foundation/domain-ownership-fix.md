# Domain Ownership Fix

## Document Information
- Version: V1.0
- Status: Freeze
- Scope: Concept ownership rules for the Signal Capture System

## 1. Purpose
This document defines the single owner of each core trading concept.

Each concept must belong to exactly one domain.
No duplication is allowed.

## 中文说明
这份文档的作用是“概念归属锁定”。
它不是新的业务设计，而是告诉系统：
- 哪些概念属于哪个域
- 哪些概念不能重复实现
- 哪些概念不能被下游重新定义

---

## 2. Core Principles

| Rule | Meaning |
|---|---|
| Single Ownership | One concept belongs to exactly one domain |
| No Duplication | No duplicate implementation across domains is allowed |
| No Reinterpretation | Downstream domains must not redefine upstream concepts |
| Single Source of Truth | Each concept has exactly one source of truth |

## 中文说明
- **单一归属**：一个概念只能有一个 Owner。
- **禁止复制**：不要在多个域里重复实现同一个概念。
- **禁止解释**：下游不能重新定义上游已经拥有的概念。
- **单一真值**：同一个概念只能有一个可信来源。

---

## 3. Market State Domain

### Unique Responsibility
The Market State Domain owns the market environment.

### Owned Concepts
| Concept | Meaning |
|---|---|
| Trend | Up / Down / Range |
| Market Regime | trend / range / distribution / expansion |
| Volatility | Strength of price movement |
| Session | Asia / London / NY |

### Forbidden Ownership
- Sweep
- MSS
- BOS
- Liquidity zones

### 中文说明
市场状态域只负责回答：
- 当前是什么环境
- 当前趋势是什么
- 当前波动强不强
- 当前属于哪个 session

它不负责结构事件，也不负责流动性位置。

---

## 4. Market Structure Domain

### Unique Responsibility
The Market Structure Domain owns price-to-structure event recognition.

### Owned Concepts
| Concept | Meaning |
|---|---|
| Sweep | Liquidity sweep high / low |
| BOS | Break of Structure |
| MSS | Market Structure Shift |

### Forbidden Ownership
- Trend judgment
- Market Regime
- Probability
- Liquidity zones

### 中文说明
结构域只负责把价格动作识别成结构事件。
它不负责趋势，不负责概率，不负责流动性区间。

---

## 5. Liquidity Domain

### Unique Responsibility
The Liquidity Domain owns liquidity location mapping.

### Owned Concepts
| Concept | Meaning |
|---|---|
| Liquidity Zones | EQH / EQL |
| Liquidity Clusters | Dense pending-liquidity areas |
| Resting Liquidity | Untriggered liquidity |

### Forbidden Ownership
- Sweep
- MSS
- BOS
- Trend

### 中文说明
流动性域只负责“流动性在哪里”。
它不负责“流动性被怎么扫”，也不负责“市场趋势是什么”。

---

## 6. Prediction Domain

### Unique Responsibility
The Prediction Domain owns future probability modeling.

### Owned Concepts
| Concept | Meaning |
|---|---|
| P_continuation | Continuation probability |
| P_reversal | Reversal probability |
| P_trap | Trap probability |

### Forbidden Ownership
- Sweep detection
- MSS detection
- BOS detection
- Trend calculation
- Raw OHLC analysis

### 中文说明
预测域只负责概率：
- 更可能延续
- 更可能反转
- 更可能 trap

它不能回头做结构识别，也不能直接读原始 K 线做重复分析。

---

## 7. Signal Domain

### Unique Responsibility
The Signal Domain owns trade signal output.

### Owned Concepts
| Concept | Meaning |
|---|---|
| Entry Signal | Entry intent |
| Exit Signal | Exit intent |
| Risk Filter | Risk filtering |

### Forbidden Ownership
- Probability calculation
- Structure detection
- Market state analysis

### 中文说明
信号域只负责输出“是否交易”的最终意图。
它不是概率计算器，也不是结构识别器。

---

## 8. Data / Event Domain

### Unique Responsibility
The Data / Event Domain owns data delivery and event transmission.

### Owned Concepts
| Concept | Meaning |
|---|---|
| OHLC Data | Raw K-line data |
| Tick Data | Trade data |
| Events | Candle / SweepEvent / MSS events |

### Forbidden Ownership
- Any market interpretation
- Any structure analysis

### 中文说明
基础域只负责数据和事件，不负责理解市场。

---

## 9. Global Lock Rules

### Rule 1 - Concept cannot exist in multiple domains
- Sweep → ONLY Structure Domain
- Trend → ONLY Market State Domain
- Liquidity Zones → ONLY Liquidity Domain
- Probability → ONLY Prediction Domain
- Signal → ONLY Signal Domain

### Rule 2 - No backward ownership
- Downstream domains may consume outputs.
- Downstream domains may not own upstream concepts.

### Rule 3 - No implicit reassignment
- If a concept already has an owner, another domain must not redefine it.

### Rule 4 - No duplicate logic
- The same concept must not be recalculated in multiple domains.

## 中文说明
这四条就是概念归属的硬锁：
- 一个概念只能一个 Owner
- 下游只能消费，不能反抢所有权
- 不能把已有概念重新命名后又当成新概念
- 同一概念不能在多个域里重复实现

---

## 10. Future
- Future concepts must be assigned to exactly one owner before implementation.
- Future additions must not violate single ownership.

