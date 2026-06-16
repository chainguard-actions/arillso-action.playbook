<!-- markdownlint-disable -->

# Hardening Report: arillso--action.playbook/0.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **arillso--action.playbook/0.5.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action uses a Docker image referenced by a mutable tag (`0.5.0`) rather than an immutable SHA digest. If the tag is moved or the registry is compromised, a different (potentially malicious) image could be pulled silently. The reference `docker://ghcr.io/arillso/action.playbook:0.5.0` should be replaced with a SHA-pinned form such as `docker://ghcr.io/arillso/action.playbook@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:221`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced the mutable Docker image tag reference `docker://ghcr.io/arillso/action.playbook:0.5.0` with the immutable SHA256 digest `docker://ghcr.io/arillso/action.playbook@sha256:894c1d5740ba46d90d14fd9efa7c896bebcc4baaf4c96e34f8165d7a1812faf5` in action.yml. The original tag `0.5.0` is preserved as a comment for readability.

