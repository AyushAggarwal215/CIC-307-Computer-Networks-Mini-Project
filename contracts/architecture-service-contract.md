# Architecture Service Contract

**Status:** Draft  
**Version:** 0.1.0  
**Consumer:** UI  
**Dependencies:** Scenario loader, adapters, validator, decision engines, explainability, config generator.

## 1. Purpose

Provide one application-level entry point that coordinates the analysis of a selected scenario. The service connects the modules in the correct order and returns one structured architecture result to the UI.

The service is orchestration only. It must not contain the internal algorithms for media selection, VLSM, routing, or configuration generation.

## 2. Proposed interface

```js
async function generateArchitecture(profileId)
```

The service:
1. Loads the selected static scenario JSON.
2. Selects the matching scenario adapter.
3. Converts source data into the Common Network Model.
4. Validates the normalized model.
5. Stops and returns structured validation errors if blocking errors exist.
6. Runs the applicable analysis engines.
7. Collects engine outputs and decision records.
8. Generates supported device configurations from validated results.
9. Returns a complete architecture result.

The exact dependency-injection strategy and result envelope must be agreed before implementation.

## 3. Proposed result envelope

```js
{
  status: "success",
  profileId: "campus-lan",
  inputSummary: {},
  validation: {
    errors: [],
    warnings: []
  },
  analysis: {
    media: {},
    topology: {},
    addressing: {},
    routing: {},
    security: {}
  },
  decisions: [],
  configurations: [],
  assumptions: [],
  warnings: []
}
```

A failed analysis must return a structured failure with enough context for the UI to explain what needs attention. The service must not return a success status when required engines have failed.

## 4. Responsibility boundaries

The service may:
- Select the adapter for a profile.
- Call modules in dependency order.
- Pass validated outputs between modules.
- Aggregate results, warnings, and errors.
- Ensure configuration generation only receives appropriate analysis results.

The service may not:
- Contain profile-specific hard-coded recommendations.
- Duplicate validation or engine algorithms.
- Rewrite engine decisions to make the UI look successful.
- Hide warnings or assumptions.
- Directly manipulate the DOM or UI state.

## 5. Error behaviour

Errors should preserve their source module and structured code. The service may add orchestration context, but must not erase the original error identity or message.

Examples:
- Scenario file not found.
- Unknown profile ID.
- Adapter rejected source schema.
- Normalized model validation failed.
- Media engine raised a physical-distance violation.
- VLSM engine reported address-space exhaustion.
- Configuration generation could not produce a supported output.

The team must decide whether independent engines continue after a non-blocking domain warning, and how partial results are represented.

## 6. Acceptance tests

- Each of the three profile IDs selects the correct loader and adapter.
- Unknown profile ID returns a structured error.
- Invalid normalized input does not proceed to configuration generation.
- A valid profile invokes each applicable engine once.
- Engine errors retain their original code and context.
- Decisions and warnings from engines are preserved in the final result.
- Configuration generation consumes analysis outputs rather than raw scenario JSON.
- The service does not mutate the source scenario object.
