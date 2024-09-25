# Devcontainers

This repository contains devcontainers that I often use.

There are two Dockerfiles (one with [poetry](https://python-poetry.org/) and one with [uv](https://github.com/astral-sh/uv)).

Each dockerfile contains the following targets.

1. `base` contains python 3.10/11/12, poetry **or** uv, pipx
2. `just` adds just to (1)
3. `terraform` adds terraform to (2)
4. `gcloud` adds gcloud to (3)

These images are built once/month and pushed to [dockerhub](https://hub.docker.com/r/jhginn/devcontainer).

Tags are structured as follows:

```
ubuntu2204-py<POETRY/UV><PYTHON-VERSION>-<YYYMMDD>-<TARGET>
```

For example:

```
ubuntu2204-pypoetry312-20240827-gcloud
```

## Sample `devcontainer.json`

I usually use a variant of the following `devcontainer.json` file with these docker images:

```json
{
    "build": {
      "dockerfile": "Dockerfile",
      "context": "."
    },
    "containerEnv": {
      "HOME": "/home/vscode"
    },
    "customizations": {
      "vscode": {
        "extensions": [
          "ms-python.python",
          "ms-toolsai.jupyter",
          "ms-python.vscode-pylance",
          "tamasfe.even-better-toml",
          "kokakiwi.vscode-just",
          "redhat.vscode-yaml",
          // "mhutchie.git-graph",
          // "ms-azuretools.vscode-docker",
          // "njpwerner.autodocstring",
          // "pomdtr.excalidraw-editor",
        ],
        "settings": {
          "python.pythonPath": "/home/vscode/workspace/.venv/bin/python",
        }
      }
    },
    "postStartCommand": "git config --global --add safe.directory ${containerWorkspaceFolder} && git config --global --add credential.useHttpPath true",
    // Optional. Remove if you don't want/need a .env file in .devcontainer dir (else spinning up the dev container fails)
    "runArgs": [
      "--env-file",
      ".devcontainer/.env"
    ],
    "workspaceFolder": "/home/vscode/workspace",
    "workspaceMount": "source=${localWorkspaceFolder},target=/home/vscode/workspace,type=bind",
    // Optional. Adds docker to dev container but also adds to build time.
    "features": {
      "ghcr.io/devcontainers/features/docker-in-docker:2": {}
    },
  }
```
