<!-- markdownlint-disable -->

# Hardening Report: cbrgm--pr-size-labeler-action/v1.3.10

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--pr-size-labeler-action/v1.3.10** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): `${{ github.event.pull_request.html_url }}` is directly interpolated inside `run:` shell commands in automerge.yml. This allows an attacker to craft a pull request URL containing shell metacharacters that get executed by the runner. Affected lines: `run: gh pr review --approve ${{ github.event.pull_request.html_url }}` (dependabot job, line ~19; renovate job, line ~33) and `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}` (dependabot job, line ~24; renovate job, line ~38; cbrgm job, line ~48). These should be moved to an `env:` block and the env var double-quoted in the shell command.

Locations:

- `.github/workflows/automerge.yml:19`
- `.github/workflows/automerge.yml:24`
- `.github/workflows/automerge.yml:33`
- `.github/workflows/automerge.yml:38`
- `.github/workflows/automerge.yml:48`

### script-injection (severity: high)

Sub-rule (a): `${{ steps.bump-semver.outputs.new_version }}` is directly interpolated inside a `run:` shell command in tag.yml. The step output value is assigned unquoted to a shell variable (`new_tag=${{ steps.bump-semver.outputs.new_version }}`), and then used unquoted in subsequent `git tag $new_tag` and `git push origin $new_tag` commands. A malicious step output could inject shell metacharacters. The value should be passed via an `env:` variable and double-quoted in the script.

Locations:

- `.github/workflows/tag.yml:55`

### unpinned-uses (severity: high)

The action.yml Docker image reference uses a mutable tag (`v1`) instead of an immutable SHA digest. This means the action could silently pull a different (potentially malicious) image on future runs if the tag is moved. Failing reference: `image: 'docker://ghcr.io/cbrgm/pr-size-labeler-action:v1'`. It should be replaced with a SHA-digest reference, e.g. `image: 'docker://ghcr.io/cbrgm/pr-size-labeler-action@sha256:<64-hex-char-digest>'`.

Locations:

- `action.yml:24`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed 3 findings across 3 files:
1. automerge.yml: Moved `github.event.pull_request.html_url` into `env: PR_URL:` blocks for all 5 affected steps (dependabot approve, dependabot automerge, renovate approve, renovate automerge, cbrgm automerge). Shell commands now reference `"$PR_URL"` with proper double-quoting.
2. tag.yml: Moved `steps.bump-semver.outputs.new_version` into `env: NEW_VERSION:` block in the 'Publish Git Tag' step. Shell script now uses `new_tag="$NEW_VERSION"` and double-quotes all variable expansions including `"$latest_tag"`, `"$new_tag"`.
3. action.yml: Pinned Docker image from mutable `docker://ghcr.io/cbrgm/pr-size-labeler-action:v1` to immutable digest `docker://ghcr.io/cbrgm/pr-size-labeler-action:v1@sha256:77ee5bf485d4e2aab13a5eb571117a60a0691983b2fb19d240ed137d2d055609`, preserving the docker:// scheme and :v1 tag inline.

