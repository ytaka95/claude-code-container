# claude-code-container

A container image designed to securely run [Claude Code](https://docs.anthropic.com/en/docs/claude-code) within a container environment.
This repository periodically builds the image using GitHub Actions and publishes it to the GitHub Container Registry.

## Important Notes

As this repository serves primarily as my personal development tool, it may contain breaking changes without prior notice.

## Images

| Image Name | Purpose |
| --- | --- |
| `ghcr.io/ytaka95/claude-code-container` | Production-ready image containing Claude Code |
| `ghcr.io/ytaka95/claude-code-container/base` | Base image including all development tools and dependencies |

## Quick Start Guide

To launch the container from the root directory of your project, follow these steps:

- Mount your project directory to `/workspace` inside the container
- To persist Claude Code configuration and authentication credentials, mount a host directory (e.g., `~/.config/claude-container`) to `CLAUDE_CONFIG_DIR`
- If using `gh` commands or accessing private repositories, pass your GitHub Personal Access Token to `GITHUB_TOKEN`
- To prevent conflicts between the host environment and venv, specify a dedicated container-specific venv directory using `UV_PROJECT_ENVIRONMENT`
    - Note: Environment isolation for non-Python environments is not currently supported

### Example

Using [Apple container](https://github.com/apple/container):

```sh
CCC_CPUS=2
CCC_MEMORY=4gb
CCC_GHTOKEN="github_pat_xxx"
CCC_USER=ccuser
CCC_CLAUDE_CONFIG_DIR=/home/${CCC_USER}/.config/claude
CCC_UV_PROJECT_ENVIRONMENT=.venv_ccc
CCC_IMAGE_URL="ghcr.io/ytaka95/claude-code-container"

alias ccc="container run --rm -it \
  --mount type=bind,source=${HOME}/.config/claude-container,target=${CCC_CLAUDE_CONFIG_DIR} \
  --mount type=bind,source=\$(pwd),target=/workspace \
  -e CLAUDE_CONFIG_DIR=${CCC_CLAUDE_CONFIG_DIR} \
  -e GITHUB_TOKEN=${CCC_GHTOKEN} \
  -e UV_PROJECT_ENVIRONMENT=${CCC_UV_PROJECT_ENVIRONMENT} \
  -e HOST_DIR=\$(pwd) \
  --cpus ${CCC_CPUS} \
  --memory ${CCC_MEMORY} \
  ${CCC_IMAGE_URL}:latest claude"
```

Then, run:

```sh
ccc
```

## Building Locally

```sh
# Base image
docker build -t claude-code-container-base -f docker/base/Dockerfile .

# Production image
docker build -t claude-code-container \
  --build-arg BASE_IMAGE=claude-code-container-base \
  -f docker/claude-code/Dockerfile .
```

## License

[MIT License](./LICENSE)
