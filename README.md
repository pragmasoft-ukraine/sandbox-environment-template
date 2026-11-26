# Docker sandbox environment template

A ready-made [Docker Sandboxes](https://docs.docker.com/ai/sandboxes/) environment for local development with Claude Code. It includes:

- `sbxenv.yaml`, the environment file. It sits outside the workspace, so the agent can't change its own environment.
- [`kits/sandboxed`](kits/sandboxed/README.md), a mixin kit that teaches the agent how to work inside a sandbox (network approvals, timeouts, ports, handing files and URLs to the host).
- `history/`, a host folder where Claude sessions and memory are kept across sandbox rebuilds (via `sbx/claude-sessions`).

## Prerequisites

1. Virtualization:
   - **Windows 11:** enable Windows Hypervisor Platform in an **admin** PowerShell, then reboot:
     ```powershell
     Enable-WindowsOptionalFeature -Online -FeatureName HypervisorPlatform -All
     ```
   - **Linux:** KVM.
2. [Install Docker Sandboxes](https://docs.docker.com/ai/sandboxes/install/). On Windows, the same command upgrades:
   ```powershell
   winget install -h Docker.sbx
   ```
3. Sign in with a Docker Hub account and apply the default network policy:
   ```sh
   sbx login
   sbx policy init balanced
   ```
4. Optional credentials:
   - `sbx secret set gitea`, then paste a Gitea personal access token (only if you keep the `sbx/gitea` kit).
   - `sbx mcp auth context7`, then sign in through OAuth to the Context7 MCP server.

## Create the environment

1. Make a folder for the project and download the template into it. You get the files only, with no `.git`.

   Windows (PowerShell):
   ```powershell
   mkdir your-git-project-name; cd your-git-project-name
   curl.exe -L https://github.com/pragmasoft-ukraine/sandbox-environment-template/archive/main.tar.gz | tar -xz --strip-components=1
   ```
   Linux / macOS:
   ```sh
   mkdir your-git-project-name && cd your-git-project-name
   curl -L https://github.com/pragmasoft-ukraine/sandbox-environment-template/archive/main.tar.gz | tar -xz --strip-components=1
   ```
2. Edit `sbxenv.yaml`:
   - `name:` is your sandbox name.
   - Under `env:`, set `GIT_AUTHOR_*` / `GIT_COMMITTER_*` to your git identity.
   - Set the `sbx/gitea` `host:` to your Gitea, or delete that kit.
   - If you change `ports:`, update `SBX_PUBLISHED_PORTS` to match.
3. Clone your project into `workspace/`:
   ```sh
   git clone https://<git-host>/<owner>/<your-git-project-name>.git workspace
   ```

## Run

```sh
sbx env plan   # review what will be created
sbx env run
```

Changes to kits, ports, mounts or secrets apply only when the sandbox is recreated (`sbx env rm`, then `sbx env run`).

## Clean up

```sh
sbx env rm
```

`history/` and `workspace/` stay on the host.
