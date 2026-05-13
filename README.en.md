[한국어](./README.md) | [English](./README.en.md) | [日本語](./README.ja.md)

# OctoSmith

OctoSmith is a Codex-native development-operations boilerplate that uses Codex, Git, and GitHub to turn ideas into PRDs, issues, mother branches, optional sub PRs, and review-ready PRs.

```text
OctoSmith
A Codex-native forge for GitHub issues, branches, and review-ready PRs.
```

## Prerequisites

- Node.js 20 or later is required.
- Node is not a project-language constraint. It is only the runtime for the Codex hooks and verification scripts in this boilerplate.
- This boilerplate does not include `package.json`.
- Target projects can use any language, including Python, Go, Rust, Java, Swift, Ruby, PHP, JavaScript, or TypeScript.
- Language-specific manifests such as `package.json`, `pyproject.toml`, `Cargo.toml`, and `go.mod` should be added only when the real project needs them.

The verification entry point is a shell script, not a package manager.

```sh
./scripts/verify
```

Individual verification commands are also available.

```sh
./scripts/verify docs
./scripts/verify hooks
./scripts/verify github
```

## What It Solves

Codex is strong at single tasks, but real development operations repeatedly run into these problems:

- Requirements are scattered across PRDs, issues, and PR bodies.
- Large tasks become single oversized PRs that are hard to review.
- After an interruption, it is unclear which documents to read and where to resume.
- Review comments, unresolved threads, checks, and Codex reaction signals are easy to miss manually.
- Without hooks or document rules, Codex follows a different process each time.

OctoSmith solves these problems with documents, skills, hooks, and GitHub surfaces instead of a server.

## Structure

- [`AGENTS.md`](./AGENTS.md): root router that tells Codex which documents to read before work
- [`ARCHITECTURE.md`](./ARCHITECTURE.md): map of the boilerplate's operating boundaries and structure
- [`docs/`](./docs/README.md): PRD, feature requirements, design, execution plans, reliability, security, and quality criteria
- [`.agents/skills/`](./.agents/skills/project-bootstrap/SKILL.md): reusable Codex operating workflows
- [`.codex/`](./.codex/config.toml): Codex runtime settings and hooks
- [`.github/`](./.github/pull_request_template.md): GitHub issue, PR, and Actions operating skeleton
- [`scripts/`](./scripts/verify): verification entry point for docs, hooks, and GitHub operating files

```mermaid
flowchart TD
  A[User request] --> B[AGENTS router]
  B --> C[docs knowledge base]
  B --> D[repo-local skills]
  B --> E[Codex hooks]
  C --> F[PRD and feature requirements]
  D --> G[GitHub issue and PR operations]
  E --> H[Dangerous-command blocking and missing-verification prevention]
  G --> I[review drain]
  I --> J[clean PR]
```

## Basic Operating Flow

```mermaid
flowchart TD
  A[Idea or request] --> B[prd-writer]
  B --> C[docs PRD and FEATURE_REQUIREMENTS]
  C --> D[issue-planner]
  D --> E[GitHub issue]
  E --> F[subpr-orchestrator]
  F --> G[mother branch]
  G --> H[sub PR plan]
  H --> I{Parallelizable}
  I -->|yes| J[worktree and sub-agent parallel work]
  I -->|no| K[sequential sub PR work]
  J --> L[Create PR]
  K --> L
  L --> M[pr-review-drain]
  M --> N{clean}
  N -->|no| O[fix, verify, push, wait for reaction]
  O --> M
  N -->|yes| P[ready to merge]
```

## Applying It To A New Project

1. Apply this boilerplate at the project root.
2. Add a project-language manifest only when needed.
3. Fill `README.md`, `ARCHITECTURE.md`, `docs/PRD.md`, and `docs/FEATURE_REQUIREMENTS.md` with project-specific content.
   - `README.en.md` and `README.ja.md` are distribution docs for the OctoSmith repository. If the target project does not need multilingual READMEs, delete them or remove the language links from `README.md`.
4. Run `./scripts/verify` to check document routing, hook settings, and GitHub operating files.
5. Connect the GitHub remote and default branch.
6. Enter requirements and start the operating flow from `prd-writer`.

```mermaid
flowchart TD
  A[Empty or existing project] --> B[Apply OctoSmith structure]
  B --> C[Write project README and PRD]
  C --> D[Decide whether a language manifest is needed]
  D -->|needed| E[Add pyproject Cargo go.mod package etc.]
  D -->|not needed| F[Keep without a manifest]
  E --> G[./scripts/verify]
  F --> G
  G --> H[Connect GitHub remote]
  H --> I[Start with prd-writer]
```

## Provided Skills

| Skill | Purpose |
| --- | --- |
| `project-bootstrap` | Applies docs, hooks, GitHub templates, and verification structure to a new project |
| `prd-writer` | Turns ideas into PRDs and feature requirements |
| `issue-planner` | Writes and creates GitHub issue drafts from PRD and development schedule criteria |
| `subpr-orchestrator` | Runs one issue on a mother branch and splits into sub PRs only when the diff is too large to review comfortably |
| `pr-review-drain` | Processes PR review comments and threads until clean |

If repo-local skills are not exposed automatically in a Codex session, ask Codex to read the relevant `SKILL.md` path directly.

Example:

```text
Read .agents/skills/pr-review-drain/SKILL.md and apply it to the current PR.
```

## Skill Flows

### project-bootstrap

This skill installs the operating structure for a new project and leaves it in a verifiable state.

```mermaid
flowchart TD
  A[Check current repository] --> B[Preserve existing docs and settings]
  B --> C[Create AGENTS README ARCHITECTURE]
  C --> D[Create docs structure]
  D --> E[Create Codex config and hooks]
  E --> F[Create GitHub template and workflow]
  F --> G[Install base skills]
  G --> H[Update context-map]
  H --> I[./scripts/verify]
  I --> J[Bootstrap summary]
```

### prd-writer

This skill decomposes an idea into product-judgment criteria and implementable requirements.

```mermaid
flowchart TD
  A[Idea input] --> B[Problem definition]
  B --> C[Target users and scenarios]
  C --> D[MVP scope and non-scope]
  D --> E[Success criteria]
  E --> F[Write docs PRD]
  F --> G[Decompose feature requirements]
  G --> H[Write Acceptance Criteria]
  H --> I[Separate open questions]
  I --> J[./scripts/verify docs]
```

### issue-planner

This skill turns the PRD and feature requirements into a GitHub issue.

```mermaid
flowchart TD
  A[Read PRD and FEATURE_REQUIREMENTS] --> B[Identify next work candidate]
  B --> C[Write issue purpose and background]
  C --> D[Write implementation scope and excluded scope]
  D --> E[Write Acceptance Criteria and DnD]
  E --> F[Write test and document requirements]
  F --> G[Expected sub PR split]
  G --> H[Parallelization judgment]
  H --> I[Show issue draft]
  I --> J{User approval}
  J -->|yes| K[gh issue create]
  J -->|no| L[Revise draft]
  L --> I
```

### subpr-orchestrator

This skill runs one issue on a mother branch and splits it into multiple sub PRs only when the diff is too large to review comfortably.

```mermaid
flowchart TD
  A[Read issue body and related docs] --> B[Create mother branch]
  B --> C{Sub PR needed}
  C -->|no| D[Implement directly on mother branch]
  D --> E[Create PR]
  C -->|yes| F[Plan sub PRs]
  F --> G[Define DnD for each sub PR]
  G --> H{Parallelizable}
  H -->|yes| I[Create multiple worktrees]
  I --> J[Delegate to Codex sub-agents]
  H -->|no| K[Proceed sequentially from first sub PR]
  J --> L[Implement verify commit push]
  K --> L
  L --> M[Create PR]
  E --> N[Run pr-review-drain]
  M --> N
  N --> O{User merge required}
  O -->|yes| P[Wait for user merge]
  P --> Q[Update mother branch]
  Q --> F
  O -->|no| R[Prepare issue completion]
```

### pr-review-drain

This skill drains PR review feedback and Codex reactions until the PR is merge-ready.

```mermaid
flowchart TD
  A[Find PR for current branch] --> B[Collect review comments]
  B --> C[Collect reviews and threads]
  C --> D[Collect current-head reactions and checks]
  D --> E{New review input}
  E -->|yes| I[Normalize findings]
  E -->|no| F{eyes reaction}
  F --> B
  F -->|yes| P[30-second polling, max 30 minutes]
  F -->|no| G{Fresh clean signal}
  G -->|yes| H[Check status and threads, then summarize merge-ready state]
  G -->|no| I[Normalize findings]
  I --> J[Define DnD for each finding]
  J --> K[Fix code docs tests]
  K --> L[Run verification]
  L --> M{Verification passed}
  M -->|no| K
  M -->|yes| N[commit push]
  N --> O[Resolve threads]
  O --> P
  P --> B
```

## Recommended Prompts

### Bootstrap A New Project

```text
$project-bootstrap
Apply the Codex-native operating boilerplate to this repository.
Create AGENTS.md, docs, .codex hooks, GitHub templates, verification scripts, and base skills, then make ./scripts/verify pass.
Infer the project name, default branch, and verification commands conservatively from the current repository state.
Write all summaries in Korean.
```

### Write A PRD

```text
$prd-writer
Write docs/PRD.md and docs/FEATURE_REQUIREMENTS.md based on the idea below.
Separate problem definition, target users, MVP scope, non-scope, success criteria, acceptance criteria, test requirements, and open questions.
Do not implement ambiguous items. Leave them as open questions.

Idea:
...
```

### Create An Issue

```text
$issue-planner
Based on the PRD and development plan documents, create a detailed GitHub issue for the next work item.
Include:
- Purpose
- Background
- Implementation scope
- Excluded scope
- Acceptance Criteria
- Definition of Done
- Test requirements
- Documentation update requirements
- Expected sub PR split
- Parallelization judgment

Show the draft before creating the issue, then create it with gh after approval.
```

### Run An Issue On A Mother Branch And Split Only If Needed

```text
/goal
Track GitHub issue #12 as the completion goal.

First read AGENTS.md and the docs router, then check the issue body and related PRD/FEATURE_REQUIREMENTS/PLANS documents.
In Plan mode, do not implement. Present a decision-complete proposed_plan.

After the plan is approved, update the current base branch, create the mother branch, and first decide whether a single PR is enough.
If a single PR is enough, work directly on the mother branch without sub PRs. If the diff is too large to review comfortably, split the issue into sub PR units.
When splitting into sub PRs, document the goal, excluded scope, DnD, and verification command for each sub PR.

For parallelizable sub PRs, split the work with worktrees and Codex sub-agents. If there are sequential dependencies, wait until the user confirms that the preceding PR was merged, then update the mother branch and create the next branch.
Do not merge directly from Codex. Hand required merges back to the user.

For each PR, proceed through commit, push, and PR creation. At the end, repeat $pr-review-drain until the Codex review is clean.
Write all responses and work summaries in Korean.
```

### PR Review Drain

```text
$pr-review-drain
Run review drain on the PR for the current branch.
Collect PR body reactions, review comments, threads, and checks against the current head. If there is no new review input and only an eyes reaction is present, poll every 30 seconds for up to 30 minutes.
When review feedback appears, handle it even if eyes is still present, then repeat fix/verify/commit/push/resolve.
When a current-head +1 reaction or no-major-issues Codex review/comment appears and checks/threads are clean, summarize the merge-ready state.
Include the PR URL, base/head, handled findings, verification commands, last reaction, clean signal freshness evidence, polling wait time, skipped/neutral checks, resolve failures, and remaining risks in the final summary.
```

## Examples

### From Product Idea To First Issue

```mermaid
flowchart TD
  A[User enters idea] --> B[Run prd-writer]
  B --> C[Write PRD and feature requirements]
  C --> D[Run issue-planner]
  D --> E[Review issue draft]
  E --> F{Approved}
  F -->|yes| G[Create GitHub issue]
  F -->|no| H[Revise scope and AC]
  H --> E
```

### Complete One Issue With Multiple PRs

```mermaid
flowchart TD
  A[Select GitHub issue] --> B[Run subpr-orchestrator]
  B --> C[Create mother branch]
  C --> D[sub PR plan]
  D --> E[foundation PR]
  D --> F[runtime PR]
  D --> G[verification PR]
  E --> H[review drain each PR]
  F --> H
  G --> H
  H --> I[clean PRs]
  I --> J[Integrate mother branch]
```

### Handle Review Comments

```mermaid
flowchart TD
  A[Review comments arrive on PR] --> B[Run pr-review-drain]
  B --> C[Write DnD for each finding]
  C --> D[Fix]
  D --> E[Verify]
  E --> F[commit push]
  F --> G[Resolve thread]
  G --> H[reaction polling]
  H --> I{clean signal}
  I -->|no| B
  I -->|yes| J[Merge-ready state]
```

## GitHub Operating Rules

- Issues should follow the sections in `.github/ISSUE_TEMPLATE/feature.yml`.
- PR bodies should fill the DnD, verification, document changes, and risk sections in `.github/pull_request_template.md`.
- The default CI runs `./scripts/verify` in `.github/workflows/verify.yml`.
- Before requesting review, record the local `./scripts/verify` result in the PR body.
- Clean review means no unresolved threads, no failed or pending checks, a current-head Codex `+1` reaction or no-major-issues review/comment clean signal, and a clean working tree.
- If you change GitHub templates or workflows, run `./scripts/verify github`.

## Hook Policy

Codex hooks do not replace the workflow. Hooks are guardrails, while skills own the workflow.

Hooks are responsible for:

- Blocking dangerous commands
- Preventing mutating Bash commands on `main`
- Warning or blocking access to secret files
- Requiring verification after document, hook, or lockfile changes
- Reminding the session about the document router on session start and prompt submit

Blocked examples:

```text
git reset --hard
git checkout --
git clean -fd
git push --force
rm -rf ...
cat .env
```

## Boundary Between Boilerplate And Server

This structure is intended for personal or small-team development-operations automation.

Good fits without a server:

- You want to repeat PRD writing and issue decomposition.
- You want to split large work into reviewable sub PRs.
- You want Codex to have stable pre-work documents and completion criteria.
- You want to process PR review comments systematically until clean.

Cases that need a separate orchestration server:

- GitHub webhooks must start jobs unattended.
- Multiple workers must hold leases and process a 24-hour queue.
- Failed Codex threads must be recovered automatically into new threads.
- You need operational requirements such as audit logs, retry policy, or SLA.
- The organization must share the same automation service.
