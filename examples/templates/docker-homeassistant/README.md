---
display_name: Home Assistant (VS Code + Copilot)
description: Develop Home Assistant in a devcontainer with VS Code and GitHub Copilot pre-configured
icon: ../../../site/static/icon/docker.png
maintainer_github: coder
verified: true
tags: [container, docker, devcontainer, envbuilder, homeassistant, copilot]
---

# Home Assistant Development with VS Code and GitHub Copilot

Provision an [Envbuilder](https://github.com/coder/envbuilder) container based on the
[`home-assistant/core`](https://github.com/home-assistant/core) devcontainer as a
[Coder workspace](https://coder.com/docs/workspaces) with VS Code and GitHub Copilot
pre-configured.

## Prerequisites

### Infrastructure

Coder must have access to a running Docker socket, and the `coder` user must be a member of the `docker` group:

```shell
# Add coder user to Docker group
sudo usermod -aG docker coder

# Restart Coder server
sudo systemctl restart coder

# Test Docker
sudo -u coder docker ps
```

### GitHub Copilot

A valid [GitHub Copilot](https://github.com/features/copilot) subscription is required.
The Copilot extension must be installed from the VS Code extension marketplace after the
workspace starts.

If your deployment uses the [AI Bridge Proxy](../../docs/ai-coder/ai-bridge/ai-bridge-proxy/index.md),
see the [Copilot configuration guide](../../docs/ai-coder/ai-bridge/clients/copilot.md) for
proxy and certificate setup.

## What's included

- **Home Assistant devcontainer** — clones `home-assistant/core` and builds the upstream
  `devcontainer.json`, giving you a fully configured Python environment with all Home
  Assistant dependencies.
- **VS Code (code-server)** — available in the browser via the Coder dashboard.
- **Copilot settings pre-applied** — on first start, the workspace writes VS Code settings
  that enable GitHub Copilot inline suggestions for Python, YAML, and JSON files so that
  Copilot works as soon as you install the extension.

## Architecture

This template provisions the following resources:

- Envbuilder cached image (conditional, persistent) using [`terraform-provider-envbuilder`](https://github.com/coder/terraform-provider-envbuilder)
- Docker image (persistent) using [`envbuilder`](https://github.com/coder/envbuilder)
- Docker container (ephemeral)
- Docker volume (persistent on `/workspaces`)

The `home-assistant/core` repository is cloned inside the `/workspaces` volume on first
start. Any local changes to the devcontainer files inside the volume are applied when you
restart the workspace. Tools or files outside `/workspaces` that are not part of the
devcontainer specification are not persisted — edit `devcontainer.json` instead.

> **Note**
> This template is designed to be a starting point! Edit the Terraform to extend it for
> your use case.

## Caching

To speed up builds you can use a container registry as a cache. When creating the template,
set the `cache_repo` variable to a valid Docker repository.

For example, run a local registry:

```shell
docker run --detach \
  --volume registry-cache:/var/lib/registry \
  --publish 5000:5000 \
  --name registry-cache \
  --net=host \
  registry:2
```

Then set `cache_repo` to `localhost:5000/envbuilder-cache` when creating the template.

See the [Envbuilder Terraform Provider Examples](https://github.com/coder/terraform-provider-envbuilder/blob/main/examples/resources/envbuilder_cached_image/envbuilder_cached_image_resource.tf) for a more complete example.

> [!NOTE]
> We recommend using a registry cache with authentication enabled.
> To allow Envbuilder to authenticate with the registry cache, specify the variable
> `cache_repo_docker_config_path` with the path to a Docker config `.json` on disk
> containing valid credentials for the registry.

## Getting started with Copilot

1. Open the workspace in VS Code via the Coder dashboard.
2. Install the [GitHub Copilot](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)
   extension from the VS Code extension marketplace.
3. Sign in to GitHub when prompted.
4. Copilot inline suggestions are automatically enabled for Python, YAML, and JSON files.

For AI Bridge Proxy configuration, see the
[Copilot client guide](../../docs/ai-coder/ai-bridge/clients/copilot.md).
