# Domain Boundary Table

## Purpose
This document defines the stable domain partition for the Signal Capture System.

## Boundary Table

| Domain | Primary Responsibility | Owner Objects | Out of Scope |
|---|---|---|---|
| Data Ingestion Domain | Receive external market data | Ingestion Event, Intake Status | Market meaning, signal generation |
| Data Pipeline Domain | Validate, normalize, cache, distribute | Pipeline State, Standardized Data | Market interpretation, decision making |
| Market State Domain | Maintain current market state | Market State, State Transition | Trade execution, final signal output |
| Market Structure Domain | Recognize structure changes | Structure Pattern, Structure State | Trade execution, raw data intake |
| Liquidity & Imbalance Domain | Recognize liquidity and imbalance behavior | Liquidity Map, Imbalance State | Final decision generation |
| Reversal / Continuation Domain | Detect directional tendency | Tendency State, Bias State | Final signal decision |
| Signal Decision Domain | Fuse analysis and generate signals | Signal, Signal Decision State | Raw data ingestion, low-level analysis ownership |

## Boundary Rules
- A domain may only own the objects listed under its ownership area.
- A domain may read upstream domain outputs only when permitted by dependency rules.
- A domain may not redefine another domain's responsibility.
- Final decision behavior must stay isolated in the Signal Decision Domain.

