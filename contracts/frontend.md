# NetArchitect — Frontend Contract

**Contract ID:** FE-001  
**Version:** 1.0.0  
**Status:** Approved for implementation  
**Release scope:** v0.1–v0.7

---

## 1. Purpose

The frontend provides the user interface for NetArchitect, a network architecture recommendation and configuration generation system.

It allows users to select predefined network scenarios, review their parameters, execute architecture analysis, inspect recommendations, understand engineering decisions, access diagnostics and manage previous analysis sessions.

The frontend is responsible for presentation, user interaction, application state and session management.

It must not independently implement networking calculations or duplicate the logic of the core engineering modules.

## 2. Technology and design decisions

- JavaScript ES Modules.
- Vite.
- Vanilla JavaScript, HTML and CSS for the initial implementation.
- IndexedDB for persistent session storage.
- localStorage for lightweight UI preferences.
- Responsive, desktop-first layout.
- Light-first monochrome visual design.

No backend server or database is required.

## 3. Application navigation

The application must have exactly three primary navigation tabs.

### 3.1 Knowledge Base

Purpose: Explain the networking concepts, engineering rules and internal decision-making processes used by NetArchitect.

Sections:

- Physical media selection.
- Network topology.
- IP addressing and VLSM.
- Switching and VLANs.
- Routing protocols.
- Network security.
- Decision rules and mathematical justifications.
- Standards and RFC references.
- Internal system architecture.

Each article should support:

- Title and description.
- Technical explanation.
- Applicable formulas and rules.
- Example calculations.
- Related decision rule IDs.
- Relevant standards and references.
- Implementation status: Implemented, Planned or General Reference.

The Knowledge Base must not claim that a feature is implemented when it is only planned.

### 3.2 Architect

Purpose: Provide the primary network design workflow.

The workflow consists of four stages.

**Stage A — Scenario Selection**

Display three predefined scenarios:

1. Campus LAN.
2. Metro MAN.
3. Global WAN.

Each scenario card must display its name, scope, number of sites, endpoint information and major requirements.

The user must be able to select a scenario and proceed to configuration review.

**Stage B — Configuration Review**

Display the selected scenario's parameters in organized sections:

- Physical infrastructure.
- Sites, devices and links.
- Endpoint requirements.
- Addressing requirements.
- Switching requirements.
- Routing requirements.
- Security requirements.
- Resiliency requirements.

Scenario values must originate from static JSON files.

The initial release does not require a custom scenario builder. Parameter editing must only be exposed when the corresponding override and validation behavior has been defined.

Available actions:

- Review configuration.
- Reset to preset.
- Run architecture analysis.

**Stage C — Analysis Results**

Display the outputs returned by the architecture service.

Result sections:

- Architecture overview.
- Physical media and link design.
- Logical topology.
- VLAN and switching design.
- IP addressing and subnet allocation.
- Routing recommendations.
- Security recommendations.
- Generated device configurations.
- Decision trace.

Every recommendation must be traceable to its corresponding decision record.

The frontend must display the actual results returned by the core and must not invent missing recommendations.

**Stage D — Export**

Support:

- Individual device configuration downloads.
- ZIP export of all generated configurations.
- CSV export of subnet allocations.
- Architecture decision report export.
- Session saving.

Only successfully generated and validated outputs should be presented as verified configurations.

### 3.3 Errors

Purpose: Provide a centralized diagnostics interface.

Diagnostic categories:

- Input validation errors.
- Physical constraint violations.
- Addressing errors.
- Routing errors.
- Security warnings.
- Configuration generation warnings.
- Informational assumptions.

Every diagnostic should support the following fields:

- Diagnostic ID.
- Error code.
- Severity.
- Description.
- Affected entity or parameter.
- Expected constraint.
- Actual value, where available.
- Suggested resolution.
- Related engine.
- Related decision ID, where applicable.

Severity levels:

- Error: A blocking problem.
- Warning: Analysis can continue, but the result requires qualification.
- Info: An assumption, limitation or informational message.

Selecting a diagnostic should navigate to the relevant configuration or result section whenever possible.

## 4. Session History Drawer

Session History is a persistent drawer, not a fourth primary navigation tab.

It must be accessible from all three primary sections.

### 4.1 Session information

Each session must preserve:

- Unique session ID.
- Session name.
- Scenario identifier.
- Scenario version.
- Original scenario input.
- User overrides, if applicable.
- Normalized network model.
- Analysis results.
- Decision records.
- Diagnostics.
- Generated configurations.
- Creation timestamp.
- Last modified timestamp.
- Session status.

### 4.2 Session operations

The user must be able to:

- Create and save a session.
- Open an existing session.
- Rename a session.
- Duplicate a session.
- Delete a session.
- Export a session.
- Compare two sessions.

### 4.3 Persistence

IndexedDB is the primary persistent storage mechanism.

Session information must survive browser refreshes and restarts.

localStorage may only be used for small UI preferences, such as theme and selected navigation state.

The application must handle storage errors and unavailable storage gracefully.

Session data must not be silently discarded when an operation fails.

## 5. Visual Design Contract

### 5.1 Theme

The application uses a light-first monochrome design.

Primary visual characteristics:

- White or near-white page background.
- Black primary text.
- Neutral grey secondary text.
- Light grey borders and separators.
- Black primary action buttons.
- Restrained shadows.
- Minimal decoration.
- Clear typography and spacing.

Avoid:

- Excessive gradients.
- Unnecessary bright colors.
- Decorative animations.
- Heavy shadows.
- Oversized dashboard cards.
- Unnecessary visual clutter.

Color may be used sparingly for meaningful diagnostic severity indicators.

### 5.2 Layout

The application must have:

- Persistent primary navigation.
- Main content area.
- Session History drawer.
- Consistent page headers.
- Reusable content panels.
- Responsive layouts.

### 5.3 Interaction

All interactive elements must have defined behavior.

Loading, empty, success, warning and error states must be represented clearly.

The interface must support keyboard navigation and accessible contrast.

## 6. Frontend Architecture

Recommended structure:

```text
src/ui/
├── app/
│   ├── app-shell.js
│   ├── navigation.js
│   └── app-state.js
│
├── knowledge-base/
│   ├── knowledge-view.js
│   ├── article-view.js
│   └── knowledge-data.js
│
├── architect/
│   ├── scenario-selector.js
│   ├── scenario-config.js
│   ├── analysis-view.js
│   ├── result-panels/
│   └── export-view.js
│
├── diagnostics/
│   ├── error-view.js
│   └── diagnostic-detail.js
│
├── sessions/
│   ├── session-drawer.js
│   ├── session-list.js
│   ├── session-detail.js
│   └── session-storage.js
│
└── shared/
    ├── modal.js
    ├── tabs.js
    ├── status-badge.js
    └── loading-state.js
```

This structure defines responsibilities, not permission to create unnecessary abstractions. Components should remain small and independently understandable.

## 7. Core Integration Contract

The frontend communicates with the core through the architecture service.

It must not directly import individual networking algorithms.

The architecture service is responsible for accepting analysis input and returning the architecture result.

The frontend must handle:

- Successful analysis.
- Validation failures.
- Engineering errors.
- Warnings.
- Configuration generation failures.
- Unexpected execution failures.

The session storage module must be independent of the architecture calculation engines.

Knowledge Base content must be independent of the calculation engines, while accurately documenting their implemented rules.

## 8. Application State

The application must maintain state for:

- Active primary tab.
- Selected scenario.
- Current scenario configuration.
- Analysis execution status.
- Analysis result.
- Current diagnostics.
- Session drawer visibility.
- Active session ID.
- Session list.
- Export status.

The application must avoid unnecessary duplicate state.

Changing tabs must not accidentally discard the active analysis or session.

## 9. Testing Requirements

### Navigation

- All three primary tabs must work.
- Session drawer must be accessible from every tab.
- Drawer visibility must not change the active primary tab.

### Scenario workflow

- All three presets must be selectable.
- Selecting a scenario must load the correct parameters.
- Changing scenarios must not accidentally retain unrelated results.
- Reset must restore the original preset.

### Analysis results

- Results must reflect the architecture service output.
- Missing results must be handled gracefully.
- Decision records must be accessible from relevant recommendations.

### Diagnostics

- Errors, warnings and informational messages must be distinguishable.
- Selecting a diagnostic must navigate to its context where possible.

### Sessions

- Sessions must persist after browser restart.
- Opening a session must restore its saved information.
- Rename, duplicate and delete operations must work.
- Storage failures must be handled without silent data loss.

### Export

- Exported content must match the actual analysis result.
- Failed or incomplete exports must be reported clearly.

## 10. Acceptance Criteria

The frontend contract is satisfied when:

1. The application provides exactly three primary navigation tabs.
2. The interface follows the approved light monochrome design.
3. All three predefined scenarios are selectable.
4. The configuration view displays the selected scenario's actual parameters.
5. Analysis results are obtained through the architecture service.
6. Recommendations expose their decision explanations.
7. Diagnostics are centrally accessible.
8. Session History is available through a persistent drawer.
9. Sessions survive browser restarts.
10. Session management operations work correctly.
11. Export operations produce the expected files.
12. Networking algorithms remain exclusively within the core.
13. The interface handles loading, empty, error and success states.
14. Automated tests cover the critical frontend workflows.

## 11. Implementation Restrictions

- Do not implement networking algorithms in the UI.
- Do not duplicate validation rules in frontend components.
- Do not introduce a backend server or database.
- Do not introduce a custom scenario builder in the initial release.
- Do not fabricate architecture results or decision explanations.
- Do not silently ignore storage or export failures.
- Do not modify core contracts without coordination.
- Do not introduce dependencies without a clear requirement.

## 12. Release Mapping

| Release | Frontend scope |
|---|---|
| 0.1 | Application shell, navigation and design system |
| 0.2 | Scenario selection and configuration review |
| 0.3–0.5 | Results panels as core engines become available |
| 0.6 | Decision trace and diagnostics integration |
| 0.7 | Session drawer, persistent storage and exports |
| 0.8 | Responsive behavior, accessibility and verification |

---

**End of Contract FE-001**