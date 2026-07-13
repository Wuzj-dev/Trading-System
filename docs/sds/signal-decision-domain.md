# Signal Decision Domain SDS

## Document Information
- Version: V1.1
- Status: Freeze
- Domain: Signal Decision Domain
- Scope: Signal Capture System

## 中文说明
Signal Decision Domain 是系统的最终决策出口，也是决策层 / Brain Layer。
它不负责原始数据接入，不负责底层结构识别，也不替代上游分析域。
它的职责是融合来自 `Market State Domain`、`Market Structure Domain`、`Liquidity & Imbalance Domain` 的结果，生成统一、可追踪、可解释的最终交易信号输出。

- 输入来自上游分析域
- 本域不拥有底层分析对象
- 本域不负责行情原始接入
- 本域回答的是“此刻是否应该输出什么交易信号”

## 1. System Definition
### Identity
- This Domain is: Signal Decision Domain
- Goal: Fuse upstream domain outputs and generate final signal decisions
- System Role: Final decision and signal emission boundary

### Description
- This Domain represents the stable behavior boundary for fusing analysis results and producing final signal decisions

### 中文理解
- 这个域不是“看见条件就报警”的检测层，而是“综合多个域结论后做最终选择”的决策层。
- 它关心的是上下文是否足够一致、结论是否足够稳定、当前是否值得输出信号。
- 它输出的是最终信号，而不是单一分析结论。

### Constraint
- Do not describe implementation details.
- Do not describe programming language, framework, data structure, algorithm, or implementation method.

## 2. Domain Boundary
### In Scope
- Fuse upstream domain outputs into a final decision context
- Evaluate whether the current market context supports a signal
- Produce final trade signal decisions
- Produce signal change notifications
- Maintain signal lifecycle visibility
- Preserve decision traceability and decision rationale
- Expose runtime visibility for signal service behavior

### Out of Scope
- Trade execution
- Position management
- Portfolio management
- Raw market intake ownership
- Low-level analysis ownership
- Raw structure calculation
- Raw liquidity calculation
- Raw market state calculation
- Strategy implementation details
- Raw prediction calculation
- Liquidity zone ownership

### Boundary Rule
- This Domain owns only the responsibilities explicitly listed in this document.

## 3. Managed Objects
### Owned Objects
- Signal
- Signal Decision State
- Signal Context
- Decision Trace
- Signal Lifecycle State
- Signal Summary

### 中文说明
- `Signal`：最终对外输出的信号对象。
- `Signal Decision State`：当前决策层的工作状态和可用性状态。
- `Signal Context`：用于支撑当前信号判断的融合上下文。
- `Decision Trace`：这次信号为什么成立或为什么未成立的追踪记录。
- `Signal Lifecycle State`：信号从候选、确认、撤销、失效等阶段的生命周期状态。
- `Signal Summary`：对当前信号结论的简要描述。

### Ownership Rule
- Each object must have exactly one Owner Domain.
- Non-owner Domains may read the object only.
- Non-owner Domains must not modify the object.

## 4. Runtime Status
### Status Items
- Health
- Ready
- Degraded
- Evaluating

### Status Meaning
- Health indicates the current runtime condition of this Domain.
- Ready indicates whether this Domain can provide final signal decision service.
- Degraded indicates the domain can still provide decisions, but confidence or timeliness is reduced.
- Evaluating indicates the domain is currently fusing upstream inputs and has not yet reached a stable decision.

### Query Rule
- Runtime status must be queryable.
- Downstream systems must not infer runtime status indirectly.

## 5. Input
### Accepted Inputs
- Market state outputs
- Market structure outputs
- Liquidity and imbalance outputs
- Prediction outputs
- Configuration updates
- Runtime control requests
- Signal review or replay requests
- Audit and trace lookup requests

### Input Rule
- Input must be interpreted only within the Domain boundary.
- Input must be derived from upstream owned outputs or explicit control requests.

## 6. Output
### Produced Outputs
- Signal decisions
- Signal notifications
- Decision trace
- Runtime status
- Signal summary
- Validation result
- Confidence score
- Entry zone
- Stop loss
- Take profit

### Output Rule
- Output must remain consistent with Domain responsibilities and ownership rules.

### Output Schema Rule
- A signal output must include the current signal direction or neutral state.
- A signal output must include the current scope.
- A signal output must include the upstream decision context version.
- A signal output must include decision confidence.
- A signal output must include entry zone when a trade signal is emitted.
- A signal output must include stop loss when a trade signal is emitted.
- A signal output must include take profit when a trade signal is emitted.
- A signal output must include trace metadata.
- A signal output must include freshness metadata when runtime status is not Ready.

### 中文说明
- 信号输出不能只是一个方向词，必须带上作用域、置信度和追踪信息。
- 如果输出的是可执行交易信号，还必须带上 entry zone、SL、TP。
- 当状态不是 `Ready` 时，下游仍可看到结果，但必须能判断它是否足够新、足够稳。

## 7. Responsibilities
### Long-Term Responsibilities
- Fuse Upstream Outputs
- Evaluate Decision Context
- Generate Signal
- Maintain Signal Lifecycle
- Maintain Decision Trace
- Notify Changes
- Provide Read Access
- Expose Runtime Visibility

### 中文说明
- `Fuse Upstream Outputs`：把不同分析域的结果合并成统一决策视角。
- `Evaluate Decision Context`：判断当前上下文是否足够支持输出信号。
- `Generate Signal`：在条件满足时生成最终交易信号。
- `Maintain Signal Lifecycle`：维护信号从候选到失效的状态变化。
- `Maintain Decision Trace`：保留信号为什么成立的追踪依据。
- `Notify Changes`：在信号状态变化时对外发出提醒。
- `Provide Read Access`：为允许的下游提供读信号入口。
- `Expose Runtime Visibility`：让外部知道当前决策服务是否可用。

### Responsibility Rule
- Responsibilities must be written as verb phrases.
- Responsibilities must remain stable over time.

## 8. Behavior Rules
### Must
- Must generate a signal only when upstream context is sufficiently consistent
- Must notify when a signal decision changes
- Must expose runtime status when queried
- Must keep owned signal objects under a single owner
- Must preserve decision traceability for emitted signals
- Must avoid overwriting upstream analysis ownership
- Must consume prediction outputs when they are available
- Must consume liquidity outputs when they are available
- Must include confidence, entry zone, stop loss, and take profit in emitted trade signals

### Must Not
- Must not execute trades
- Must not manage objects owned by other Domains
- Must not own raw data ingestion behavior
- Must not replace upstream analysis domains
- Must not describe implementation methods

## 9. System Policy
### Current Policy
- Decision policy: configurable
- Runtime thresholds: configurable
- Version-specific signal rules: configurable
- Signal suppression policy: configurable
- Signal sizing policy: configurable
- Risk model policy: configurable

### Policy Rule
- Policy may change across versions.
- Policy must not rewrite the SDS behavior definition.

### Policy Source
- YAML
- Configuration file
- Version policy document

## 10. Interaction
### Internal Interaction
- Receive analytical outputs from upstream owned domains
- Publish signal notifications to downstream domains
- Consume only owner-readable outputs from upstream domains
- Read prediction outputs from Prediction Domain
- Read liquidity outputs from Liquidity Domain

### External Interaction
- Event Notify + Data Pull

### Interaction Rule
- Events notify state changes only.
- Business data must be pulled from the owner domain.
- Events must not carry business objects.
- Final decisions must be derived from upstream owned outputs, not from raw ingestion events.
- Signal outputs may be consumed by downstream execution or alert domains, but execution is outside this domain.

### 中文说明
- 这个域不是只看结构、状态、流动性，它还要看预测层的输出。
- 预测层已经把更上游的信息整理成概率，因此信号决策层应把它视为正式输入之一。
- 最终信号必须来自“状态 + 结构 + 流动性 + 预测”的融合，而不是任何单一域的直接结论。

## 11. Output Guarantee
### Guarantee
- This Domain guarantees consistent final trade signal decisions for valid upstream context

### Guarantee Boundary
- Unless Policy states otherwise, guarantees apply only when Ready is true.

## 12. Forbidden
### Forbidden Behaviors
- Must not modify non-owned objects.
- Must not introduce circular dependencies.
- Must not redefine Foundation.
- Must not mix Policy into SDS behavior definitions.
- Must not collapse upstream analysis ownership into the decision layer.
- Must not expose raw analysis ownership as if it were owned here.

## 13. Future
### Future Extension
- Future signal categories
- Future decision policies
- Future trace formats
- Future confidence models

### Future Rule
- Future items must not weaken the current stable design.
