---
name: build-with-agents
description: Implement coding tasks with an orchestrator, a small bounded team of workers, and independent review before committing on a dedicated branch. Use when the user asks to build, implement, fix, or refactor with delegated agents, especially when they want controlled agent usage and coherent integration. Do not use for questions or review-only requests.
---

# Build with agents

Deliver coherent, tested changes through a small team. Prefer substantial assignments to fragmented parallel work. The orchestrator owns scope, architecture, integration, the usage ledger, and commits.

## Portability and capabilities

Use the host's available delegation and Git tools. This skill does not require a particular agent product, slash-command syntax, or executable integration. Its limits are workflow instructions, not runtime-enforced quotas.

Before launching agents, confirm that the host can delegate, receive results, and track completion. If delegation is unavailable, explain the limitation and ask whether to proceed without agents. Do not silently replace independent review with self-review or claim that agents ran when they did not.

Read `references/handoffs.md` relative to this skill directory before preparing assignments or review requests. It contains the plan, ledger, and reporting templates.

## Non-negotiable rules

- Make all task-related repository edits on a dedicated task branch, never directly on the starting branch.
- Only the orchestrator delegates. Workers and reviewers must not spawn agents.
- Workers and reviewers must not commit, merge, push, or switch branches in a shared checkout.
- Count all dispatched assignments, including retries, follow-ups, replacements, and resumed sessions. Reusing an agent does not make another assignment free.
- Respect concurrency and total-call ceilings. Ask before exceeding either; do not quietly redefine the task to reset the budget.
- Review the integrated result independently before committing. A worker's summary is not review evidence.
- Preserve unrelated user changes. Never stage everything blindly, discard work, or overwrite other contributors' changes.
- Never merge into the starting branch or push without explicit authorization.

## 1. Inspect and scope

Read repository instructions and relevant code before designing assignments. Check Git status, the current branch or detached HEAD, and any existing task log. Identify acceptance criteria, architectural constraints, relevant tests, shared interfaces, and likely file conflicts.

Use the requested starting revision; otherwise use the current checkout. Do not assume that `main` is the desired base, fetch, or change branches without considering local work.

If the directory is not a Git repository, stop and ask whether to initialize one or use another repository. Do not silently skip the dedicated-branch requirement.

Define cohesive deliverables. Delegate only work with a clear outcome that can be validated. Do not create agents merely to fill slots, assign one agent per file by default, or launch separate planning and reporting agents for work the orchestrator can do.

For tiny or tightly coupled tasks, explain why fewer workers—or no worker delegation—would be more efficient. Ask before replacing an explicitly requested delegated workflow with solo implementation.

## 2. Choose and announce the budget

Honor user-supplied limits first. Otherwise use these defaults:

| Control | Default |
| --- | --- |
| Concurrent worker ceiling | 2 |
| Chosen worker concurrency | 1–2, based on independent work; prefer 2 when useful |
| Worker-call estimate | Chosen after scoping; count assignments across every wave |
| Worker-call ceiling | Chosen after scoping, with an explicit, small fix allowance |
| Reviewer-call ceiling | 2: initial review plus one re-review |
| Fix/re-review cycles | 1 |

These defaults are not a requirement to use every slot or consume every call. Users may authorize higher concurrency; do not raise the default ceiling yourself. Never launch an unbounded wave because a task appears complex.

Before the first dispatch, state the branch plan, acceptance criteria, chosen concurrency, expected worker calls, hard worker-call ceiling, reviewer-call ceiling, fix-cycle limit, and rationale. Use integers for ceilings, not “as needed.” If no user ceiling was supplied, the announced scope-based ceilings become this task's limits. Ask for clarification when supplied limits conflict.

Distinguish forecasts from ceilings. Reserve enough capacity for useful fixes rather than spending the whole worker allowance on initial implementation. Report combined worker-plus-reviewer call totals as well as their separate counts. These counts are usage proxies, not token or monetary guarantees.

Maintain a ledger. Reserve a call immediately before dispatch and record its role, assignment, ownership, and state. Count a dispatch with uncertain delivery conservatively; do not retry it blindly. Reading results or polling status is not another assignment. Sending new instructions to an agent is. An idle or stalled agent still occupies a worker slot until completion or confirmed cancellation.

Run review after workers finish and integration is complete, not alongside active edits. If limits are exhausted, stop delegation and explain what is complete, what remains, and the exact additional calls requested. Do not evade the ceiling by doing a large unplanned rewrite yourself.

## 3. Create the task branch before editing

Record the starting branch/revision and working-tree state. Create and switch to a descriptive, unique branch such as `work/add-password-reset` before task-related edits, including log edits. Do not repurpose an unrelated existing branch. If resuming the same task, verify the existing task branch and ledger instead of creating a new one or resetting counts.

Do not stash, reset, clean, force-switch, or move unrelated changes without permission. If a dirty checkout makes ownership or branch creation unsafe, ask the user or propose an isolated worktree from an agreed base. Never assume a new worktree includes uncommitted work from the original checkout.

Choose the simplest safe workspace arrangement:

- **Shared checkout:** every worker stays on the task branch, with non-overlapping write ownership. Only the orchestrator runs Git index/branch operations.
- **Isolated worktrees:** workers use separate task-specific branches; the orchestrator integrates their changes into the dedicated task branch. Workers still do not commit. Inspect and transfer their changes using available tools; do not require worker commits for integration.

Avoid isolated worktrees when the host cannot safely collect uncommitted changes. Do not remove worktrees containing uncollected work.

## 4. Delegate cohesive work

The orchestrator defines shared interfaces and architectural direction before parallel implementation. If an interface is uncertain, settle it first rather than making workers guess independently.

Each assignment must include:

- A concrete deliverable and acceptance criteria.
- Relevant context, agreed interfaces, and dependencies.
- The workspace/branch, explicit write ownership, and excluded files.
- Validation commands or checks, and the required result format.
- Instructions not to delegate, commit, or expand scope.

Supply focused context, not a dump of the entire conversation. Prefer an end-to-end component or another substantial, separable outcome over many tiny assignments. Tests can run in parallel with implementation only when contracts are stable and file ownership is separate.

Workers may read shared files but must report needed changes outside their ownership instead of editing them. The orchestrator handles shared files, dependency manifests, lockfiles, and cross-cutting edits unless one worker has exclusive ownership. Reassign ownership only after the previous writer has stopped.

Require workers to report actual files changed, implementation decisions, checks executed with outcomes, and remaining concerns. An incomplete result must be labeled incomplete. If an agent stalls, inspect available status and cancel or await it before replacing it; account for any replacement call.

## 5. Integrate and validate

Inspect actual diffs rather than accepting summaries. Integrate deliberately, resolve interface mismatches, and check that the combined result has one consistent design. Reject unnecessary abstractions, duplicated logic, incompatible conventions, and unrelated cleanup.

The orchestrator may perform planned integration and small glue changes. Substantial new implementation requires an updated plan within the remaining budget, or approval for an increase.

Run the project's relevant tests, linting, type checks, or build as appropriate. Record commands and outcomes. Never claim an unrun check passed. If a required check cannot run, report the blocker and obtain an explicit exception before treating that criterion as satisfied.

## 6. Request independent review

After all writers have stopped, dispatch a fresh reviewer that did not implement the work. Provide the acceptance criteria, task baseline, full task diff (including intended new files), worker reports, and validation evidence. The reviewer is read-only and checks the integrated code directly.

Review correctness, regressions, error handling, security where relevant, test coverage, and architectural coherence. Distinguish actionable defects from optional style preferences. The reviewer must return one of:

- **PASS:** acceptance criteria met, required checks supported by evidence, no blocking findings.
- **FAIL:** concrete blocking findings with locations, impact, and required fixes.
- **BLOCKED:** insufficient access, evidence, or validation to reach a verdict.

Missing evidence is not a pass. A reviewer may inspect and run permitted checks, but must not edit the code or spawn agents. Checks with destructive or external effects still require the appropriate permission.

For FAIL or BLOCKED, make bounded fixes or resolve missing evidence, then request re-review within the call and cycle limits. Do not commit a failed or blocked result as approved. If the budget is insufficient, report the status and ask for the needed extension. Do not keep spawning reviewers until one agrees.

A pass applies only to the reviewed code state. Re-review any subsequent implementation change, within the remaining budget. A factual task-log update can follow review without another code review; it must not introduce implementation changes.

## 7. Update the log and commit

After PASS, update the repository's relevant task/changelog/development log before committing so the log and implementation can be recorded together. Follow existing conventions; do not scatter duplicate logs. If no log convention exists, use `WORK_LOG.md` at the repository root unless project instructions say otherwise.

Record the task, branch, result, validation and limitations, review verdict, and actual worker/reviewer calls versus ceilings. Keep private reasoning, secrets, and raw conversation transcripts out of logs.

This skill's normal workflow includes a local commit; an explicit request to complete this workflow authorizes that commit, subject to host permissions and repository rules. If the user prohibits committing or authorization is unclear, leave the reviewed changes uncommitted and explain why.

Inspect the final diff and status, stage only intended task changes and the log, and inspect the staged diff for accidental inclusions and whitespace errors. Where files also contain unrelated edits, stage only task hunks; ask if separation is uncertain. Commit on the dedicated task branch with a descriptive message.

If a commit hook changes implementation, the earlier PASS is stale: validate and re-review before finalizing, using the remaining budget or requesting an extension. Do not disable hooks, force a commit, or amend unrelated history to bypass failures.

Finish with a concise report: branch, commit hash (or why uncommitted), delivered changes, checks and outcomes, review status, log path, actual call counts, and remaining limitations. Leave the task branch available; do not automatically merge, push, or delete it.
