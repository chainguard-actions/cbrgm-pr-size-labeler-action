<!-- markdownlint-disable -->

# Hardening Report: cbrgm--pr-size-labeler-action/v1.3.12

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--pr-size-labeler-action/v1.3.12** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image pinned to a mutable tag (`docker://ghcr.io/cbrgm/pr-size-labeler-action:v1`) instead of an immutable SHA digest. A tag can be silently overwritten, enabling a supply-chain attack. Replace with a reference of the form `ghcr.io/cbrgm/pr-size-labeler-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:28`

### script-injection (severity: high)

Sub-rule (a): `${{ github.event.pull_request.html_url }}` is interpolated directly into `run:` shell commands in automerge.yml. The expression is expanded by the Actions template engine before the shell parses the command, allowing an attacker to craft a PR URL containing shell metacharacters (`;`, `|`, `$(...)`, etc.) to achieve arbitrary command execution. Affected steps: 'Approve request' (dependabot job, line 23), 'Enable automerge' (dependabot job, line 29), 'Approve request' (renovate job, line 38), 'Enable automerge' (renovate job, line 44), 'Enable automerge' (cbrgm job, line 52). Fix: move the URL into an `env:` variable and reference it as a quoted shell variable, e.g. `env: PR_URL: ${{ github.event.pull_request.html_url }}` then `run: gh pr review --approve "$PR_URL"`.

Locations:

- `.github/workflows/automerge.yml:23`
- `.github/workflows/automerge.yml:29`
- `.github/workflows/automerge.yml:38`
- `.github/workflows/automerge.yml:44`
- `.github/workflows/automerge.yml:52`

### script-injection (severity: high)

Sub-rule (a): `${{ steps.bump-semver.outputs.new_version }}` is interpolated directly into a `run:` shell script in tag.yml (line 57: `new_tag=${{ steps.bump-semver.outputs.new_version }}`). `steps.*.outputs.*` is an untrusted/workflow-controllable context that is expanded by the Actions template engine before the shell parses the command, enabling injection of arbitrary shell commands. Fix: move the value into an `env:` variable and reference it as a quoted shell variable, e.g. `env: NEW_TAG: ${{ steps.bump-semver.outputs.new_version }}` then `new_tag="$NEW_TAG"`.

Locations:

- `.github/workflows/tag.yml:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Three fixes applied: (1) action.yml: Pinned Docker image from mutable tag `docker://ghcr.io/cbrgm/pr-size-labeler-action:v1` to immutable digest `docker://ghcr.io/cbrgm/pr-size-labeler-action:v1@sha256:ac29ca2fe9e164a5f3e3ac84c3a17b2798cd026a9953d2f0cc94be79bff1c279`. (2) automerge.yml: Moved `${{ github.event.pull_request.html_url }}` out of all 5 `run:` shell commands into `env: PR_URL:` blocks, referencing as `"$PR_URL"` in the shell. (3) tag.yml: Moved `${{ steps.bump-semver.outputs.new_version }}` out of the `run:` shell script into `env: NEW_VERSION:`, referencing as `"$NEW_VERSION"` in the shell.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted variable expansions in the 'Publish Git Tag' step of .github/workflows/tag.yml. Changed `git rev-list $latest_tag..HEAD` to `git rev-list "$latest_tag"..HEAD`, `git tag $new_tag` to `git tag "$new_tag"`, and `git push origin $new_tag` to `git push origin "$new_tag"`. The variable `new_tag` is derived from the `NEW_VERSION` env var which is sourced from a workflow-controllable step output, so all expansions must be double-quoted to prevent shell metacharacter injection.

