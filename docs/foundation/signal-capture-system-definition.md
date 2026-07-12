# Signal Capture System Definition

## Document Information
- Version: V1.0
- Status: Draft
- Scope: Real-time market signal capture system

## 1. System Definition
### Identity
- System Name: Signal Capture System
- Chinese Name: 实时市场信号捕获系统
- Positioning: A real-time signal capture and generation system based on ICT / SMC structure recognition

### Goal
- Continuously capture real-time market data
- Maintain market state awareness
- Detect structure changes and liquidity behavior
- Fuse multi-layer domain information
- Generate stable and traceable trading signals

### System Role
- This system is the behavioral boundary for market signal capture and decision generation.

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

### 4.2 Data Pipeline Domain
- Owns validation, standardization, caching, and distribution behavior
- Does not assign market semantics
- Does not make decision-level judgments

### 4.3 Market State Domain
- Owns the current market state behavior
- Does not generate final signals
- Does not own raw data intake

### 4.4 Market Structure Domain
- Owns structure change recognition behavior
- Does not execute trades
- Does not own data ingestion behavior

### 4.5 Liquidity & Imbalance Domain
- Owns liquidity and imbalance recognition behavior
- Does not generate final signals
- Does not manage market state ownership

### 4.6 Reversal / Continuation Domain
- Owns directional tendency behavior
- Does not replace signal decision behavior
- Does not own upstream data ingestion

### 4.7 Signal Decision Domain
- Owns final signal decision behavior
- Fuses information from other domains
- Does not own upstream analysis domains

## 5. Boundary Rules
- Each domain owns one stable responsibility area.
- Each object must have one owner.
- Non-owner domains may read only.
- Non-owner domains must not modify owned objects.
- Data movement between domains must follow event notify + data pull.

