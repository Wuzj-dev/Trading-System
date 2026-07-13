# Signal Capture System Architecture Overview

## Document Information
- Version: V1.0
- Status: Freeze
- Scope: Repository-level architecture summary

## 1. Purpose
This document summarizes the system into three layers:
- Foundation Layer
- Domain Design Layer
- Policy Layer

It also clarifies which domains belong to which layer and what each layer is responsible for.

## 中文说明
这份文档是整个仓库的总览图，目的只有一个：
- 先把系统按层次分清楚
- 再把每个功能域放到正确的位置
- 最后让你一眼看明白哪些内容属于基础层，哪些内容属于域设计层，哪些内容属于策略层

---

## 2. Foundation Layer / 基础层

### 2.1 Layer Definition
The Foundation Layer defines the stable system-wide rules that must not change lightly.

### 中文说明
基础层就是“全系统统一遵守的底线和母规则”。
它不讲具体业务动作，而是讲：
- 系统怎么分层
- 谁拥有对象
- 谁只能读取
- 事件怎么传
- 哪些东西不能越界

### 2.2 Included Documents
- [Foundation Spec](foundation/foundation-spec.md)
- [Signal Capture System Definition](foundation/signal-capture-system-definition.md)
- [Domain Boundary Table](foundation/domain-boundaries.md)
- [Domain Dependency Map](foundation/domain-dependency-map.md)
- [Event-Driven Infrastructure Domain SDS](sds/event-infrastructure-domain.md)
- [Data Ingestion Domain Baseline](foundation/data-ingestion-baseline.md)
- [Signal System Architecture Guard Rules](foundation/signal-architecture-guard-rules.md)

### 2.3 Foundation Responsibilities
- Define stable principles
- Define ownership rules
- Define runtime rules
- Define dependency direction
- Define document structure baseline

### 2.4 Foundation Constraint
- Foundation should remain stable.
- Foundation changes require controlled review and ADR.

---

## 3. Domain Design Layer / 域设计层

### 3.1 Layer Definition
The Domain Design Layer defines the behavior, boundary, ownership, and interaction rules of each functional domain.

### 中文说明
域设计层就是“每个功能域自己的说明书”。
这层的原则是：
- 一个功能域，一份设计文档
- 每份文档只讲一个域
- 域和域之间可以交互，但不能混写

### 3.2 Domain List and Ownership

#### Data Ingestion Domain
- Document: [Data Ingestion Domain SDS](sds/data-ingestion-domain.md)
- Role: Unique market data entry layer
- Owner Scope: market data intake, fixed window maintenance, historical initialization, synchronization, integrity maintenance, event publishing

#### Event-Driven Infrastructure Domain
- Document: [Event-Driven Infrastructure Domain SDS](sds/event-infrastructure-domain.md)
- Role: Single event communication layer and runtime event kernel
- Owner Scope: event routing, subscription graph, dispatch lifecycle, async event delivery

#### Data Pipeline Domain
- Document: [Data Pipeline Domain SDS](sds/data-pipeline-domain.md)
- Role: Validate, standardize, cache, and distribute data
- Owner Scope: pipeline processing and standardized data distribution

#### Market State Domain
- Document: [Market State Domain SDS](sds/market-state-domain.md)
- Role: Maintain current market environment state
- Owner Scope: trend, range, distribution, expansion, and related state outputs

#### Market Structure Domain
- Document: [Market Structure Domain SDS](sds/market-structure-domain.md)
- Role: Detect structural events
- Owner Scope: sweep, MSS, BOS, breakouts, and structure events

#### Liquidity & Imbalance Domain
- Document: [Liquidity & Imbalance Domain SDS](sds/liquidity-imbalance-domain.md)
- Role: Detect liquidity and imbalance behavior
- Owner Scope: liquidity map, imbalance state, liquidity consumption behavior

#### Reversal / Continuation Domain
- Current status: behavior is covered inside the system architecture rules and domain dependency model
- Role: Evaluate continuation / reversal tendency
- Owner Scope: probability or tendency outputs for continuation, reversal, trap

#### Prediction Domain
- Document: [Prediction Domain SDS](sds/prediction-domain.md)
- Role: Consume structure and state outputs to produce probabilities
- Owner Scope: continuation, reversal, trap probabilities

#### Signal Decision Domain
- Document: [Signal Decision Domain SDS](sds/signal-decision-domain.md)
- Role: Fuse prediction outputs and generate final signal intent
- Owner Scope: final signal generation and logical entry / exit zone definition

### 3.3 Domain Design Principles
- Each domain has one owner
- Each domain has one document
- Each domain only describes its own behavior
- Each domain may read upstream outputs only through allowed interfaces or events
- No domain may recalculate another domain's owned concept

### 3.4 Domain Boundary Summary
- Event-Driven Infrastructure: owns event routing and dispatch
- Data Ingestion: owns market input
- Data Pipeline: owns standardized data flow
- Market State: owns environment state
- Market Structure: owns structural events
- Liquidity & Imbalance: owns liquidity and imbalance interpretation
- Prediction: owns probability outputs
- Signal Decision: owns final signal decision

### 3.5 Concept Ownership Reference
- [Domain Ownership Fix](foundation/domain-ownership-fix.md)

### 中文说明
如果你关心“某个概念到底归谁”，优先看这份修复文档。
它比普通的 Domain 边界表更硬，因为它直接定义概念的唯一 Owner。

---

## 4. Policy Layer / 策略层

### 4.1 Layer Definition
The Policy Layer defines configurable, version-specific, and adjustable rules.

### 中文说明
策略层不是稳定原则，而是“当前版本可以调整的运行规则”。
它通常会随着版本变化而更新，比如：
- 默认值
- 参数
- 配置项
- 版本差异

### 4.2 Included Documents
- [Version Policy](policy/version-policy.md)

### 4.3 Policy Responsibilities
- Define current version defaults
- Define configurable parameters
- Define version-specific behavior options
- Keep tunable rules separate from stable design

### 4.4 Policy Constraint
- Policy must not rewrite Foundation.
- Policy must not redefine domain ownership.
- Policy must not replace SDS behavior definitions.

---

## 5. Layer Relationship

### 5.1 Foundation → Domain Design → Policy
- Foundation defines the stable base
- Domain Design defines each domain's behavior
- Policy defines tunable runtime rules
- Event-Driven Infrastructure is part of the stable foundation / runtime kernel layer

### 中文说明
这个顺序不能乱：
1. 先定基础层
2. 再定域设计层
3. 最后再放策略层

不能先写代码、再补规范，更不能边实现边改基础定义。

### 5.2 Dependency Rule
- Domains may depend on upstream outputs only
- Domains may not access upstream internal logic
- Domains may not recalculate upstream outputs
- Policy may adjust runtime behavior but not ownership

---

## 6. Repository View

### 6.1 What Belongs Where
- Foundation Layer: system-wide stable rules
- Domain Design Layer: one document per functional domain
- Policy Layer: configurable and versioned rules

### 6.2 Reading Order
If you want to understand the system, read in this order:
1. [Foundation Spec](foundation/foundation-spec.md)
2. [Signal Capture System Definition](foundation/signal-capture-system-definition.md)
3. [Data Ingestion Domain Baseline](foundation/data-ingestion-baseline.md)
4. [Signal System Architecture Guard Rules](foundation/signal-architecture-guard-rules.md)
5. Domain SDS documents under `docs/sds/`
6. [Version Policy](policy/version-policy.md)

---

## 7. Summary
This repository is organized into three layers:

- Foundation Layer: stable, shared, system-wide rules
- Domain Design Layer: one document per functional domain
- Policy Layer: configurable, versioned rules

The architecture goal is:
- no logic duplication
- no reverse dependency
- no domain boundary collapse
- no confusion between stable rules and configurable rules
