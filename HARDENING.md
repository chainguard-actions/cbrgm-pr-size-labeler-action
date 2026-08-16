<!-- markdownlint-disable -->

# Hardening Report: cbrgm--pr-size-labeler-action/v1.3.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--pr-size-labeler-action/v1.3.7** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses a Docker image pinned to a mutable tag (:v1) instead of an immutable SHA digest. This means the action could silently pull a different (potentially malicious) image on future runs. The reference `image: 'docker://ghcr.io/cbrgm/pr-size-labeler-action:v1'` should be replaced with a SHA-digest reference such as `image: 'docker://ghcr.io/cbrgm/pr-size-labeler-action@sha256:<64-hex-char-digest>'`.

Locations:

- `action.yml:28`

### script-injection (severity: high)

automerge.yml directly interpolates `${{ github.event.pull_request.html_url }}` into run: shell commands (rule a). A malicious PR title or URL could contain shell metacharacters that are expanded before the shell ever sees the command. All five affected steps pass the raw expression as a positional argument to `gh pr review` or `gh pr merge`. The value should be routed through an env: variable and double-quoted in the shell instead: `env: PR_URL: ${{ github.event.pull_request.html_url }}` then `run: gh pr review --approve "$PR_URL"`.

Locations:

- `.github/workflows/automerge.yml:24`
- `.github/workflows/automerge.yml:30`
- `.github/workflows/automerge.yml:38`
- `.github/workflows/automerge.yml:44`
- `.github/workflows/automerge.yml:52`

### script-injection (severity: high)

tag.yml 'Publish Git Tag' step directly interpolates `${{ steps.bump-semver.outputs.new_version }}` into a run: shell script (rule a): `new_tag=${{ steps.bump-semver.outputs.new_version }}`. The interpolated value is then used unquoted in `git tag $new_tag` and `git push origin $new_tag` (rule b). If the step output contains shell metacharacters or newlines, arbitrary commands could be injected. Fix: route through an env: variable and double-quote all expansions: `env: NEW_TAG: ${{ steps.bump-semver.outputs.new_version }}` then `new_tag="$NEW_TAG"` and `git tag "$new_tag"`.

Locations:

- `.github/workflows/tag.yml:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Three fixes applied: (1) action.yml: pinned Docker image from mutable ':v1' tag to immutable digest 'docker://ghcr.io/cbrgm/pr-size-labeler-action:v1@sha256:77ee5bf485d4e2aab13a5eb571117a60a0691983b2fb19d240ed137d2d055609'. (2) automerge.yml: moved all 5 occurrences of '${{ github.event.pull_request.html_url }}' out of run: shell strings into env: blocks as PR_URL, then referenced as "$PR_URL" in the shell commands. (3) tag.yml: moved '${{ steps.bump-semver.outputs.new_version }}' out of the run: shell script into an env: block as NEW_VERSION, assigned with new_tag="$NEW_VERSION", and double-quoted all subsequent uses of $new_tag and $latest_tag in git commands.

