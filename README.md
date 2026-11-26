# Set up docker sandbox environment

Set up Docker sandbox environment for local development

## Prerequisites

Hypervisor (Windows) or KVM (Linux)

`sudo Enable-WindowsOptionalFeature -Online -FeatureName HypervisorPlatform -All`

[Install docker sandbox](https://docs.docker.com/ai/sandboxes/install/)

`winget install -h Docker.sbx` (same command for upgrade)

Docker hub account (for `sbx login`)

`sbx login`

`sbx policy init balanced`

`sbx secret set gitea` and paste gitea PAT, created in gitea admin dashboard

`sbx mcp auth context7` use oauth to authenticate with context7 mcp

## Prepare directories

`mkdir your-git-project-name; cd your-git-project-name`

`curl.exe -L https://github.com/pragmasoft-ukraine/sandbox-environment-template/archive/main.tar.gz | tar -xz --strip-components=1`

`mkdir history`

`git clone https://<git-repo>/<owner>/<your-git-project-name>.git workspace`

## Run

`sbx env run`

## Clean up

`sbx env rm`
