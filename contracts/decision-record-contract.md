# Decision Record Contract

**Status:** Draft  
**Version:** 0.1.0  
**Producer:** Decision engines and explainability layer  
**Consumers:** Architecture service, results UI, export/report layer.

## 1. Purpose

Define a consistent, traceable representation for every important engineering recommendation. The record must explain the decision made by an actual rule, using the values that the rule evaluated.

## 2. Proposed record shape

```js
{
  id: "decision-media-link-a",
  domain: "physical-media",
  subjectId: "link-a",
  outcome: {
    selected: "OS2-SMF",
    technology: "10GBASE-LR"
  },
  rule: {
    id: "MEDIA_DISTANCE_OVER_400M",
    description: "Long-distance link selection rule"
  },
  inputs: {
    distanceM: 22000,
    bandwidthBps: 10000000000
  },
  explanation: "OS2 single-mode fiber was selected because ...",
  standards: [],
  assumptions: [],
  warnings: []
}
```

This is illustrative. Exact enumerations, optional fields, and standards-reference format remain to be finalized.

## 3. Required semantics

- `id` is unique within the architecture result.
- `domain` identifies the decision area.
- `subjectId` identifies the affected site, device, link, subnet, or routing domain where applicable.
- `outcome` records what was selected or concluded.
- `rule.id` identifies the deterministic rule that fired.
- `inputs` records the relevant values used by that rule.
- `explanation` is readable by a student or network reviewer.
- `standards` contains only references that have been checked and are relevant.
- `assumptions` exposes assumptions that affect the decision.
- `warnings` exposes limitations, unresolved requirements, or operator actions.

## 4. Behavioural rules

1. Explanations must be derived from the same evaluated values as the decision.
2. The explainability layer must not change an engine's decision.
3. The UI must not need to reconstruct the technical rationale from raw inputs.
4. A decision must not claim a standard mandates something unless that claim has been verified.
5. If the result depends on an assumption, the assumption must be visible.
6. If the engine cannot make a justified recommendation, it must return an explicit unresolved result or error, not a fabricated choice.

## 5. Acceptance tests

- A media decision's explanation includes the evaluated link distance.
- A VLSM decision can identify the host request and allocated prefix.
- A routing decision identifies the rule and topology conditions that triggered it.
- An assumption is visible in the exported explanation.
- A decision record cannot contradict the actual engine result.
- Missing standards evidence is not replaced with an invented citation.
