# Modified from https://github.com/shelajev/acr-sbx-kit
# Original license: Apache License 2.0
#
# Modifications:
# - Migrated SBX kit schema from v2 to v3.
# - Replaced legacy configuration fields with v3 capabilities.
#
# Copyright 2026 ACR SBX kit contributors

# ACR Docker Sandboxes kit

This mixin installs [Agentic Context Registry (ACR)](https://github.com/jbaruch/agentic-context-registry), a package manager for AI coding-agent instructions. ACR can materialize GitHub-hosted rules, skills, scripts, and hooks for Claude Code, Codex, and Cursor into the sandbox workspace.

The default ACR version is pinned and its release archive is checked against the publisher's `checksums.txt` before installation. A GitHub credential is optional: public repositories work without one, while a token enables private repositories, higher API limits, and package publishing. The real token remains in Docker Sandboxes' host-side credential proxy.

When a project contains `agents.yaml`, the kit instructs the agent to reconcile and realize its declared ACR packages before beginning substantive work in every session. The agent then rereads the resulting project instructions and uses the materialized skills. This keeps project bootstrap automatic while leaving changes and failures visible to the agent and user.

## Usage

Allow kits from this GitHub account once (Docker Hub remains allowed):

```bash
sbx settings set kit.allowedSources '["docker.io/","github.com/shelajev/"]'
```

Then run the kit directly from this Git repository:

```bash
sbx run codex --kit "git+https://github.com/shelajev/acr-sbx-kit.git#ref=main" .
```

For reproducible use, replace `main` with a release tag or full commit SHA.

From a local checkout:

```bash
sbx run codex --kit ./acr-sbx-kit .
```

The mixin is agent-independent, so `codex` can be replaced by another Docker Sandboxes agent.

To install a different ACR release, pass the version without a leading `v`:

```bash
sbx run codex \
  --kit "git+https://github.com/shelajev/acr-sbx-kit.git#ref=main" \
  --kit-arg acr.version=0.2.1 .
```

## Using ACR

Inside the sandbox, initialize a project and install a package:

```bash
acr install github:OWNER/REPO \
  --agent codex \
  --freshness outdated \
  --non-interactive
acr realize
```

Use `--agent claude-code`, `--agent codex`, and/or `--agent cursor` to select the layouts ACR should maintain. `acr install` updates dependency state; `acr realize` writes the selected agents' files. Run `acr help COMMAND` for all options.

Commit `agents.yaml` and `.agents/registry.lock` to share the dependency declarations and immutable resolutions with the team. On later sandbox sessions, the agent follows the kit instructions and runs:

```bash
acr install --non-interactive
acr realize
```

Pinned dependencies remain pinned. Dependencies declared as `latest` follow ACR's update and hold policies. When realization introduces skills or session-start hooks that the active agent cannot discover dynamically, the agent will ask for one session restart.

## GitHub authentication

For public packages, no credential is required. For private packages, increased GitHub API limits, or `acr publish`, bind a GitHub token to the kit's optional `acr-github` service using Docker Sandboxes' credential setup when prompted. The distinct service name avoids colliding with an agent's own GitHub credential declaration. The kit exposes only the `proxy-managed` sentinel as `GH_TOKEN`; the proxy injects the real token into requests to `api.github.com`, `codeload.github.com`, and `uploads.github.com`.

## Network policy

The kit allows only the GitHub hosts needed to download and verify ACR releases and for ACR to resolve, download, and publish packages:

- `github.com`
- `release-assets.githubusercontent.com`
- `api.github.com`
- `codeload.github.com`
- `objects.githubusercontent.com`
- `uploads.github.com`

## Verify the kit

```bash
sbx kit validate .
sbx kit inspect . --json
```

For a smoke test after sandbox creation:

```bash
acr version
acr help install
```

## License

Apache-2.0. See [LICENSE](LICENSE).
