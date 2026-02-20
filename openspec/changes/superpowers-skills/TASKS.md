# Tasks: Superpowers & Skills

## M0 - Planning
- [ ] Confirm scope with stakeholders and capture decisions in SPEC.md.
- [ ] Finalize default sandbox strategy (WASM vs VM) and document rationale.
- [ ] Define manifest schema v1 and sample manifest.

## M1 - Foundations
- [ ] Implement registry data model and storage schema.
- [ ] Build manifest validation CLI/command and schema tests.
- [ ] Define permission scope taxonomy and approval workflow states.

## M2 - Runtime
- [ ] Build sandbox adapter for chosen default runtime with CPU/memory/time budgets.
- [ ] Implement invocation orchestrator skeleton with context builder and preflight checks.
- [ ] Emit structured logs and metrics per invocation with trace IDs.

## M3 - UI & Integrations
- [ ] Implement command palette/launcher entrypoint wired to orchestrator API.
- [ ] Add inline/contextual action trigger and API invocation endpoint.
- [ ] Create at least two sample skills exercising registry, sandbox, and permission checks.

## M4 - Hardening
- [ ] Add rate limiting, retries (idempotent paths), and circuit breaking for failing skills.
- [ ] Implement audit logging/dashboard for registrations and invocations.
- [ ] Run security review on sandbox configuration and permission defaults; address findings.
