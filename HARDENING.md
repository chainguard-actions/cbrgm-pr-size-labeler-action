<!-- markdownlint-disable -->

# Hardening Report: cbrgm--pr-size-labeler-action/v1.3.14

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--pr-size-labeler-action/v1.3.14** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml Docker image reference uses a mutable tag (':v1') instead of an immutable SHA digest. This means the action could silently pull a different (potentially malicious) image if the tag is moved. The image reference 'docker://ghcr.io/cbrgm/pr-size-labeler-action:v1' should be replaced with a pinned digest such as 'docker://ghcr.io/cbrgm/pr-size-labeler-action@sha256:<64-hex-char-digest>'.

Locations:

- `action.yml:24`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned the Docker image reference in action.yml from the mutable tag 'docker://ghcr.io/cbrgm/pr-size-labeler-action:v1' to the immutable digest 'docker://ghcr.io/cbrgm/pr-size-labeler-action:v1@sha256:f898cff9429a7c1e31e9c0de5c50f59ea833600a2574dc4edb2f6cd9d29fd689'. The 'docker://' scheme and ':v1' tag are preserved inline as required.

