# Engineering data model

Maintain a human-readable neutral record. Native CAD/CAE IDs belong only under adapter mappings.

```yaml
project: {id: string, revision: string, units: string, global_frame: string}
scope: {robot_class: string, subsystem: string, decision: string}
assurance:
  consequence: critical|important|local|undetermined
  fidelity_selected: concept|preliminary|detailed|release-candidate
  fidelity_recommended: concept|preliminary|detailed|release-candidate
  mismatch_acknowledged: boolean
  rationale: string
requirements: []
links: []
interfaces: []
joints: []
actuators: []
materials: []
mass_properties: []
load_cases: []
analyses: []
margins_by_failure_mode: []
assumptions: []
approvals: []
evidence: []
adapter_mappings: {fusion: {}, solidworks: {}, ansys: {}}
```

Each engineering value records value, units, state, source/provenance, uncertainty, and approval when applicable. Each requirement has a measurable criterion and links to load cases and evidence. Do not translate a face, edge, or body identifier from one application into another without a verified mapping artifact.
