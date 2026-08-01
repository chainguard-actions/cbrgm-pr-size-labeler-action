<!-- markdownlint-disable -->

# Hardening Report: cbrgm--pr-size-labeler-action/v1.3.11

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--pr-size-labeler-action/v1.3.11** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image pinned to a mutable tag (`docker://ghcr.io/cbrgm/pr-size-labeler-action:v1`) under `runs.image` instead of an immutable SHA digest. A tag can be silently overwritten to point to a malicious image, enabling a supply-chain attack. It should be replaced with a digest reference such as `ghcr.io/cbrgm/pr-size-labeler-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:24`

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` steps in automerge.yml directly interpolate `${{ github.event.pull_request.html_url }}` into shell commands. Because YAML template substitution happens before the shell parses the string, an attacker who controls the PR URL could inject arbitrary shell commands. The value should be passed via an `env:` variable and then double-quoted in the shell. Affected steps:
- Line 24: `run: gh pr review --approve ${{ github.event.pull_request.html_url }}` (dependabot/Approve request)
- Line 29: `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}` (dependabot/Enable automerge)
- Line 37: `run: gh pr review --approve ${{ github.event.pull_request.html_url }}` (renovate/Approve request)
- Line 42: `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}` (renovate/Enable automerge)
- Line 50: `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}` (cbrgm/Enable automerge)

Locations:

- `.github/workflows/automerge.yml:24`
- `.github/workflows/automerge.yml:29`
- `.github/workflows/automerge.yml:37`
- `.github/workflows/automerge.yml:42`
- `.github/workflows/automerge.yml:50`

### script-injection (severity: high)

Sub-rule (a): The 'Publish Git Tag' `run:` block in tag.yml directly interpolates `${{ steps.bump-semver.outputs.new_version }}` into the shell script: `new_tag=${{ steps.bump-semver.outputs.new_version }}`. Step outputs flow through YAML template substitution before the shell parses the string, so a malicious value could inject arbitrary shell commands. The value should be passed via an `env:` variable and then double-quoted in the shell (e.g., `new_tag="$NEW_TAG"`).

Locations:

- `.github/workflows/tag.yml:57`

### github-env-injection (severity: high)

The 'Get Latest Tag' step in tag.yml writes the output of `git tag | sort -V | tail -n 1` directly to `$GITHUB_ENV` without sanitization: `echo "latest_tag=$latest_tag" >> $GITHUB_ENV`. Git tag names are external data and could contain newlines that allow injecting additional environment variable definitions into the runner environment. The value must be sanitized with `printf '%s' "$latest_tag" | tr -d '\n\r'` before writing to `$GITHUB_ENV`.

Locations:

- `.github/workflows/tag.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection

**Notes:**

Fixed 4 findings across 3 files:
1. action.yml: Pinned Docker image from mutable tag `v1` to immutable digest `sha256:510e21d8994ce283b7c62d344395cf2fdb96b8ccb8f9f871db969fc17d1d3c2c`, preserving the `docker://` scheme and `:v1` tag inline.
2. .github/workflows/automerge.yml: Fixed 5 script injection instances by moving `${{ github.event.pull_request.html_url }}` out of `run:` strings into `env:` blocks as `PR_URL`, then referencing `"$PR_URL"` in shell commands.
3. .github/workflows/tag.yml (script-injection): Moved `${{ steps.bump-semver.outputs.new_version }}` from the `run:` block into an `env:` block as `NEW_VERSION`, using `new_tag="$NEW_VERSION"` in the shell script.
4. .github/workflows/tag.yml (github-env-injection): Added sanitization of the git tag value using `printf '%s' "$latest_tag" | tr -d '\n\r'` before writing to `$GITHUB_ENV` to prevent newline injection.

