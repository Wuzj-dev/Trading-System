# Freeze Checklist

## Purpose
This checklist defines the minimum requirements for freezing Foundation, SDS, or Policy documents.

## Foundation Checks
- The document defines only stable principles.
- The document does not include implementation details.
- The document does not contain domain-specific behavior.
- The document has ADR support if a foundation rule changed.

## SDS Checks
- The document follows the standard chapter order.
- The document describes behavior only.
- The document does not mention implementation methods.
- The document uses stable ownership language.
- The document separates Policy from behavior.

## Policy Checks
- The document contains configurable rules only.
- The document does not rewrite SDS behavior.
- The document clearly identifies defaults and version differences.

## Ownership Checks
- Every object has exactly one Owner.
- Non-owner domains are read-only.
- No domain manages another domain's objects.

## Interaction Checks
- Events notify only.
- Business data is pulled from the owner domain.
- No circular dependency exists.

## Runtime Checks
- Health is defined.
- Ready is defined.
- Runtime status is queryable.

## Freeze Gate
The document may be marked as Freeze only when all required checks pass.

