<!-- markdownlint-disable -->

# Hardening Report: cbrgm--pr-size-labeler-action/v1.3.13

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--pr-size-labeler-action/v1.3.13** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): GitHub Actions expressions are interpolated directly inside run: shell commands. In automerge.yml, `${{ github.event.pull_request.html_url }}` is embedded directly in five separate `run:` lines across three jobs (dependabot, renovate, cbrgm). An attacker who can control the PR URL (e.g. via a crafted pull request) could inject arbitrary shell commands. Offending lines: `run: gh pr review --approve ${{ github.event.pull_request.html_url }}` (lines 25, 39) and `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}` (lines 31, 43, 52). The value should be passed via an env: variable and the shell variable should be double-quoted.

Locations:

- `.github/workflows/automerge.yml:25`
- `.github/workflows/automerge.yml:31`
- `.github/workflows/automerge.yml:39`
- `.github/workflows/automerge.yml:43`
- `.github/workflows/automerge.yml:52`

### script-injection (severity: high)

Rule (a): In tag.yml, the `Publish Git Tag` step interpolates `${{ steps.bump-semver.outputs.new_version }}` directly into a shell variable assignment: `new_tag=${{ steps.bump-semver.outputs.new_version }}`. The `steps.*.outputs.*` context is a workflow-controllable value that flows through YAML template substitution before the shell sees it, allowing injection of arbitrary shell metacharacters. The value should be passed via an env: variable and double-quoted in the script.

Locations:

- `.github/workflows/tag.yml:58`

### unpinned-uses (severity: high)

The action.yml uses a Docker image with a mutable tag instead of an immutable SHA digest: `image: 'docker://ghcr.io/cbrgm/pr-size-labeler-action:v1'`. The tag `:v1` can be silently overwritten to point to a different (potentially malicious) image at any time. The image reference should use a SHA digest, e.g. `image: 'docker://ghcr.io/cbrgm/pr-size-labeler-action@sha256:<64-hex-char-digest>'`.

Locations:

- `action.yml:24`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed three security findings: (1) In automerge.yml, moved `${{ github.event.pull_request.html_url }}` out of all 5 `run:` shell commands across the dependabot, renovate, and cbrgm jobs into `env:` blocks as `PR_URL`, then referenced as `"$PR_URL"` in the shell. (2) In tag.yml, moved `${{ steps.bump-semver.outputs.new_version }}` out of the shell variable assignment into an `env:` block as `NEW_VERSION`, referenced as `"$NEW_VERSION"` in the script. (3) In action.yml, pinned the Docker image from the mutable tag `:v1` to the immutable digest `docker://ghcr.io/cbrgm/pr-size-labeler-action:v1@sha256:bc51f8ade7b3a1d2ab6fe5976f0119ebf3212e5fa5ea3c4d34ec7876057d9f44`.

