# ADR 0002: Trading Signal Detection Domain Boundary

## Status
Accepted

## Context
The repository needs a first domain definition for the trading signal detection program.

## Decision
The Trading Signal Detection Domain owns signal detection behavior only.

## Consequences
- Trade execution stays outside this domain.
- Position management stays outside this domain.
- The domain remains focused on signal-related behavior.

