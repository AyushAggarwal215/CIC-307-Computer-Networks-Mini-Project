# Module Contract Template

**Module:** `<module-name>`  
**Owner:** `<developer/team member>`  
**Status:** Draft  
**Version:** 0.1.0  
**Last reviewed:** `<date>`

## 1. Purpose

Describe the single responsibility of this module in two or three sentences.

## 2. Scope

### The module is responsible for
- ...

### The module is not responsible for
- ...

## 3. Dependencies

List the contracts and modules this module relies on. Avoid depending on another module's private implementation.

## 4. Public interface

For every exported function, specify:

| Field | Required detail |
|---|---|
| Function name | Exact exported name |
| Signature | Parameters and return type/shape |
| Input constraints | Required fields, valid ranges, allowed values |
| Output | Exact structure and meaning of each field |
| Side effects | Prefer none for computation engines |
| Errors | Exact error types/codes and when they occur |

## 5. Behavioural rules

Write deterministic rules in language that can be tested. Avoid vague phrases such as "handles properly" or "uses the best option."

## 6. Invariants

State facts that must always remain true, including before and after execution.

## 7. Error handling

For each error, define:
- Error code/name
- Trigger condition
- Relevant context included
- Whether the caller can recover
- Expected caller behaviour

## 8. Acceptance tests

List observable test cases. Include normal cases, boundary cases, invalid inputs, and failure cases.

## 9. Example

Provide at least one realistic input and its expected output. Label illustrative values clearly.

## 10. Change history

| Version | Change | Author | Review status |
|---|---|---|---|
| 0.1.0 | Initial draft | TBD | Pending |
