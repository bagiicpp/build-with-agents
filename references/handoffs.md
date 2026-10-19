# Plans and handoffs

Adapt these templates to the host's tools. They are communication formats, not executable tool calls. Keep reports factual and concise.

## Delegation plan

```text
Task:
Starting branch/revision:
Task branch:
Workspace strategy: shared checkout | isolated worktrees
Acceptance criteria:
Shared interfaces / architectural decisions:

Concurrent worker ceiling:
Chosen concurrency:
Expected worker calls:
Hard worker-call ceiling (including fixes):
Reviewer-call ceiling:
Fix/re-review cycle limit:
Combined call ceiling:

Assignments and write ownership:
Dependencies / integration order:
Reserved fix allowance:
Validation:
Relevant log path:
Why this is the smallest useful team:
```

For example, a task with two independent implementation deliverables could use two workers concurrently, expect two worker calls, reserve two more for fixes, and allow two reviewer calls. That is a forecast of three calls including initial review, a hard ceiling of six, and one fix/re-review cycle—not a requirement to launch six agents. A different task should receive a different scope-based budget.

## Call ledger

| Call | Role | Assignment | Workspace / write ownership | State |
| --- | --- | --- | --- | --- |
| W1 | Worker | First cohesive deliverable | Assigned paths | Reserved / active / completed / cancelled / uncertain |
| W2 | Worker | Second cohesive deliverable | Separate assigned paths | Reserved / active / completed / cancelled / uncertain |
| R1 | Reviewer | Integrated result | Read-only task workspace | Reserved / active / completed / cancelled / uncertain |

Track worker calls used/ceiling, reviewer calls used/ceiling, active workers/chosen concurrency, and completed fix cycles/limit. Preserve the ledger across resumed sessions; use a session artifact or the relevant task log when persistence is needed. Do not log every status poll.

Every new assignment gets another call ID even if sent to a previously used agent. Count failed deliveries when uncertain whether an agent started. Do not free a concurrency slot while its worker may still be editing.

## Worker assignment

```text
Role: implementation worker. Do not delegate or spawn agents.

Deliverable:
Acceptance criteria:
Relevant code and conventions:
Agreed interfaces:
Dependencies already satisfied:
Workspace and expected branch:
Files/areas you may edit:
Files/areas you must not edit:
Validation commands/checks:

Stay within this assignment. Do not commit, merge, push, switch a shared
branch, or modify another worker's files. Report required out-of-scope
changes to the orchestrator. Preserve unrelated changes.

Return:
- Status: COMPLETE | INCOMPLETE | BLOCKED
- Changes and files touched
- Important decisions / interface assumptions
- Checks actually executed, with outcomes
- Checks not run and why
- Remaining risks, blockers, and integration notes
```

## Reviewer assignment

```text
Role: independent, read-only reviewer. Do not delegate, edit, or commit.

Task and acceptance criteria:
Repository instructions:
Workspace and task branch:
Task baseline and complete diff scope, including intended new files:
Known unrelated changes to exclude:
Agreed interfaces / architecture:
Worker reports (context, not proof):
Validation commands and recorded results:
Explicitly approved validation exceptions, if any:

Inspect the actual integrated changes and relevant surrounding code.
Check correctness, regressions, security where relevant, error handling,
tests, and consistency of the combined design. Confirm evidence rather
than trusting worker claims. Do not manufacture style-only blockers.

Return:
- Verdict: PASS | FAIL | BLOCKED
- Reviewed revision / working-tree state
- Acceptance criteria: satisfied or unresolved, with evidence
- Blocking findings: severity, location, impact, required fix
- Optional suggestions, separately labeled
- Checks run or inspected, with outcomes and limitations

Return BLOCKED when missing access or evidence prevents a conclusion.
PASS requires no blocking findings and sufficient validation evidence.
```

If a reviewer cannot see uncommitted or new files in the implementation workspace, provide the complete changes in an accessible workspace or artifact before asking for a verdict. Never assume a reviewer launched from the base revision automatically sees the task's working tree.

## Budget-extension request

```text
Status: stopped at a delegation/review limit.
Completed:
Remaining issue and impact:
Current usage: workers X/Y; reviewers A/B; fix cycles C/D
Requested increase: [specific additional calls / concurrency / cycles]
Why those calls are needed:
Current branch and uncommitted state:
```

## Task-log entry

```markdown
## <Task title>
- Branch: <task branch>
- Outcome: <implemented behavior>
- Validation: <commands and outcomes; limitations or approved exceptions>
- Independent review: <verdict and reviewed state>
- Agent calls: workers <used>/<ceiling>; reviewers <used>/<ceiling>
- Peak concurrent workers: <number>; fix cycles: <used>/<limit>
- Remaining limitations: <none or specifics>
```

Update an existing task entry where appropriate. Do not invent a commit hash before creating the commit; report the resulting hash in the final response rather than creating a second commit merely to record it.
