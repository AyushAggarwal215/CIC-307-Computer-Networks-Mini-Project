# Validation Contract

**Status:** Draft  
**Version:** 0.1.0  
**Input:** Common Network Model  
**Output:** Validation result or structured validation errors.

## 1. Purpose

Validate the normalized network model before it is passed to computation engines. Validation must catch malformed structure, invalid references, impossible values, and contradictory requirements early.

## 2. Responsibility

The validator checks model integrity and general input constraints. Domain-specific engines remain responsible for errors that can only be determined while applying their own algorithms.

The validator must not select media, allocate subnets, choose routing protocols, or generate configurations.

## 3. Proposed interface

```js
validateNetworkModel(networkModel)
```

Proposed result:

```js
{
  valid: true,
  errors: [],
  warnings: []
}
```

If invalid, `valid` is false and `errors` contains one or more structured issues. The exact issue schema must be approved before implementation.

## 4. Validation categories

### Structural validation
- Required top-level fields exist.
- Collections have the expected types.
- Entity IDs are present and unique.
- Field types match the model contract.
- Enumerated values are supported.

### Referential validation
- Every device references an existing site.
- Every link references valid endpoints.
- VLAN, subnet, and routing references resolve where those relationships are modelled.

### Value validation
- Distances are finite and non-negative.
- Counts are valid non-negative integers, with zero allowed only where meaningful.
- Bandwidth and latency values use the agreed units and valid ranges.
- CIDR prefixes are syntactically valid and within supported IPv4 limits for version 1.

### Cross-field validation
- Contradictory requirements are reported.
- Required link endpoints and site relationships are coherent.
- Explicit requirements are not silently overridden by inferred recommendations.

## 5. Error format

Proposed issue shape:

```js
{
  code: "INVALID_LINK_DISTANCE",
  path: "links[0].distanceM",
  message: "Link distance must be a finite, non-negative number.",
  severity: "error",
  entityId: "link-a"
}
```

Warnings use `severity: "warning"` and must not make `valid` false unless the team explicitly defines that warning as blocking.

## 6. Domain-error boundary

The following belong to domain engines rather than generic model validation:
- Copper run exceeds 100 m.
- VLSM root prefix lacks sufficient address space.
- Requested topology cannot satisfy resiliency requirements.
- Transceiver/media combination is unsupported.
- A routing requirement cannot be satisfied by the supplied topology.

The validator may detect obviously malformed fields, but it must not duplicate engine calculations.

## 7. Invariants

- Validation is deterministic for the same input.
- Validation does not mutate the input model.
- Errors identify the offending field or entity where possible.
- The validator returns all safely detectable issues in one pass rather than stopping at the first unrelated error.
- It does not invent defaults to make invalid input pass.

## 8. Acceptance tests

- Missing required top-level property.
- Duplicate site, device, or link IDs.
- Device references unknown site.
- Link references unknown endpoint.
- Negative, non-finite, or malformed distance.
- Invalid endpoint counts.
- Invalid IPv4 prefix syntax.
- Unsupported enum value.
- Contradictory or unresolved cross-references.
- Valid model produces no errors and does not mutate its input.
