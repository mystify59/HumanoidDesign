---
name: humanoid-cad-simulation
description: Plan, execute, and verify humanoid robot mechanical-design workflows through available Fusion, SOLIDWORKS, and Ansys MCP tools. Use for humanoid frames, links, joints, actuator supports, CAD construction or inspection, FEA preparation and review, boundary-condition intake, and consequence-scaled engineering assurance; not for generic CAD work unrelated to humanoid robotics.
---

# Humanoid CAD and Simulation

Coordinate humanoid mechanical design without confusing tool execution with engineering validation. Preserve the user's authority over scope, fidelity, assumptions, and mutations.

## Route the request

1. Read [project intake](references/project-intake.md) for every new project or materially changed scope.
2. Read [engineering data model](references/engineering-data-model.md) when recording or exchanging project state.
3. Discover connected MCP servers, live tool schemas, versions, active documents, and read/write capabilities. Never invent a tool name.
4. Read only the applicable adapter: [Fusion](references/fusion-adapter.md), [SOLIDWORKS](references/solidworks-adapter.md), or [Ansys](references/ansys-adapter.md).
5. Read [load cases](references/humanoid-load-cases.md) when deriving analysis inputs.
6. Before mutations or solves, read [workflow and approvals](references/workflow-and-approvals.md).
7. For model or result acceptance, read [verification gates](references/verification-gates.md).
8. Use [reporting](references/reporting.md) for preflight and final evidence.

## Invariants

- Prefer structured native model data; use screenshots only as supplementary evidence.
- Treat consequence, fidelity, assurance profile, and failure-mode margins as separate fields.
- Ask for missing loads, contacts, constraints, materials, and acceptance criteria. Do not silently assume them.
- Require approval before CAD/CAE mutation, overwrite, native scripting, solver execution, or use of a material engineering assumption.
- Establish a recoverable checkpoint before mutation and stop on document, unit, reference, topology, dialog, or convergence mismatch.
- A successful tool call proves execution only. Verify geometry, mesh, equilibrium, convergence, and requirements independently.
- Structural FEA does not prove balance, gait stability, controller safety, certification, or regulatory compliance.

## Unsupported capability

When a needed MCP operation is unavailable, state the exact gap and offer a neutral export, native script for review, or manual handoff. Do not claim the step ran.
