# Event-Driven Infrastructure Rust Design

## Document Information
- Version: V1.0
- Status: Draft
- Scope: Implementation design for Event-Driven Infrastructure Domain

## 1. Design Goal
This document translates the Event-Driven Infrastructure Domain SDS into a Rust implementation design.

The design goal is to provide:
- a single event bus
- subscription-based routing
- immutable event dispatch
- asynchronous delivery
- domain decoupling

## 中文说明
这份文档只管“怎么落地事件基础设施”，不改 SDS 的边界。
它的目标是把事件层实现成一个真正可运行的 Rust 内核。

## 2. Core Runtime Model
The runtime model is centered on four concerns:
- event definition
- subscription registry
- routing and dispatch
- async execution

## 3. Suggested Module Layout
```text
event/
  mod.rs
  event.rs
  types.rs
  metadata.rs
bus/
  mod.rs
  bus.rs
  registry.rs
router/
  mod.rs
  router.rs
  graph.rs
runtime/
  mod.rs
  dispatcher.rs
  queue.rs
  buffer.rs
domain/
  mod.rs
  subscriber.rs
  producer.rs
```

## 4. Core Types

### 4.1 Event
An Event should represent a single immutable notification.

Recommended fields:
- `event_id`
- `event_type`
- `domain_origin`
- `timestamp`
- `version`
- `trace_id`
- `payload_ref` or payload handle

### 4.2 EventType
EventType should enumerate the system event kinds such as:
- `TickEvent`
- `CandleEvent`
- `ReSyncEvent`
- `StateEvent`
- `SweepEvent`
- `MSSEvent`
- `BOSEvent`
- `LiquidityZoneEvent`
- `PredictionEvent`
- `SignalEvent`

### 4.3 DomainId
DomainId should identify the subscriber or producer domain.

### 4.4 SubscriptionRegistry
SubscriptionRegistry should map event types to subscribers.

### 4.5 RoutingGraph
RoutingGraph should track the current event-to-domain delivery topology.

## 5. Suggested Traits

### 5.1 DomainSubscriber
```rust
trait DomainSubscriber {
    fn domain_id(&self) -> DomainId;
    fn subscribe_to(&self) -> &[EventType];
    fn on_event(&self, event: &Event);
}
```

### 5.2 DomainProducer
```rust
trait DomainProducer {
    fn emit(&self, event: Event);
}
```

### 5.3 EventRouter
```rust
trait EventRouter {
    fn route(&self, event: &Event);
    fn register_subscription(&self, domain: DomainId, event_type: EventType);
}
```

### 5.4 EventBus
```rust
trait EventBus {
    fn publish(&self, event: Event);
    fn subscribe(&self, domain: DomainId, event_type: EventType);
    fn broadcast(&self, event: Event);
}
```

## 6. Async Execution Model
The implementation should use non-blocking delivery semantics.

Recommended runtime behaviors:
- publish into an async queue
- dispatch through worker tasks
- isolate slow handlers from the main bus
- support bounded buffering

## 7. Immutability Rule
Events should be treated as immutable after creation.

Recommended enforcement:
- construct once
- share by reference or read-only handle
- never mutate payload content after dispatch

## 8. Routing Rules
- Event routing should be driven by subscription registration only
- Routing should not inspect business meaning
- Infra should only know event type and subscriber list
- Domain behavior should remain outside infrastructure logic

## 9. Delivery Semantics
Recommended delivery behaviors:
- at-least-once internal routing semantics by default
- optional deduplication at the consumer boundary
- bounded queue overflow policy
- observable dispatch failures

## 10. Error Handling
The infrastructure layer should report:
- subscription registration failure
- route lookup failure
- queue overflow
- dispatch failure
- stale subscriber entry

## 11. Extension Points
Future-compatible additions may include:
- priority routing
- dead-letter queue
- event replay
- delivery tracing
- backpressure control

## 12. Boundary Reminder
This design must not introduce market interpretation, structure detection, liquidity reasoning, prediction modeling, or signal generation.
