# progress-report

[한국어](README.ko.md) · [MIT License](LICENSE)

A skill for reporting the current request's goal and what is done, in progress, blocked, or still unplanned. It shows scope and goal in a summary table, followed by an evidence-backed task table with a concrete verification method for each step. Codex and Claude Code plugins read the same `skills/progress-report/` directory.

## Why this exists

A conversational status update can easily mix the latest request with older work, label an untested edit as complete, or hide a deferred item. `progress-report` makes the scope and state of each step visible in one table. This problem statement comes from the skill's current behavior, not from an unverified story about its original creation.

Use it when you ask “Where are we?”, “What remains?”, “Show progress by step”, or “What did we plan to do this session?”. It reports observed work; it does not manage a separate task database or automatically run checks.

## Install as a plugin

The plugin and marketplace are both named `progress-report`. This repository is self-contained and does not require `ai-workflow`.

### Codex

```bash
codex plugin marketplace add insung/progress-report
codex plugin add progress-report@progress-report
```

Start a new task if needed. Invoke `$progress-report` or ask for the active request's progress.

### Claude Code

```bash
claude plugin marketplace add insung/progress-report
claude plugin install progress-report@progress-report
```

Start a new session if needed. Invoke `/progress-report:progress-report`.

## How to use it

**Current request**

```text
$progress-report Where are we on the public skill repository work?
```

The default scope is the most recent major request and its follow-up changes. A summary table shows `범위` (scope) and `목표` (intended outcome). A task table then shows one row per step with `단계` (step), `할 일` (action), `검증 방법` (verification), and `상태` (status). If the work has phases, the task table follows those phases.

**Whole session**

```text
$progress-report Show progress for this entire session, including earlier major requests.
```

Explicitly saying “whole session” expands the scope to all major requests in the session, with a separate goal for each request. Naming a specific phase narrows the report to that phase and its goal.

The status vocabulary is fixed: `미착수` (not started), `계획 중` (planning), `진행 중` (in progress), `검토 중` (review), `막힘` (blocked), `완료` (done), and `이번 범위에서 제외` (out of scope). “Done” requires evidence that the named verification actually passed. A changed file or planned test alone is not completion. Review states name who must review; blocked states name the obstacle; deferred work remains visible.

A useful report shows the goal and remaining actions without a narrative:

| 범위 | 목표 |
| --- | --- |
| Public skill repository work | Complete a public repository with usage guides and validated installation |

| 단계 | 할 일 | 검증 방법 | 상태 |
| --- | --- | --- | --- |
| 1 | Write the usage guide | Read both language versions and follow local links | 진행 중 |
| 2 | Validate installation | Install from each host's marketplace in a clean profile | 미착수 |
| 3 | Review the result | User checks the public repository URL | 검토 중 · 사용자 |

**다음 작업:** Validate installation and incorporate the user's review.

### Worktree and verification examples

All projects, repository-relative paths, commands, and results below are fictional examples, not execution evidence. In actual reports, verify paths and branches with `git worktree list --porcelain` in each project.

For one related worktree, insert this table between the summary and task tables:

| 프로젝트 | 워크트리 경로 | 브랜치 | 용도 |
| --- | --- | --- | --- |
| auth-service | `.worktree/reset-api` | `feat/reset-api` | API implementation and self-checks |

For several related worktrees, add rows to the same structure. Omit worktrees belonging to other requests.

| 프로젝트 | 워크트리 경로 | 브랜치 | 용도 |
| --- | --- | --- | --- |
| auth-service | `.worktree/reset-api` | `feat/reset-api` | API implementation and self-checks |
| auth-web | `.worktree/reset-form` | `feat/reset-form` | UI implementation and self-checks |

When work is split across locations and row-to-location mapping is unclear, append `작업 위치` (work location) to the task table. Keep the default four columns when implementation and review purposes already make the mapping clear.

| 단계 | 할 일 | 검증 방법 | 상태 | 작업 위치 |
| --- | --- | --- | --- | --- |
| 1 | Reject expired API tokens | `pnpm lint`, `pnpm typecheck`, `pnpm test -- token.test.ts` | 진행 중 · lint passed; others not run | auth-service / `.worktree/reset-api` |
| 2 | Implement the email input form | `pnpm test -- form.test.ts`, user screen and wording review | 검토 중 · 사용자, automated tests: 6 passed | auth-web / `.worktree/reset-form` |

If no related separate worktree exists, omit the worktree table. A primary checkout alone does not count as a separate working worktree. A failed query is not evidence of absence; do not invent paths or branches. Put an uncertainty note in the worktree section:

**워크트리:** 미확인 · Repository access restriction prevented listing.

Use actual commands confirmed in the project, including relevant lint, typecheck, and tests. For work without commands or requiring human judgment, specify who reviews which screen, wording, links, or procedure. Reporting does not automatically run checks.

| Point in time | Verification | Status |
| --- | --- | --- |
| Implemented, checks not run | `pnpm lint`, `pnpm typecheck`, `pnpm test -- token.test.ts` | 진행 중 |
| Only lint passed | Same three commands | 진행 중 · lint passed; typecheck and tests not run |
| All specified checks passed | Same three commands | 완료 · lint and typecheck passed; tests: 5 passed |
| Automated tests passed; required user review pending | `pnpm test -- form.test.ts`, user screen and wording review | 검토 중 · 사용자 |
| Commands not confirmed | Confirm project check commands next | 계획 중 |

Output order is summary → optional worktrees → tasks → optional pending decisions → `다음 작업` (next action). The [report template](skills/progress-report/templates/progress-report.md) defines structure; SKILL.md remains canonical for scope, status, and verification rules.

## Boundaries

This skill reports only what the current conversation and accessible artifacts support. It does not infer that remote publication happened from a local commit, that a test passed because it was planned, or that the user approved a draft because time passed. If verification has not run, the step remains in progress or not started. If an earlier item was explicitly deferred, the report keeps it as out of scope rather than silently dropping it.

This is a reporting convention, not a tracker integration. A project can use spec-it independently; `progress-report` does not replace that project's policy or acceptance process.

## Package and updates

The canonical instructions are in [`skills/progress-report/SKILL.md`](skills/progress-report/SKILL.md). The current plugin version is `0.1.0`; maintainers should update host manifests together for a new release.

To refresh and reinstall in Codex:

```bash
codex plugin marketplace upgrade progress-report
codex plugin remove progress-report@progress-report
codex plugin add progress-report@progress-report
```

For Claude Code:

```bash
claude plugin marketplace update progress-report
claude plugin update progress-report@progress-report
```

To remove the plugin, run `codex plugin remove progress-report@progress-report` or `claude plugin uninstall progress-report@progress-report`.
