
# ACR Docker Sandboxes Kit (v3)

A Docker Sandboxes v3-compatible mixin for installing
[Agentic Context Registry (ACR)](https://github.com/jbaruch/agentic-context-registry).

ACR manages reusable, GitHub-hosted rules, skills, scripts,
and hooks for AI coding agents, including Codex and Claude Code.

## Attribution

This repository is a modified fork of the original
[ACR SBX Kit](https://github.com/shelajev/acr-sbx-kit)
developed by shelajev and the ACR SBX kit contributors.

The original repository uses Docker Sandbox Kit schema v2.
This fork migrates the configuration to schema v3 to support
composition with v3 workloads and mixins.

The original project is licensed under Apache License 2.0.
The original copyright notices are retained.

## Changes from upstream

- Migrated `schemaVersion` from 2 to 3.
- Replaced legacy configuration fields with v3 capabilities.
- Preserved ACR installation and checksum verification.
- Preserved the optional GitHub credential configuration.
- Preserved agent instructions for ACR initialization.

## Usage

### 1. Allow the GitHub publisher

First check the existing allowed sources:

```bash
sbx settings get kit.allowedSources
```

If only Docker Hub is listed, add this fork's publisher:

```bash
sbx settings set kit.allowedSources '["docker.io/","github.com/yjrd21/"]'
```

Preserve any additional publishers already configured.

### 2. Launch with the GitHub-hosted kit

From your project directory:

```bash
sbx run codex "$(pwd)" \
  --name sbx-acr \
  --skills off \
  --kit 'git+https://github.com/yjrd21/acr-dockersbx-kit.git#ref=main'
```

For reproducible environments, replace `main` with a
tested commit SHA or release tag.

### 3. Alternative: use a local checkout

```bash
git clone https://github.com/yjrd21/acr-dockersbx-kit.git
```

From the directory containing the cloned repository:

```bash
sbx kit validate ./acr-dockersbx-kit
```

Then run the kit:

```bash
sbx run codex "$(pwd)/sample-app" \
  --name sbx-acr \
  --skills off \
  --kit ./acr-dockersbx-kit
```

Adjust the workspace path to match your local project.
```
