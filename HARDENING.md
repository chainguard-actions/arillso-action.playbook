<!-- markdownlint-disable -->

# Hardening Report: arillso--action.playbook/0.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **arillso--action.playbook/0.5.0** was hardened automatically. 1 finding(s) were identified and resolved across 4 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action's Docker image reference in `runs.image` uses a mutable version tag (`0.5.0`) instead of an immutable SHA digest. This means the image could be replaced with a malicious version without changing the action.yml, enabling a supply-chain attack. The failing reference is: `image: "docker://ghcr.io/arillso/action.playbook:0.5.0"`. It should be pinned to a full SHA256 digest, e.g. `image: "docker://ghcr.io/arillso/action.playbook@sha256:<64-hex-char-digest>"`.

Locations:

- `action.yml:196`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned the Docker image reference in action.yml from `docker://ghcr.io/arillso/action.playbook:0.5.0` to `docker://ghcr.io/arillso/action.playbook:0.5.0@sha256:894c1d5740ba46d90d14fd9efa7c896bebcc4baaf4c96e34f8165d7a1812faf5`. The `docker://` scheme and `:0.5.0` tag are preserved alongside the immutable digest to prevent supply-chain attacks via mutable tags.

### Iteration 2

**Fixes applied:** script-injection, github-env-injection, unpinned-uses, hardcoded-credentials

**Notes:**

Fixed all findings across 5 workflow files:

1. **unpinned-uses**: Pinned all 6 date-based @2026-03-11 references in ci.yml (4), cleanup.yml (1), and security.yml (1) to SHA 0222158d619e95b1e4aada399d61682cdb1eadec.

2. **script-injection (deploy.yml)**: Moved ${{ github.event_name }}, ${{ github.event.release.tag_name }}, ${{ steps.version.outputs.version }}, ${{ needs.prepare.outputs.version }}, and all publish-summary ${{ }} expressions from run: blocks into env: blocks.

3. **script-injection + github-env-injection (security.yml)**: Fixed 'Test action functionality' (IMAGE_NAME:test), 'Set Ansible version' (with printf sanitization to strip newlines before writing to GITHUB_ENV), 'Generate test summary' (all needs outputs), and 'Check test results' (security-test.result, go-test.result).

4. **script-injection (test.yml)**: Fixed 9 run: blocks that interpolated ${{ }} expressions: Configure test level (github.event_name), Verify invalid key (steps outcome), Verify error handling (2 step outcomes), Check success/failure outputs (step outputs), Verify lint flag (step outcome), Verify retries (step outcome), Generate comprehensive test summary (16 needs results), Test quality gate (15 needs results).

5. **hardcoded-credentials (test.yml)**: Replaced hardcoded 'test_vault_password' with a dynamically generated random password via openssl rand -hex 16, stored in a step output and referenced via ${{ steps.vault-creds.outputs.vault_pass }}.

### Iteration 3

**Fixes applied:** script-injection

**Notes:**

Fixed two script-injection findings:
1. hardened/action/.github/workflows/security.yml (line 52): Moved `${{ hashFiles('Dockerfile', 'go.mod', 'go.sum', 'main.go') }}` from the run: shell string into an env: block as HASH_FILES, then referenced it as ${HASH_FILES} in the shell script.
2. hardened/action/.github/workflows/test.yml (line 68): Moved `${{ hashFiles('**/go.sum', 'Dockerfile', 'tests/**/*', 'action.yml') }}` from the run: shell string into an env: block as HASH_FILES, then referenced it as ${HASH_FILES} in the shell script.
A third hashFiles() occurrence in security.yml is in an if: expression (not a run: block) and is not a script-injection risk — left unchanged.

### Iteration 4

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection finding in the 'Determine version' step of .github/workflows/deploy.yml. Added sanitization using `safe_version=$(printf '%s' "${VERSION}" | tr -d '\n\r')` before writing to $GITHUB_OUTPUT. The VERSION variable can come from GH_RELEASE_TAG (an untrusted github.event.release.tag_name value), so stripping newlines prevents an attacker from injecting newlines to poison the GITHUB_OUTPUT file. All subsequent uses of VERSION in the step were updated to use safe_version.

