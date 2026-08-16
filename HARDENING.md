<!-- markdownlint-disable -->

# Hardening Report: cbrgm--pr-size-labeler-action/v1.3.9

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--pr-size-labeler-action/v1.3.9** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image with a mutable tag `:v1` instead of a SHA digest. This means the image can be silently replaced with a different version, enabling supply-chain attacks. The image reference `docker://ghcr.io/cbrgm/pr-size-labeler-action:v1` should be replaced with a SHA-pinned digest such as `ghcr.io/cbrgm/pr-size-labeler-action@sha256:<64-hex-char-digest>`

Locations:

- `action.yml:28`

### script-injection (severity: high)

Rule (a) violation: Multiple `run:` steps in automerge.yml directly interpolate `${{ github.event.pull_request.html_url }}` into shell commands. This value is attacker-controlled (it comes from the pull request event) and is substituted into the shell command string before the shell parses it, enabling command injection. Offending lines:
- `run: gh pr review --approve ${{ github.event.pull_request.html_url }}` (dependabot job, line ~22)
- `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}` (dependabot job, line ~27)
- `run: gh pr review --approve ${{ github.event.pull_request.html_url }}` (renovate job, line ~33)
- `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}` (renovate job, line ~38)
- `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}` (cbrgm job, line ~48)
Fix: move the URL into an env var and reference it as `"$GH_PR_URL"` in the shell command.

Locations:

- `.github/workflows/automerge.yml:22`
- `.github/workflows/automerge.yml:27`
- `.github/workflows/automerge.yml:33`
- `.github/workflows/automerge.yml:38`
- `.github/workflows/automerge.yml:48`

### script-injection (severity: high)

Rule (a) violation: The 'Publish Git Tag' `run:` block in tag.yml directly interpolates `${{ steps.bump-semver.outputs.new_version }}` into a shell command: `new_tag=${{ steps.bump-semver.outputs.new_version }}`. The step output is substituted into the shell script before the shell parses it, enabling command injection if the output contains shell metacharacters. Fix: move the value into an env var (e.g. `NEW_TAG: ${{ steps.bump-semver.outputs.new_version }}`) and reference it as `"$NEW_TAG"` in the shell script.

Locations:

- `.github/workflows/tag.yml:55`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed three security findings: (1) Pinned the Docker image in action.yml from mutable tag ':v1' to immutable digest 'docker://ghcr.io/cbrgm/pr-size-labeler-action:v1@sha256:77ee5bf485d4e2aab13a5eb571117a60a0691983b2fb19d240ed137d2d055609', preserving the docker:// scheme and tag inline. (2) Fixed all 5 script injection instances in automerge.yml by moving github.event.pull_request.html_url into a GH_PR_URL env var and referencing it as "$GH_PR_URL" in each run: command. (3) Fixed script injection in tag.yml by moving steps.bump-semver.outputs.new_version into a NEW_TAG env var and referencing it as "$NEW_TAG" in the shell script.

