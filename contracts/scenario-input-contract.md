# Scenario Input Contract

**Status:** Draft  
**Version:** 0.1.0  
**Producer:** `public/scenarios/*.json`  
**Consumer:** Scenario loader and scenario-specific adapters.

## 1. Purpose

Define how the three predefined scenario files are stored, loaded, and normalized. Each profile may have a source structure that reflects its own requirements. The adapter—not the shared engine—maps that structure into the Common Network Model.

## 2. Supported profiles in version 1

| Profile ID | Scenario | Core characteristics from assignment |
|---|---|---|
| `campus-lan` | Campus LAN | 3 buildings, under 400 m, 1,600 endpoints, PoE access points, Cat6a/MMF, VLANs, RSTP, single-area OSPF |
| `metro-man` | Metro MAN | HQ and 2 remote sites, 15–35 km, 450 users, FinTech colocation, OS2 SMF, EVPL, sub-5 ms QoS target, dual-homed failover |
| `global-wan` | Global WAN | 4 cities and a cloud VPC, 2,000 IoT/scanner endpoints, IPsec VPN, multi-area OSPF/BGP, route summarization |

These characteristics are assignment-derived. Exact link layouts, subnet demands, device inventories, and numeric engineering parameters must be specified in the actual scenario data or clearly labelled assumptions.

## 3. File and loading rules

- Store one JSON file per profile under `public/scenarios/`.
- Use stable file names: `campus-lan.json`, `metro-man.json`, `global-wan.json`.
- Each file must include a profile identifier and schema version.
- The loader must reject malformed JSON and unknown profile IDs.
- The loader must not mutate the loaded source object.
- The loader returns source data; the adapter performs normalization.
- A static JSON fetch from GitHub Pages is a file request, not an application backend API.

## 4. Adapter interface

Proposed interface:

```js
adaptCampusScenario(rawScenario)
adaptMetroScenario(rawScenario)
adaptGlobalScenario(rawScenario)
```

Each function:
- Accepts one parsed source scenario object.
- Checks profile identity and supported source schema version.
- Returns a normalized Common Network Model object.
- Does not run media, routing, topology, or VLSM algorithms.
- Does not generate configuration text.
- Reports malformed or unsupported input through the agreed validation/error format.

The final function signatures and result envelope will be aligned with the validation contract.

## 5. Profile-specific requirements to preserve

### Campus LAN

The adapter must preserve:
- Three distinct buildings.
- Relevant inter-building distances, not merely one aggregate distance if links differ.
- Endpoint population and PoE access-point requirements.
- VLAN and RSTP requirements.
- Single-area OSPF requirement.
- Addressing requests, if specified in the scenario file.

### Metro MAN

The adapter must preserve:
- HQ and two remote sites.
- Individual inter-site link distances in the stated 15–35 km range, where supplied.
- User population.
- FinTech colocation requirement.
- OS2 SMF and EVPL requirements.
- Sub-5 ms QoS target as a target/constraint, not a guaranteed outcome.
- Dual-homed failover requirement.

### Global WAN

The adapter must preserve:
- Four city sites and one cloud VPC.
- IoT/scanner endpoint population.
- IPsec VPN requirement.
- Multi-area OSPF/BGP requirements.
- Route summarization requirement.
- Addressing and security inputs, if specified.

## 6. Validation boundary

Source-shape validation belongs to the loader/adapter boundary. Engineering validation belongs to the normalized model validator and the relevant engine.

Examples:
- Missing `profileId`: source/adapter error.
- Link references an unknown device after normalization: model validation error.
- Copper requested for a run longer than 100 m: media-rule error.
- Root prefix cannot fit requested subnets: VLSM allocation error.

## 7. Acceptance criteria

- Each of the three files loads independently.
- Each file is accepted only by its matching adapter.
- Each adapter produces the same top-level normalized model contract.
- No adapter silently fabricates distances, bandwidth, subnet demands, device capabilities, optical-budget inputs, or provider guarantees.
- A malformed or unsupported file produces a structured, actionable error.
