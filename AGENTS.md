# Instructions for coding agents

You are working in `G-U-R-U/.github`, part of G.U.R.U. It holds the organisation profile (`profile/README.md`) and the reusable workflows in `.github/workflows/` that other G.U.R.U repositories call (`ci.yml` and `triage.yml` there are this repository's own callers). [README.md](README.md) explains how to call them.

This repository is **public**. Anyone can read every file, commit, issue and pull request in it.

## Hard rules

1. **Nothing private goes here.** Never put secrets, tokens, internal details, content from private repositories, customer data or business plans in a file, commit message, issue or pull request.
2. **Never open licensed data.** Don't read source documents or the source store.
3. **Release every reusable-workflow change.** Callers pin each workflow by commit SHA with the release tag as a comment, and Dependabot proposes the bumps. So a change to a reusable workflow, including Dependabot's action bumps in it, ships as a new release tag `vMAJOR.MINOR.PATCH`. A breaking change, such as removing or renaming a workflow or an input, is a new major version. In the pull request, say which version the change needs and update README (the version in the example pins and, for a breaking change, the Releases notes). Don't create or push tags: the owner tags the release after merging.
4. **Fix shared workflows here.** When a reusable workflow is wrong, fix it in this repository and release it. Don't work around it in a caller.
5. **Leave no legacy behind.** When a change replaces a workflow, input, setting or doc, remove what it replaced in the same pull request. Keep something old only for a stated reason, and write that reason down where it's kept.
6. **The owner alone merges.** Never merge a pull request and never enable auto-merge.

## Pull requests

Keep each pull request to one task. Before opening it, review the branch as its author and fix what you find. Then request a Copilot review, and reply to every review thread before the owner merges. Leave out every "made by AI" label in commits and pull requests.

## Before you say a task is done

CI (`.github/workflows/ci.yml`) must pass on the pull request. It runs one check: `gitleaks`, which scans the full Git history for secrets.
