# Freeze Checklist

## Purpose
This checklist defines the minimum review standard before a Foundation document, SDS document, or Policy document can be marked as Freeze.

## 1. Foundation Checks
- The document defines only stable system-level principles.
- The document does not include implementation details.
- The document does not define domain-specific behavior.
- The document does not mix configurable values into stable principles.
- Any foundation change has an ADR reference.

## 2. SDS Checks
- The document follows the required chapter order.
- The document describes behavior only.
- The document does not mention programming language, framework, data structure, algorithm, or implementation method.
- Responsibilities are expressed as verb phrases.
- Managed Objects are expressed as nouns.
- Behavior rules use Must / Must Not wording.
- The document does not redefine Foundation.

## 3. Policy Checks
- The document contains only configurable rules, defaults, and version differences.
- The document does not rewrite SDS behavior definitions.
- The document is explicitly separated from stable design content.

## 4. Ownership Checks
- Every object has exactly one Owner.
- Non-owner domains have read-only access only.
- No domain claims responsibility for another domain's object lifecycle.

## 5. Interaction Checks
- Events are used for notification only.
- Business data is retrieved from the owner domain.
- Events do not carry business objects.
- No circular dependency exists.

## 6. Runtime Checks
- Health is defined.
- Ready is defined.
- Runtime status is queryable.
- Downstream systems do not need to infer runtime status.

## 7. Review Checks
- The document has been reviewed for boundary clarity.
- The document has been reviewed for ownership clarity.
- The document has been reviewed for stable vs configurable separation.
- The document has been reviewed for freeze readiness.

## 8. Freeze Gate
The document may be marked as Freeze only when all required checks pass.

## 9. Failure Handling
- If any required check fails, the document must return to review.
- If a Foundation rule must change, an ADR is required.
- If a policy value must change, update Policy only.

