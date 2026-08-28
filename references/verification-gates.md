# Verification gates

Scale evidence with the approved assurance profile.

| Fidelity | Minimum evidence |
|---|---|
| Concept | Units, envelopes, interfaces, gross clearance, approximate mass, first-order load path, explicit limitations |
| Preliminary | Parametric state, dominant cases, representative joints/materials, mesh quality, initial sensitivity, requirement comparison |
| Detailed | Manufacturing geometry, verified interfaces, realistic connections/contacts, convergence study, applicable nonlinear/buckling/fatigue checks |
| Release-candidate | Detailed evidence plus configuration control, broader cases, documented sensitivity/uncertainty, independent artifacts, human engineering review |

CAD checks include document/configuration, body/component/feature counts, axis alignment, range of motion, interference/clearance, envelope, volume, mass, centre of mass, inertia, thickness, merge/cut scope, and neutral export/reopen where practical.

Simulation checks include material completeness, load/support/contact review, mesh formulation and quality, convergence history, reaction balance, energy/force consistency, singularity interpretation, relevant results, sensitivity, and comparison with approved margins.

Higher fidelity increases evidence depth; it does not automatically modify an approved safety factor. Tool success is not sufficient evidence. Use `inconclusive` when evidence is insufficient.
