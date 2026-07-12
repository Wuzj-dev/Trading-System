# Version Policy

## Purpose
This document defines how versions are named and how documentation and policy changes are tracked.

## Version Naming
- `vMAJOR.MINOR.PATCH-area`

## Meaning
- `MAJOR`: stable foundation or structural change
- `MINOR`: domain specification or policy addition
- `PATCH`: document correction, clarification, or non-behavioral adjustment
- `area`: the affected area such as `docs`, `foundation`, `sds`, `policy`, or `review`

## Recommended Usage
- `v1.0.0-docs`
- `v1.0.1-foundation`
- `v1.0.2-sds`
- `v1.0.3-review`

## Rules
- Foundation changes must be explicit and reviewed.
- SDS changes must remain behavior-only.
- Policy changes may evolve without rewriting SDS.
- Every release must identify what changed and why.

