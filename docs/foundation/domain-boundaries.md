# Domain Boundary Table

## Purpose
This document defines the stable domain partition for the Signal Capture System.

## 中文说明
这份表的作用是把系统拆成几个明确的功能域，并说明每个域负责什么、不能做什么。  
你可以把它理解成“责任分工表”。后面写 SDS 或实现时，所有人都先看这张表，避免职责重叠。

## Boundary Table

| Domain | Primary Responsibility | Owner Objects | Out of Scope |
|---|---|---|---|
| Event-Driven Infrastructure Domain | Route, dispatch, and manage system events | Event Bus, Event Router, Dispatch Queue, Subscription Graph | Market interpretation, trading logic, domain internals |
| Data Ingestion Domain | Receive external market data | Ingestion Event, Intake Status | Market meaning, signal generation |
| Data Pipeline Domain | Validate, normalize, cache, distribute | Pipeline State, Standardized Data | Market interpretation, decision making |
| Market State Domain | Maintain current market state | Market State, State Transition | Trade execution, final signal output |
| Market Structure Domain | Recognize structure changes | Structure Pattern, Structure State | Trade execution, raw data intake |
| Liquidity & Imbalance Domain | Recognize liquidity and imbalance behavior | Liquidity Map, Imbalance State | Final decision generation |
| Prediction Domain | Model future behavior probabilities | Continuation Probability, Reversal Probability, Trap Probability | Market state definition, market structure definition, final signal decision |
| Signal Decision Domain | Fuse analysis and generate signals | Signal, Signal Decision State | Raw data ingestion, low-level analysis ownership |

## 表格解释
- **Domain**：哪个功能域负责这件事。
- **Primary Responsibility**：这个域最主要的长期职责。
- **Owner Objects**：这个域拥有并维护的对象。
- **Out of Scope**：这个域明确不负责的事情。

## Boundary Rules
- Event-Driven Infrastructure Domain is the single event communication layer for all domains.
- A domain may only own the objects listed under its ownership area.
- A domain may read upstream domain outputs only when permitted by dependency rules.
- A domain may not redefine another domain's responsibility.
- Final decision behavior must stay isolated in the Signal Decision Domain.
- Business domains must not call each other directly.

## 规则解释
- 每个域只能管自己的对象，不能“顺手”去改别人的对象。
- 下游域如果要用上游结果，只能按规则读取，不能直接修改。
- 任何一个域都不能重新定义别的域的职责。
- 最终信号必须集中在 `Signal Decision Domain`，这样系统的决策出口才统一。
