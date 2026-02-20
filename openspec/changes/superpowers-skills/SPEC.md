# Superpowers & Skills Specification

**Status**: Drafting (Change Proposal)  
**Created**: 2026-02-20  
**Owner**: A Session (draft prepared by codex)  
**Related Issue**: Skills module and superpowers capability planning

## Overview
Define the skills subsystem and the overarching superpowers capability that orchestrates skill discovery, isolation, invocation, and user entry points. This spec focuses on design clarity, risk reduction, and a phased path to implementation before any code is written.

## Goals
- Provide a clear functional and technical scope for the skills subsystem.
- Establish design targets for the superpowers orchestrator (global capability entry point).
- Decide on registration, isolation, invocation, and permission flows that are safe by default.
- Outline milestones with measurable deliverables to track progress and risks.

## Scope
- Skills registry and metadata model (manifest, versioning, capabilities, permissions).
- Isolation and execution strategy for skills (sandboxing, resource limits, observability).
- Invocation pipeline (context building, permission checks, execution, result shaping).
- Superpowers entry points (UI triggers, APIs, and orchestration glue).
- Progress tracking via milestones and risk register.

Out of scope for this change:
- Final UI polish or theming.
- Production deployment automation.
- Vendor-specific integrations beyond the evaluation shortlist.

## Functional Requirements
1. **Skill Registration and Discovery**
   - Accept a manifest describing name, version, description, required permissions, input/output schema, resource hints, and execution mode (local/remote).
   - Support registry queries by capability tags and version constraints.
   - Validate manifest against schema and reject insecure or incomplete entries.

2. **Skill Isolation and Execution**
   - Run each skill in a sandboxed runtime with CPU/memory limits and execution timeout.
   - Support both in-process sandbox (VM/WASM) and remote worker modes; choose default with pluggable strategy.
   - Enforce deny-by-default permission model; explicit allow-list per skill.
   - Capture structured logs and metrics per invocation.

3. **Invocation and Context Pipeline**
   - Build execution context from user input, session metadata, and allowed resources.
   - Preflight checks: permission evaluation, schema validation, quota/rate limits.
   - Post-execution: normalize results, redact sensitive fields, return typed payload with status.
   - Provide cancellation and retry semantics (idempotent retries where safe).

4. **Superpowers Orchestration**
   - Single orchestration layer that routes user intents to skills based on capability matching and policy.
   - Support UI trigger points: command palette/launcher, inline action buttons, and programmable shortcuts.
   - Offer API surface for programmatic invocation with audit logging.

5. **Permissions, Safety, and Auditing**
   - Permission scopes: data access (files, APIs), network, system resources.
   - Context isolation per invocation; no cross-skill state leakage without explicit channels.
   - Audit log for registration events, approvals, and invocations (success/failure).

## Non-Functional Requirements
- **Security**: Sandbox isolation, deny-by-default permissions, input/output validation, rate limiting.
- **Reliability**: Timeouts, bounded retries, circuit breaking for failing skills, health checks for remote runtimes.
- **Performance**: P95 cold start and execution budgets defined per skill class; target <1.5s cold start and <500ms warm execution for light skills.
- **Observability**: Structured logs, metrics per skill, correlation IDs across orchestrator and runtimes.
- **Extensibility**: Pluggable runtime adapters (VM2/WASM/remote worker) and registry backends.
- **DX**: Developer template for skill manifest and starter, CLI hooks for validation.

## Architecture and Components
- **Skills Registry**: Stores manifests, version history, capability tags, approval status.
- **Permission and Policy Engine**: Evaluates scopes and rate limits before invocation.
- **Context Builder**: Assembles sanitized execution context (user/session/input/resources).
- **Execution Runtime**: Default sandbox (WASM or VM-based) plus optional remote worker; enforces resource budgets.
- **Invocation Orchestrator (Superpowers Core)**: Routes intents to skills, manages lifecycle, collects telemetry.
- **UI Entrypoints**: Command palette/launcher, contextual actions, programmable shortcuts; backed by orchestrator APIs.
- **Observability Stack**: Log/metrics emitters with per-skill correlation IDs and dashboards.

### Key Flows
1. **Registration Flow**
   - Validate manifest → security review (static checks) → store in registry → mark approved/pending.
2. **Invocation Flow**
   - Select skill by capability tag/version → build context → preflight (schema, permission, quota) → execute in sandbox → normalize/return result → emit audit log and metrics.
3. **Error/Retry Flow**
   - Classify errors (user input, skill logic, platform) → retry idempotent paths; surface actionable error messages.

## Data Contracts
- **Skill Manifest (draft v1)**
  - `name`, `version`, `description`, `capability_tags[]`, `owner`, `runtime` (vm/wasm/remote), `entrypoint`, `permissions[]` (scopes), `inputs` (JSON schema), `outputs` (JSON schema), `resource_limits` (cpu/ms, memory/MB), `network` (allowed hosts), `observability` (log level), `notes`.
- **Invocation Request**
  - `skill_id/version`, `intent`, `input_payload`, `context` (session/user), `options` (timeout, retries), `trace_id`.
- **Invocation Response**
  - `status` (success/fail/cancelled), `output`, `errors[]`, `metrics` (duration, resource), `logs_ref`, `trace_id`.

## Milestones
- **M0 - Planning**: Approve spec, confirm scope, select default sandbox strategy and manifest schema v1.
- **M1 - Foundations**: Implement registry data model, manifest validator CLI, and permission scope taxonomy.
- **M2 - Runtime**: Implement sandbox adapter (chosen default) with resource limits, logging, and metrics; add invocation orchestrator skeleton.
- **M3 - UI & Integrations**: Wire command palette/launcher and API entrypoints; ship sample skills and end-to-end demo.
- **M4 - Hardening**: Add rate limiting, retries/circuit breaking, audit dashboards, and security review sign-off.

## Risks and Mitigations
- **Sandbox escape or unsafe permissions**: Choose vetted sandbox (WASM preferred), enforce allow-list, add pre-publish static checks.
- **Performance regressions**: Define per-skill budgets; add metrics and profiling in M2; cache warm runtime instances.
- **Registry inconsistency**: Apply schema validation and version pinning; add migration path for manifest changes.
- **UX discoverability**: Provide default entrypoints (palette and inline actions) and sample skills in M3.
- **Operational visibility gaps**: Ship default dashboards and alerts for failures and timeouts in M4.

## Open Questions
- Which runtime is preferred as default (WASM vs VM-based sandbox)?
- Do skills need outbound network by default, or should it be opt-in per host?
- What minimum audit fields are required for compliance?
- Which UI entrypoint should launch first (palette vs inline action)?
