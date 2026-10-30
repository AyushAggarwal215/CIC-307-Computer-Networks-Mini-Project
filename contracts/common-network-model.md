# Common Network Model Contract

**Status:** Draft — foundational contract  
**Version:** 0.1.0  
**Consumers:** Validation, media, topology, addressing, routing, security, explainability, configuration, and orchestration modules.

## 1. Purpose

Define the normalized representation of a network that all shared computation engines consume. The three scenario adapters may accept different source JSON formats, but they must produce this common representation.

This contract defines the conceptual model first. Exact JavaScript types, enumerations, and required/optional status must be approved before implementation.

## 2. Design proposal

**Proposed representation: graph-oriented topology with site ownership.**

- A `sites` collection represents physical or logical locations.
- A `devices` collection represents network devices.
- A `links` collection represents connections between devices or network endpoints.
- Each device belongs to a site through `siteId`.
- Each link identifies its two endpoints.
- A site may contain many devices; a device may participate in multiple links.
- The UI may display a hierarchical view (sites containing devices), but the canonical connectivity representation is the links graph.

This is a proposal, not yet an approved decision. It supports redundant paths and links between sites without forcing the network into a tree. The team must approve or revise it before engine implementation.

## 3. Top-level model

Proposed conceptual shape:

```js
{
  schemaVersion: "1.0",
  profileId: "campus-lan",
  displayName: "Campus LAN",

  sites: [],
  devices: [],
  links: [],

  endpointGroups: [],
  addressing: {},
  switching: {},
  routing: {},
  security: {},
  performance: {},
  resiliency: {},

  assumptions: []
}
```

The shape above is illustrative. It is not yet a final JSON Schema or TypeScript definition.

## 4. Entity responsibilities

### 4.1 Network

Represents the complete normalized design input.

Expected identity fields:
- `schemaVersion`: version of this internal model contract.
- `profileId`: stable identifier of the source profile.
- `displayName`: human-readable profile name.

### 4.2 Site

Represents a location or network site, such as a campus building, headquarters, remote office, city, or cloud location.

Candidate fields:
- `id`: stable unique identifier.
- `name`: human-readable name.
- `type`: controlled category, to be finalized.
- `metadata`: optional profile-specific descriptive data.

A site does not itself imply a particular topology.

### 4.3 Device

Represents a network device or a logical network endpoint that participates in connectivity.

Candidate fields:
- `id`
- `siteId`
- `name`
- `role` (for example, router, switch, firewall, access point, or cloud gateway)
- `vendor` and `platform`, only when known and relevant to configuration generation
- `capabilities`, only when explicitly supplied or selected by a documented assumption

The model must distinguish a real specified device from a logical placeholder used during planning.

### 4.4 Link

Represents a connection between two endpoints.

Candidate fields:
- `id`
- `sourceEndpointId`
- `targetEndpointId`
- `distanceM`
- `bandwidthRequirement`
- `requestedMedium` (optional)
- `duplex` or other link properties only if required by a rule or configuration
- `resiliencyRole` (optional)

A link is evaluated independently by the media engine. A link may be physical or logical; the model must make this distinction explicit before implementation.

### 4.5 Endpoint group

Represents a group of users or devices with shared network requirements.

Candidate fields:
- `id`
- `name`
- `endpointType`
- `count`
- `requirements` such as PoE, VLAN membership, or traffic class where applicable.

Endpoint counts must not automatically be treated as subnet host requests. Addressing requirements are separately represented.

### 4.6 Addressing

Represents the root IPv4 allocation and requested subnet groups.

Candidate fields:
- `rootPrefix`
- `subnetRequests[]`, each containing a stable ID, name, required usable host count, and optional VLAN association.

The addressing engine must not infer individual subnet host counts from the total endpoint count unless the input explicitly defines that mapping.

### 4.7 Switching

Represents Layer 2 requirements such as VLANs, trunk relationships, spanning-tree requirements, and switching constraints.

Exact fields are pending the switching/configuration design.

### 4.8 Routing

Represents routing scope and constraints, such as routing domains, participating sites, protocol requirements, and route summarization requirements.

Exact fields are pending the routing contract.

### 4.9 Security

Represents required security controls, such as ACL policy requirements and VPN requirements. Secrets and credentials must not be embedded in preset scenario JSON.

### 4.10 Performance and resiliency

Represent measurable requirements such as bandwidth, latency targets, availability expectations, redundancy requirements, and failover constraints. A target is not a guarantee; provider- or platform-dependent requirements must be labelled as such.

### 4.11 Assumptions

Represents assumptions introduced because a scenario does not provide every engineering input.

Each assumption should have:
- A stable ID.
- A short description.
- The affected entity or decision domain.
- A reason it is needed.
- A confidence or status field if the team adopts one.

An assumption must not be disguised as a supplied fact.

## 5. Model invariants

1. IDs are unique within their entity collection.
2. Every device's `siteId` refers to an existing site.
3. Every link endpoint refers to an existing device or explicitly modelled endpoint.
4. A link cannot connect an endpoint to itself unless a specific supported use case defines that behaviour.
5. Distances use metres internally.
6. Bandwidth values use one documented unit convention throughout the model.
7. IPv4 prefixes use CIDR notation.
8. Host demand for VLSM is explicit in each subnet request.
9. Unknown values are not replaced with fabricated defaults.
10. Scenario-specific details that do not fit the common model must be preserved through a documented extension mechanism or adapter metadata; they must not be silently discarded.

## 6. Normalization rules

- Adapters convert source units into the model's canonical units.
- Adapters map source identifiers to stable model IDs.
- Adapters preserve the source profile identity.
- Adapters do not make media, routing, or VLSM decisions.
- Adapters must report missing mandatory source information instead of guessing.
- Adapters may add assumptions only when the assumption is explicit, traceable, and approved by the relevant rule contract.

## 7. Open decisions before approval

- Approve graph-oriented connectivity with site ownership, or choose a hierarchical canonical model.
- Define whether links may connect devices only, or devices and logical endpoints.
- Define controlled values for site type, device role, endpoint type, and medium.
- Decide the bandwidth representation (for example, integer bits per second).
- Decide whether the model supports IPv6 in version 1 or explicitly limits addressing to IPv4.
- Finalize exact required and optional fields for each of the three profiles.
- Decide how unknown device platform/capability data is represented.

## 8. Acceptance criteria

- All three profile adapters can represent their required sites and links without losing meaningful connectivity.
- The model can represent redundant paths and multi-site connectivity.
- The model distinguishes endpoint population from subnet host demand.
- The model can represent all inputs required by the media, topology, VLSM, routing, and security contracts.
- Invalid references and duplicate IDs can be detected by validation.
- The same normalized model can be passed to shared engines regardless of which preset produced it.
