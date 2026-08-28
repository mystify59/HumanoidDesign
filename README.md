# Humanoid CAD and Simulation Skill Design

## Purpose

Create a reusable Codex skill named `humanoid-cad-simulation` that plans, executes, and verifies humanoid mechanical-design workflows through available MCP-connected CAD and CAE software. Autodesk Fusion, SOLIDWORKS, and Ansys Mechanical are the baseline applications. The architecture must allow later adapters for robot dynamics and interchange tools such as URDF, MuJoCo, Gazebo, and ROS 2.

The skill supplies engineering intake, planning, approval gates, verification, and reporting. It does not implement a CAD kernel, solver, or MCP server and must not imply professional certification of a design.

## Target Location

The completed skill project will be created at `E:\humanoid-cad-simulation`. The current repository contains this reviewed design specification. Writing to the target drive is a separate implementation action and may require filesystem approval.

## Users and Supported Work

The skill supports engineers, roboticists, researchers, students, and technical designers working on:

- Full humanoids, upper-body platforms, bipeds, research-scale robots, and individual humanoid subsystems.
- Clean-sheet designs and inspection, analysis, or revision of existing CAD.
- Frames, links, pelvises, torsos, limbs, feet, joint housings, actuator mounts, and associated interfaces.
- Structural-static, modal, buckling, fatigue, thermal-structural, and selected nonlinear or impact-oriented preparation when the connected solver supports them.
- Handoff between CAD mass properties and robot representations.

The skill must scale its modelling effort and verification burden to the consequence of failure. A chest frame, pelvis, hip-joint carrier, primary actuator mount, or other load-path-critical component may propagate damage or loss of control across the robot. A camera bracket, cosmetic cover, sensor mount, or local accessory flange may justify a lighter workflow when its failure remains local and does not create a secondary hazard.

The skill must distinguish structural FEA from rigid-body and gait simulation. A successful structural run is not evidence of balance, controller stability, or safe walking.

## Architecture

Use a CAD-neutral orchestrator with software-specific adapters:

```text
User request
    -> humanoid project intake
    -> requirements and assumptions registry
    -> CAD-neutral design/analysis plan
    -> live MCP capability discovery
    -> software adapter
    -> approval gate
    -> bounded MCP execution
    -> independent verification
    -> traceable engineering report
```

The planned skill layout is:

```text
humanoid-cad-simulation/
|-- SKILL.md
|-- agents/
|   `-- openai.yaml
`-- references/
    |-- project-intake.md
    |-- engineering-data-model.md
    |-- workflow-and-approvals.md
    |-- verification-gates.md
    |-- humanoid-load-cases.md
    |-- fusion-adapter.md
    |-- solidworks-adapter.md
    |-- ansys-adapter.md
    `-- reporting.md
```

`SKILL.md` contains shared routing and invariants. Detailed, conditional guidance lives in references and is loaded only when relevant.

## Capability Discovery

The skill must not assume that an application is installed, connected, licensed, open, or represented by a particular MCP tool schema. At the beginning of a tool-backed workflow it must:

1. Identify relevant connected MCP servers.
2. Discover their live tools, schemas, versions, and connection state.
3. Determine which operations are read-only, mutating, destructive, expensive, or unsupported.
4. Map available operations to the neutral plan.
5. Report gaps and use a manual or artifact-based handoff when possible.

Software-adapter references describe capability categories and mapping criteria, not fixed tool names. Live schemas are authoritative.

## Model Inspection

Prefer structured native data over screenshots. When supported, inspect:

- Application version, document identity, units, configuration, and save state.
- Assembly hierarchy, components, bodies, features, sketches, mates, and joints.
- Named parameters, dimensions, materials, and manufacturing metadata.
- Stable entity identifiers for faces, edges, axes, planes, and components.
- Mass, volume, centre of mass, and inertia tensors.
- Interference, clearance, minimum thickness, and bounding envelopes.
- Existing loads, contacts, constraints, mesh, analyses, and results.
- Exportable neutral or robot-model artifacts.

Viewport images may supplement structured inspection but cannot independently establish exact dimensions, topology, contacts, or material state.

## Project Intake

Intake is progressive rather than a single exhaustive questionnaire. The skill first classifies:

- Humanoid type, scale, total-mass target, payload, environment, and maturity.
- Full-system or subsystem scope.
- Clean-sheet or existing-model workflow.
- Intended activities and operating modes.
- Available CAD, actuator, material, test, robot-model, and load data.
- Required outputs and acceptance criteria.
- Component criticality and the consequence of failure.
- User-selected modelling and analysis fidelity for the current iteration.

It then requests only applicable details, including:

- Unit system and global coordinate convention.
- Link hierarchy, joint axes, travel, hard stops, stiffness, damping, and backlash.
- Actuator continuous and peak torque, transmission ratio, efficiency, and mounting interfaces.
- Bearings, shafts, fasteners, preload, cable routing, cooling, and keep-out envelopes.
- Materials, manufacturing processes, tolerances, and minimum thicknesses.
- Mass, centre-of-mass, and inertia budgets.
- Strength, stiffness, fatigue-life, buckling, and modal-frequency targets.
- User-selected or approved safety factors and margins for each applicable failure mode.
- Contact conditions, friction, support phases, accelerations, and duty cycles.

Every material input is labelled `provided`, `derived`, `assumed_pending_approval`, `unknown_blocking`, or `not_applicable`. Unknown values may be researched or estimated only when the user authorizes that work; estimates remain explicit assumptions.

### Failure-Consequence Classification

Classify each in-scope component or assembly by what happens if it fractures, detaches, deforms excessively, jams, or loses alignment. The classification is based on consequence, not merely component size:

- `critical`: Failure can cause collapse, uncontrolled motion, loss of a primary load path, loss of balance, actuator or joint release, major cascading damage, or a material hazard to people or surroundings.
- `important`: Failure degrades a major robot function, can damage adjacent equipment, or requires substantial repair, but does not normally cause immediate system collapse or uncontrolled motion.
- `local`: Failure is expected to remain confined to the component or nearby replaceable hardware, with limited effect on robot stability and no credible escalation into a critical hazard.
- `undetermined`: Available information is insufficient to classify the consequence. Treat this as blocking for mutation or solver execution until the user classifies it or approves a documented provisional classification.

The skill proposes a classification with its reasoning and asks the user to confirm or change it. It must consider secondary effects such as loose parts entering joints, sharp fragments, cable damage, sensor loss affecting control, obstruction of moving links, or a nominally small bracket becoming part of a lifting or safety load path.

### User-Controlled Fidelity

The user selects the modelling and analysis fidelity independently of component criticality:

- `concept`: Envelope, interfaces, approximate mass, gross clearance, and first-order load paths. Suitable for architecture exploration; not suitable for safety or release decisions.
- `preliminary`: Parametric geometry, representative materials and joints, dominant load cases, practical mesh controls, and initial sensitivity checks. Suitable for comparing design directions.
- `detailed`: Manufacturing-relevant geometry, verified interfaces, realistic connections and contacts, mesh-convergence evidence, applicable nonlinearities, and requirement-based result review. Suitable for design verification subject to stated limitations.
- `release-candidate`: Detailed fidelity plus configuration control, broader load-case coverage, documented convergence and sensitivity, traceable evidence, and explicit human engineering review. This does not itself constitute certification.

The skill recommends a minimum fidelity from failure consequence, design maturity, requested decision, and available evidence. A normal starting recommendation is `detailed` for critical components, `preliminary` for important components, and `concept` or `preliminary` for local components. These are recommendations rather than automatic settings.

The user retains control over the chosen fidelity. If the user selects a level below the recommendation, the skill records the mismatch, narrows the conclusions that may be drawn, and asks for explicit acknowledgement before execution. It must not describe an intentionally low-fidelity result as validating a critical component. Conversely, it must not impose expensive high-fidelity analysis on a local component unless its interfaces, failure mode, requested decision, or secondary hazards justify it.

### Execution Assurance Profile

The confirmed consequence class and fidelity become an execution assurance profile that remains active through design, simulation, verification, and reporting. It is not intake metadata only. The profile controls the depth of work while preserving the user's approved scope:

- Design decisions: load-path redundancy, geometry simplification, minimum sections, fillets and stress transitions, material allowables, joint architecture, fastener retention, hard stops, inspectability, replaceability, and tolerance treatment.
- CAD evidence: parameterization, manufacturing detail, interface checks, range-of-motion checks, interference coverage, mass-property verification, and configuration control.
- Analysis effort: load-case breadth, representation of contacts and joints, mesh refinement, convergence evidence, nonlinear effects, fatigue or buckling checks, uncertainty and sensitivity studies, and independent result checks.
- Review burden: number and granularity of checkpoints, required evidence, unresolved-risk tolerance, and need for explicit human engineering review.

The skill must not add unapproved features or redesign the robot merely because a higher assurance profile is selected. Instead, it uses the profile to compare candidate decisions, explain why a more robust choice is recommended, and obtain approval for material changes.

### Safety Factors and Margins

Treat safety factor as an explicit requirement per failure mode, not a universal number. The target must be selected or approved by the user after the skill identifies relevant drivers:

- Failure consequence and possibility of cascading damage.
- Uncertainty in loads, contacts, material data, manufacturing quality, degradation, and model assumptions.
- Static yield, ultimate failure, buckling, fatigue, joint slip or separation, bearing, fastener, impact, and other applicable modes.
- Applicable standards, test requirements, duty cycle, inspection strategy, redundancy, and design maturity.

Critical or uncertainty-dominated parts should normally receive higher required margins than local, well-characterized parts. Higher modelling fidelity can reduce numerical and modelling uncertainty, but does not by itself justify either raising or lowering the safety factor. The skill must show the proposed target and rationale during preflight and obtain approval before using it as a design constraint or pass/fail threshold.

When no governing standard or user target is available, the skill may propose a provisional factor or margin range, clearly labelled `assumed_pending_approval`. It must not silently select a value. Different failure modes may require different factors, and a stress safety factor cannot substitute for fatigue life, buckling margin, joint integrity, or impact assessment.

## Engineering Data Model

The neutral project state records:

- Project identity, revision, source artifacts, units, and coordinate frames.
- Requirements, priorities, and measurable acceptance criteria.
- Assemblies, links, components, interfaces, and keep-out envelopes.
- Joint definitions and actuator/transmission properties.
- Material and manufacturing constraints.
- Mass properties and budgets.
- Load cases, contacts, supports, and boundary conditions.
- Simulation type, mesh controls, solver controls, and requested results.
- Assumptions, evidence, provenance, confidence, and approval state.
- Verification observations, failures, waivers, and unresolved risks.
- Failure-consequence classification, selected fidelity, recommended fidelity, rationale, and any acknowledged mismatch.
- Execution assurance profile and approved safety factors or margins by failure mode.

Native entity identifiers are stored in adapter mappings. They do not become universal identifiers in the neutral model.

## Humanoid Load Cases

The skill selects candidate load cases from project activities but never silently supplies their magnitudes. Possible cases include:

- Double-leg standing and single-leg stance.
- Toe-off, heel strike, and maximum commanded joint acceleration.
- Squat, lift, payload-at-reach, stair ascent, and trip recovery.
- Emergency stop and actuator stall.
- Forward, backward, or lateral fall.
- Transport, handling, and maintenance loads.
- Thermal loading around motors, drives, batteries, and enclosed electronics.

Each selected case records load source, coordinate frame, magnitude, time basis, constraints, contacts, combination rule, uncertainty, and acceptance criteria. Loads should come from measured data, actuator limits, multibody simulation, test specifications, or explicitly approved conservative assumptions.

## Planning and Execution

For each requested workflow, the skill must:

1. Confirm scope and identify source-of-truth artifacts.
2. Inspect application and model state without mutation.
3. Update the neutral project state.
4. Identify missing, ambiguous, or contradictory inputs.
5. Confirm component criticality and the user-selected fidelity, then establish the execution assurance profile and conclusions it can support.
6. Confirm the safety-factor or margin targets applicable to each evaluated failure mode.
7. Produce a proportionate CAD or analysis plan with ordered checkpoints.
8. Present affected documents, proposed operations, assumptions, expected cost, recovery strategy, assurance profile, and any fidelity mismatch.
9. Obtain required approval.
10. Execute only the approved scope in bounded steps.
11. Verify each material checkpoint before continuing.
12. Stop on violated invariants or unexpected application state.
13. Report evidence, results, limitations, and suggested revisions.

The skill may adapt the plan after ordinary read-only discoveries. A change that materially expands scope, cost, model mutations, boundary conditions, or analysis type requires a revised preflight and approval.

## Approval Gates

Explicit approval is required before:

- Mutating source CAD or simulation models.
- Creating, deleting, suppressing, replacing, or materially changing geometry.
- Changing materials, mates, joints, contacts, constraints, or configurations.
- Overwriting an existing artifact or saving to an existing path.
- Applying an engineering assumption that materially affects results.
- Launching a solver run or materially increasing run cost.
- Executing arbitrary native scripts through an MCP tool.
- Performing destructive recovery or cleanup.

Read-only inspection, capability discovery, plan construction, and report preparation may proceed without approval. Approval for one plan does not authorize unrelated changes or retries with materially different settings.

## Recovery and Checkpoints

Before mutation, establish a recoverable state using the safest supported method: saved copy, versioned document, transaction, named checkpoint, or new output artifact. Never overwrite the only known-good model.

Stop and preserve the last verified checkpoint when:

- The active document or configuration differs from the approved target.
- Units or coordinate frames cannot be established.
- A stable reference cannot be resolved unambiguously.
- A tool changes unexpected topology or component state.
- An unknown modal dialog or application error appears.
- Required capability becomes unavailable.
- Solver or mesh behavior violates the approved criteria.

Automatic retries are allowed only for bounded, non-destructive transient failures with unchanged engineering meaning. Otherwise, explain the failure and request direction.

## CAD Verification Gates

Use supported independent checks after meaningful CAD mutations:

- Document identity, units, configuration, and saved revision.
- Expected feature, body, and component counts.
- Joint-axis, mate, and reference-frame alignment.
- Range of motion, interference, clearance, and keep-out compliance.
- Bounding envelope, volume, mass, centre of mass, and inertia.
- Minimum thickness and selected manufacturing rules.
- Expected merge or cut scope and topology.
- Neutral export plus reopen or downstream import when practical.
- Human-readable viewport evidence as a supplement.

A successful API response is execution evidence, not sufficient verification.

## Simulation Preflight and Verification

Before solving, review:

- Analysis type and whether linear assumptions are appropriate.
- Geometry revision and unit consistency.
- Material completeness and applicable constitutive data.
- Contacts, joint representations, fastener or preload assumptions, and friction.
- Loads, supports, remote points, coordinate frames, and load equilibrium.
- Mesh formulation, target size, quality metrics, refinement zones, and convergence criteria.
- Solver controls, nonlinear settings, expected runtime, and requested results.
- Approved safety factors or margins by failure mode, acceptance criteria, and known singularity risks.

After solving, inspect:

- Solver status, warnings, residuals, and convergence history.
- Mesh statistics and convergence evidence.
- Reaction balance and energy or force consistency where applicable.
- Stress singularities and whether peak values are physically interpretable.
- Deformation, stress, buckling factor, fatigue, frequency, temperature, or contact results relevant to the approved case.
- Sensitivity to important assumptions.
- Comparison with requirements and safety factors.

The report must distinguish solver output, engineering interpretation, assumptions, and unresolved uncertainty.

## Baseline Adapters

### Autodesk Fusion

Route through the connected Fusion MCP server and its add-in or bridge. Account for Fusion API thread restrictions by using server-supported queued operations rather than bypassing its bridge. Prefer named parameters, explicit sketches and features, stable references, and neutral exports. Do not assume every Fusion MCP implementation supports assemblies, simulation, or arbitrary scripts.

### SOLIDWORKS

Route through the connected SOLIDWORKS MCP server, normally backed by the Windows COM API. Prefer transaction or plan operations, stable entity identifiers, explicit document/configuration targeting, and structured geometry verification. Unknown modal dialogs require human inspection.

### Ansys Mechanical

Route through the available Ansys Mechanical MCP tools, commonly backed by PyMechanical remote sessions over gRPC. Confirm the active Mechanical instance and analysis system. Use structured operations where available and native scripting only with explicit approval. Preserve geometry revision, named selections, units, materials, mesh, loads, results, solver messages, and exported evidence.

## Reporting

Every completed run should report:

- Objective and approved scope.
- Source documents and revisions.
- Software, MCP server, and relevant capability versions.
- Requirements and acceptance criteria.
- Failure-consequence classification and selected analysis fidelity.
- Recommended fidelity and any user-acknowledged reduction from it.
- Execution assurance profile and how it influenced material design and analysis decisions.
- Approved safety factors or margins, their basis, and the failure modes to which they apply.
- Assumptions and their approval status.
- Operations performed and checkpoints created.
- Load cases, boundary conditions, contacts, mesh, and solver controls.
- Verification evidence and result summaries.
- Pass, conditional pass, fail, or inconclusive status per criterion.
- Limitations, unresolved risks, and recommended next actions.
- Paths or identifiers for resulting CAD, neutral exports, solver projects, plots, and reports.

Use `inconclusive` when evidence is insufficient. Never convert missing evidence into a pass.

## Security and Operational Boundaries

- Use local transports or authenticated trusted-network transports for CAD and solver control.
- Do not expose unauthenticated application bridges to untrusted networks.
- Treat native-script execution as high privilege.
- Keep credentials and tokens out of skill files, reports, prompts, and version control.
- Respect application licensing and the licenses of third-party MCP servers.
- Do not claim professional engineering certification or regulatory compliance.

## Validation Strategy

Validate the skill package structurally with the bundled skill validator and behaviorally with scenarios covering:

1. No relevant MCP server connected.
2. Fusion read-only model inspection.
3. SOLIDWORKS assembly edit with approval and rollback checkpoint.
4. Ansys static-structural preflight and solve approval.
5. Missing or contradictory boundary conditions.
6. Unit and coordinate-frame mismatch.
7. Unsupported MCP operation.
8. Unexpected topology change.
9. Mesh or nonlinear convergence failure.
10. Clean-sheet humanoid leg planning.
11. Existing-model revision and evidence preservation.
12. Rejection of an unjustified safety or certification conclusion.
13. Low-fidelity concept study for a local camera bracket.
14. User-selected concept analysis for a critical hip frame, with conclusions correctly restricted and the fidelity mismatch recorded.
15. Reclassification of an apparently local accessory when its detachment can obstruct a joint or damage control cabling.
16. Critical chest-frame execution where the assurance profile changes connection, mesh-convergence, load-case, and evidence requirements.
17. Local accessory execution that remains intentionally lightweight while documenting why broader analysis is unnecessary.
18. Missing safety-factor target that remains approval-blocking rather than being silently invented.

Behavioral validation should evaluate decisions and artifacts rather than exact prose. Live CAD or solver tests are optional integration tests and require installed, licensed applications and explicit authorization for their operations.

## Initial Implementation Boundary

The first implementation creates the skill instructions, UI metadata, and reference documents described here. It does not install third-party MCP servers, edit Codex MCP configuration, control live CAD/CAE applications, or create a robot design. Those are separate, explicitly authorized follow-on tasks.

## Acceptance Criteria

The initial framework is complete when:

- The skill has valid frontmatter and passes the bundled structural validator.
- Its description clearly routes humanoid CAD and simulation requests without attracting generic CAD questions unnecessarily.
- The entrypoint routes to relevant references using progressive disclosure.
- Fusion, SOLIDWORKS, and Ansys guidance is separated from shared engineering logic.
- Approval, recovery, capability-discovery, and evidence rules are explicit.
- Intake and load-case guidance handles both complete humanoids and subsystems.
- The user can choose modelling and analysis fidelity independently for each component or study.
- Component criticality produces a transparent fidelity recommendation without forcing unnecessary analysis on local structures.
- The confirmed fidelity carries through design decisions, CAD evidence, simulation effort, verification, and reporting as an execution assurance profile.
- Safety factors and margins are explicit, failure-mode-specific, consequence- and uncertainty-informed, and user-approved.
- Results cannot be presented as validating a critical component when the approved fidelity is insufficient for that conclusion.
- Missing inputs cannot silently become simulation assumptions.
- Test scenarios demonstrate safe behavior for unsupported tools, failed verification, and insufficient evidence.
