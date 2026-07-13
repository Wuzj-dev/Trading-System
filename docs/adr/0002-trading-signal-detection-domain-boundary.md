# ADR 0002: Signal Decision Domain Boundary

## Status
Accepted

## Context
The repository needs a stable boundary definition for the final signal decision domain.

## Decision
The Signal Decision Domain owns final signal decision behavior only.

## Consequences
- Trade execution stays outside this domain.
- Position management stays outside this domain.
- Upstream analysis domains remain separate from this domain.
- The domain remains focused on final signal behavior.
