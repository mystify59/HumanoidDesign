# Workflow, approvals, and recovery

Read-only discovery may proceed. Before any mutation or solve, show a preflight containing target application/document/configuration, source revision, operations, affected entities, assumptions, assurance profile, failure-mode margins, load cases, expected cost, checkpoint/recovery method, verification evidence, and stop conditions.

Require explicit approval before geometry or configuration changes, material/contact/constraint changes, overwrite, solver launch or major cost increase, native scripting, destructive cleanup, or use of a material assumption.

Execute in bounded checkpoints. After each, confirm the active document, units, expected topology/state, and required evidence. Stop on ambiguous references, unexpected topology, modal dialogs, application errors, convergence violations, or capability loss.

Retry only unchanged, non-destructive transient failures with a finite limit. A changed model, analysis type, assumption, scope, cost, or boundary condition requires a revised preflight. Never overwrite the only known-good artifact.
