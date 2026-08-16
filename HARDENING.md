<!-- markdownlint-disable -->

# Hardening Report: cbrgm--pr-size-labeler-action/v1.3.6

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--pr-size-labeler-action/v1.3.6** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation in run: blocks. In automerge.yml, `${{ github.event.pull_request.html_url }}` is interpolated directly into shell commands across multiple steps (dependabot, renovate, and cbrgm jobs). A malicious PR title or URL could inject shell metacharacters. Offending lines: `run: gh pr review --approve ${{ github.event.pull_request.html_url }}` and `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}`.

Locations:

- `.github/workflows/automerge.yml:21`
- `.github/workflows/automerge.yml:27`
- `.github/workflows/automerge.yml:36`
- `.github/workflows/automerge.yml:42`
- `.github/workflows/automerge.yml:49`

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation in run: block. In tag.yml, `${{ steps.bump-semver.outputs.new_version }}` is interpolated directly into a multi-line shell script: `new_tag=${{ steps.bump-semver.outputs.new_version }}`. The step output value flows through YAML template substitution before the shell parses it, allowing injection of shell metacharacters.

Locations:

- `.github/workflows/tag.yml:52`

### unpinned-uses (severity: high)

Multiple unpinned action/image references found:
1. action.yml: `image: 'docker://ghcr.io/cbrgm/pr-size-labeler-action:v1'` — uses a mutable tag `:v1` instead of a SHA digest (e.g. `@sha256:<digest>`).
2. .github/workflows/stale.yml: `uses: cbrgm/cleanup-stale-branches-action@main` — uses the mutable `main` branch ref instead of a full 40-character commit SHA.
3. .github/workflows/tag.yml: `uses: cbrgm/semver-bump-action@main` — uses the mutable `main` branch ref instead of a full 40-character commit SHA.

Locations:

- `action.yml:24`
- `.github/workflows/stale.yml:29`
- `.github/workflows/tag.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed all findings:
1. automerge.yml (script-injection): Moved `${{ github.event.pull_request.html_url }}` out of all 5 `run:` shell commands into `env:` blocks as `PR_URL`, referenced as `"$PR_URL"` in shell.
2. tag.yml (script-injection): Moved `${{ steps.bump-semver.outputs.new_version }}` out of the `run:` shell script into an `env:` block as `NEW_VERSION`, referenced as `"$NEW_VERSION"` in shell.
3. tag.yml (unpinned-uses): Pinned `cbrgm/semver-bump-action@main` to full SHA `cc89dae95968de9a49b9a4879290be60e1dd5600`.
4. stale.yml (unpinned-uses): Pinned `cbrgm/cleanup-stale-branches-action@main` to full SHA `1cd2068354f38284bb05b8ba279ae30790d68c44`.
5. action.yml (unpinned-uses): Pinned `docker://ghcr.io/cbrgm/pr-size-labeler-action:v1` to digest `sha256:77ee5bf485d4e2aab13a5eb571117a60a0691983b2fb19d240ed137d2d055609`, preserving the `docker://` scheme and `:v1` tag inline.

