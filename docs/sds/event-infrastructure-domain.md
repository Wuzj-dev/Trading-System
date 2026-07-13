# Event-Driven Infrastructure Domain SDS

## Document Information
- Version: V1.0
- Status: Draft
- Domain: Event-Driven Infrastructure Domain
- Scope: Signal Capture System

## 中文说明
Event-Driven Infrastructure Domain 是系统的事件通信基础设施层。
它不负责市场解释，不负责结构识别，不负责流动性分析，也不负责信号生成。
它只负责事件的发布、订阅、路由、生命周期管理与异步派发。

## 1. System Definition
### Identity
- This Domain is: Event-Driven Infrastructure Domain
- Goal: Provide the single communication layer, event routing backbone, and asynchronous dispatch kernel for all domains
- System Role: Runtime event kernel and decoupling layer

### Description
- This Domain represents the stable behavior boundary for event publication, event routing, subscriber management, event lifecycle control, and asynchronous event dispatch

### 中文理解
- 这个域是系统的运行时内核。
- 它像邮局和分发中心，只负责把事件送到应该去的地方。
- 它不理解事件内容，也不参与任何业务判断。

### Constraint
- Do not describe implementation details.
- Do not describe programming language, framework, data structure, algorithm, or implementation method.

## 2. Domain Boundary
### In Scope
- Publish events
- Subscribe to events
- Broadcast events
- Route events to subscribed domains
- Maintain subscription graph
- Manage event lifecycle
- Enforce event immutability
- Support asynchronous event dispatch
- Manage event queue and buffer behavior

### Out of Scope
- Market interpretation
- Structure detection
- Liquidity analysis
- Prediction modeling
- Signal generation
- Trade execution
- Domain-to-domain direct calls
- Access to business domain internals

### Boundary Rule
- This Domain owns only the responsibilities explicitly listed in this document.

## 3. Managed Objects
### Owned Objects
- Event Bus
- Event Router
- Event Lifecycle State
- Subscription Graph
- Dispatch Queue
- Dispatch Buffer
- Routing Rule Set
- Event Metadata

### Ownership Rule
- Each object must have exactly one Owner Domain.
- Non-owner Domains may read the object only.
- Non-owner Domains must not modify the object.

## 4. Runtime Status
### Status Items
- Health
- Ready
- Degraded
- Routing

### Status Meaning
- Health indicates the current runtime condition of this Domain.
- Ready indicates whether this Domain can provide event routing and dispatch service.
- Degraded indicates the domain can still route events, but throughput, freshness, or delivery quality is reduced.
- Routing indicates the domain is actively dispatching events or processing routing work.

### Query Rule
- Runtime status must be queryable.
- Downstream systems must not infer runtime status indirectly.

## 5. Input
### Accepted Inputs
- TickEvent
- CandleEvent
- ReSyncEvent
- StateEvent
- SweepEvent
- MSSEvent
- BOSEvent
- LiquidityZoneEvent
- PredictionEvent
- SignalEvent
- Runtime control requests
- Subscription registration requests
- Routing configuration updates

### Input Rule
- Input must be interpreted only within the Domain boundary.

## 6. Output
### Produced Outputs
- Routed event delivery
- Broadcast event stream
- Event propagation notifications
- Dispatch status
- Runtime status

### Output Rule
- Output must remain consistent with Domain responsibilities and ownership rules.

## 7. Responsibilities
### Long-Term Responsibilities
- Publish Events
- Subscribe Events
- Broadcast Events
- Route Events
- Maintain Subscription Graph
- Manage Event Lifecycle
- Enforce Event Immutability
- Dispatch Events Asynchronously
- Manage Queue State
- Manage Buffer State
- Provide Routing Visibility

### Responsibility Rule
- Responsibilities must be written as verb phrases.
- Responsibilities must remain stable over time.

## 8. Behavior Rules
### Must
- Must route all system events through the event layer
- Must not modify event payloads after creation
- Must preserve subscription-based routing behavior
- Must support non-blocking dispatch
- Must expose runtime status when queried
- Must keep business logic outside the infrastructure boundary

### Must Not
- Must not analyze market data
- Must not detect Sweep, MSS, or BOS
- Must not compute liquidity zones
- Must not compute prediction probabilities
- Must not generate trading signals
- Must not interpret market behavior
- Must not access business domain internals
- Must not call other domains directly

### Behavior Rule
- Behavior rules describe only observable system behavior.
- Behavior rules must not describe implementation methods.

## 9. System Policy
### Current Policy
- Subscription policy: configurable
- Routing policy: configurable
- Dispatch policy: configurable
- Queue capacity policy: configurable
- Buffer policy: configurable
- Event version policy: optional

### Policy Rule
- Policy may change across versions.
- Policy must not rewrite the SDS behavior definition.

### Policy Source
- YAML
- Configuration file
- Version policy document

## 10. Interaction
### Internal Interaction
- Receive all domain events through the event layer
- Route events to subscribed domains
- Deliver notifications for downstream processing

### External Interaction
- Event Notify + Data Pull

### Interaction Rule
- Events notify state changes only.
- Business data must be pulled from the owner domain.
- Events must not carry business objects.

## 11. Output Guarantee
### Guarantee
- This Domain guarantees consistent event routing, subscriber-based delivery, and asynchronous dispatch for valid system events.

### Guarantee Boundary
- Unless Policy states otherwise, guarantees apply only when Ready is true.

## 12. Forbidden
- Must not modify non-owned objects
- Must not introduce circular dependencies
- Must not bypass architecture dependency rules
- Must not redefine Foundation
- Must not mix Policy into SDS behavior definitions
- Must not describe implementation details

## 13. Future
### Future Extension
- Event priority handling
- Dead-letter handling
- Backpressure management
- Delivery tracing
- Replay support

### Future Rule
- Future items must not weaken the current stable design.
