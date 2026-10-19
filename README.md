# Build with agents

A portable [Agent Skill](https://agentskills.io/specification) for coding with a small, bounded team—not a swarm of agents.

An orchestrator scopes the task, creates a dedicated branch, delegates cohesive work, integrates the results, and requests independent review. After review passes, it updates the relevant log and commits locally. It never automatically merges or pushes.

## Install

Install from this GitHub repository with the [skills CLI](https://skills.sh/):

```sh
npx skills add bagiicpp/build-with-agents --skill build-with-agents
```

Follow the CLI prompts to select your agent and installation scope. The CLI requires Node.js/npm; the skill itself has no runtime dependencies.

Alternatively, copy this repository into your agent's supported skills location as a directory named `build-with-agents`.

## Use

Ask your agent explicitly:

```text
Use build-with-agents to implement CSV export with tests.
```

If your host exposes skills as slash commands, an invocation may look like:

```text
/build-with-agents implement CSV export with tests
```

Exact command syntax and whether a reload or new session is required depend on the host.

For explicit limits:

```text
Use build-with-agents to add password reset with tests.
Allow at most 2 concurrent workers, 5 worker calls, 2 reviewer calls,
and 1 fix/re-review cycle. Do not push or merge.
```

The normal workflow includes a local commit. Add “do not commit” if you want reviewed changes left uncommitted.

## Workflow

1. **Inspect and scope:** read repository instructions, define acceptance criteria, and identify genuinely independent work.
2. **Announce a budget:** choose concurrency, estimate total calls, and set hard ceilings with a small fix allowance.
3. **Create a task branch:** preserve unrelated changes and make no task edits on the starting branch.
4. **Delegate substantial work:** give workers clear deliverables, stable interfaces, separate write ownership, and validation requirements.
5. **Integrate and validate:** inspect actual diffs, reconcile the combined design, and run relevant checks.
6. **Review independently:** a fresh read-only reviewer returns PASS, FAIL, or BLOCKED based on the integrated changes and evidence.
7. **Log and commit:** after PASS, update the relevant log and commit only intended changes on the task branch.

## Agent budgets

| Control | Default |
| --- | --- |
| Concurrent worker ceiling | 2 |
| Actual concurrency | 1–2, depending on useful independent work |
| Total worker calls | Estimated and capped by the orchestrator after scoping |
| Reviewer calls | At most 2: initial review and one re-review |
| Fix/re-review cycles | At most 1 |

Higher concurrency requires user authorization. Workers and reviewers cannot spawn more agents. Follow-ups, retries, replacements, and resumed assignments all count toward the ceilings; reading results and polling status do not.

The orchestrator asks before exceeding a ceiling. It does not launch agents just to fill slots or keep requesting reviews until one passes.

## Requirements and limitations

- An agent host with worker/reviewer delegation, access to repository files, Git, and relevant validation tools.
- A Git repository for the development task; otherwise the orchestrator asks how to proceed.
- Permission to perform the requested work, run checks, and create a task branch.

This is a portable instruction-based skill, not a delegation engine. It does not add subagent capabilities to hosts that lack them. Without delegation, it asks before falling back to solo work rather than pretending an independent review occurred.

Call ceilings are workflow rules, not runtime-enforced token or monetary quotas. Two calls can still consume substantial usage, and adherence depends on the host and model.

## Repository layout

```text
.
├── README.md
├── SKILL.md                  # Instructions loaded by the agent
├── references/
│   └── handoffs.md           # Planning, assignment, review, and log templates
└── tests/
    └── scenarios.md          # Manual behavioral acceptance tests
```

See [`SKILL.md`](SKILL.md) for the complete rules and [`references/handoffs.md`](references/handoffs.md) for the handoff templates.

## Validation and testing

The skill is ready for initial use and evaluation. Metadata and file-layout checks are not proof of agent behavior, and this repository does not claim cross-host behavioral certification.

Run the 15 manual cases in [`tests/scenarios.md`](tests/scenarios.md) in a disposable repository. Inspect actual tool traces for call counts, concurrency, branch safety, ownership, validation, review, and staged changes—not just the final summary.

Start with a small real task and two workers before increasing limits. The goal is meaningful parallel work and coherent code, not maximum agent activity.
