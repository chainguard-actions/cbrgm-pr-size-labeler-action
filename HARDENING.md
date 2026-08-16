<!-- markdownlint-disable -->

# Hardening Report: cbrgm--pr-size-labeler-action/v1.3.8

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--pr-size-labeler-action/v1.3.8** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image referenced by a mutable tag (`docker://ghcr.io/cbrgm/pr-size-labeler-action:v1`) instead of an immutable SHA digest. This means the image content can change without notice, enabling supply-chain attacks. It should be pinned to a specific SHA digest, e.g. `ghcr.io/cbrgm/pr-size-labeler-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:24`

### script-injection (severity: high)

Rule (a): Multiple `run:` blocks in automerge.yml directly interpolate `${{ github.event.pull_request.html_url }}` — a `github.*` context expression — into shell commands. Before the shell executes the command, GitHub Actions performs template substitution, allowing an attacker to inject arbitrary shell metacharacters via a crafted pull request URL. Affected steps: 'Approve request' (dependabot job, line 23), 'Enable automerge' (dependabot job, line 28), 'Approve request' (renovate job, line 35), 'Enable automerge' (renovate job, line 40), 'Enable automerge' (cbrgm job, line 47). Example offending line: `run: gh pr review --approve ${{ github.event.pull_request.html_url }}`

Locations:

- `.github/workflows/automerge.yml:23`
- `.github/workflows/automerge.yml:28`
- `.github/workflows/automerge.yml:35`
- `.github/workflows/automerge.yml:40`
- `.github/workflows/automerge.yml:47`

### script-injection (severity: high)

Rule (a): The 'Publish Git Tag' `run:` block in tag.yml directly interpolates `${{ steps.bump-semver.outputs.new_version }}` — a `steps.*.outputs.*` expression — into the shell script. GitHub Actions substitutes this value before the shell parses the command, allowing a malicious step output to inject arbitrary shell commands. Offending line: `new_tag=${{ steps.bump-semver.outputs.new_version }}`

Locations:

- `.github/workflows/tag.yml:52`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Three fixes applied: (1) action.yml: Pinned Docker image from mutable tag `docker://ghcr.io/cbrgm/pr-size-labeler-action:v1` to immutable digest `docker://ghcr.io/cbrgm/pr-size-labeler-action:v1@sha256:77ee5bf485d4e2aab13a5eb571117a60a0691983b2fb19d240ed137d2d055609`. (2) automerge.yml: Fixed all 5 script injection instances by moving `${{ github.event.pull_request.html_url }}` out of `run:` blocks and into `env:` blocks as `PR_URL`, referenced as `"$PR_URL"` in shell commands. (3) tag.yml: Fixed script injection in 'Publish Git Tag' step by moving `${{ steps.bump-semver.outputs.new_version }}` into an `env:` block as `NEW_VERSION`, referenced as `"$NEW_VERSION"` in the shell script.

