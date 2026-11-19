# G-U-R-U/.github

Shared GitHub settings for the G-U-R-U organisation.

- `profile/README.md` is the public organisation profile.
- `.github/workflows/` holds reusable workflows that other repositories call.

This repository is public, so any repository in the organisation can call its workflows. Never put
secrets or internal details in it.

## Reusable workflows

| Workflow | What it does | Inputs | Secrets |
| --- | --- | --- | --- |
| `add-to-project.yml` | Adds the pull request to a project board. Skips Dependabot PRs. | `project-url` (default `https://github.com/orgs/G-U-R-U/projects/1`) | `ADD_TO_PROJECT_PAT` (required): classic token with the `repo` and `project` scopes |
| `assign-owner.yml` | Assigns the owner to the issue or pull request. Skips runs Dependabot started. | `owner` (default `manouk-van-draanen`) | none |
| `gitleaks.yml` | Scans the full Git history of the calling repository for secrets. | none | none |

Notes:

- `assign-owner.yml` needs `issues: write` and `pull-requests: write`. The calling job must grant
  both, because a called workflow can't get more permissions than its caller.
- Pass `ADD_TO_PROJECT_PAT` by name, not with `secrets: inherit`, so the workflow gets only that secret.
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
    uses: G-U-R-U/.github/.github/workflows/assign-owner.yml@<sha> # v1.0.0

  add-to-project:
    if: github.event_name == 'pull_request_target'
    uses: G-U-R-U/.github/.github/workflows/add-to-project.yml@<sha> # v1.0.0
    secrets:
      ADD_TO_PROJECT_PAT: ${{ secrets.ADD_TO_PROJECT_PAT }}
```

```yaml
# In the repository's CI workflow
jobs:
  gitleaks:
    permissions:
      contents: read
    uses: G-U-R-U/.github/.github/workflows/gitleaks.yml@<sha> # v1.0.0
```

## Releases

Releases are tagged `vMAJOR.MINOR.PATCH`. A breaking change, such as removing or renaming an
input, bumps MAJOR.
