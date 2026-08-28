# Project intake

Ask progressively; do not dump every question at once. First establish robot class and scale, subsystem, clean-sheet versus existing model, intended activities, environment, payload, available artifacts, desired decision, and deliverables.

## Record every input state

Use exactly: `provided`, `derived`, `assumed_pending_approval`, `unknown_blocking`, or `not_applicable`.

## Classify failure consequence

- `critical`: collapse, uncontrolled motion, loss of balance or a primary load path, joint/actuator release, cascading damage, or hazard to people/surroundings.
- `important`: loss of a major function or substantial adjacent damage without expected immediate collapse.
- `local`: failure remains confined to replaceable nearby hardware with no credible escalation.
- `undetermined`: evidence is insufficient; block mutation or solving until confirmed or provisionally approved.

Propose and explain the class, then ask the user to confirm it. Check secondary hazards: loose hardware in joints, sharp fragments, cable damage, sensor loss affecting control, motion obstruction, and hidden lifting/safety load paths.

## Let the user select fidelity

- `concept`: envelopes, interfaces, approximate mass, clearances, and first-order load paths.
- `preliminary`: parametric geometry, representative joints/materials, dominant cases, and initial sensitivity.
- `detailed`: manufacturing-relevant geometry, realistic connections, convergence evidence, applicable nonlinearities, and requirements review.
- `release-candidate`: detailed work plus configuration control, broader cases, documented sensitivity, traceable evidence, and explicit human review. This is not certification.

Recommend a minimum using consequence, uncertainty, maturity, and the requested decision. Typical starting points are detailed for critical, preliminary for important, and concept/preliminary for local. The user controls the selection. Record and obtain acknowledgement for a selection below the recommendation; restrict the conclusions accordingly.

## Establish the execution assurance profile

Carry consequence and fidelity into geometry robustness, load-path redundancy, material and connection choices, tolerances, CAD evidence, load-case breadth, contact realism, mesh/convergence effort, sensitivity work, checkpoints, and review depth. Propose material changes; do not expand scope silently.

## Safety factors and margins

Ask the user to select or approve targets per failure mode: yield, ultimate, buckling, fatigue, slip/separation, bearing, fastener, impact, or others. Base recommendations on consequence, uncertainty, standards, duty, manufacturing, degradation, redundancy, and inspection. Critical or uncertain items normally need higher margins. Fidelity may reduce uncertainty but does not automatically change a factor. Missing targets remain `assumed_pending_approval` or `unknown_blocking`.

Then request only applicable units, frames, joint axes/limits, actuator torque and transmission data, supports, contacts, friction, materials, manufacture, tolerances, mass/inertia budgets, duty cycle, thermal state, loads, mesh expectations, and acceptance criteria.
