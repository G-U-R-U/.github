# G-U-R-U/.github

Shared GitHub settings for the G-U-R-U organisation.

- `profile/README.md` is the public organisation profile.
- `.github/workflows/` holds reusable workflows that other repositories call.

This repository is public, so any repository in the organisation can call its workflows. Never put
secrets or internal details in it.

## Reusable workflows

| Workflow | What it does | Inputs |
| --- | --- | --- |
| `assign-owner.yml` | Assigns the owner to the issue or pull request. Skips runs Dependabot started. | `owner` (default `manouk-van-draanen`) |
| `gitleaks.yml` | Scans the full Git history of the calling repository for secrets. | none |

Neither workflow needs a secret from the caller.

Notes:

- `assign-owner.yml` needs `issues: write` and `pull-requests: write`. The calling job must grant
  both, because a called workflow can't get more permissions than its caller.
- Dependabot can't assign its own PRs through a workflow. Set `assignees` in the calling
  repository's `dependabot.yml`.

## Calling a workflow

Pin each workflow by full commit SHA, with the release tag as a comment. Dependabot then updates
the pin when a new release comes out.

```yaml
# .github/workflows/triage.yml
name: Triage

on:
  issues:
    types: [opened, reopened]
  pull_request_target:
    types: [opened, reopened]

permissions: {}

jobs:
  assign-owner:
    permissions:
      issues: write
      pull-requests: write
    uses: G-U-R-U/.github/.github/workflows/assign-owner.yml@<sha> # v2.0.0
```

```yaml
# In the repository's CI workflow
jobs:
  gitleaks:
    permissions:
      contents: read
    uses: G-U-R-U/.github/.github/workflows/gitleaks.yml@<sha> # v2.0.0
```

## Releases

Releases are tagged `vMAJOR.MINOR.PATCH`. A breaking change, such as removing or renaming an
input, bumps MAJOR.

v2.0.0 removed `add-to-project.yml`: the organisation no longer adds pull requests to a project
board automatically. Callers on v1.x that still call it must drop that job before moving to v2.0.0.
