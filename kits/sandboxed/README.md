# Sandboxed mixin kit

Best-practice rules for an agent running inside a Docker sandbox. Written for kit spec v3.

It's a manifest-only mixin: its single capability (`agent-context`) adds instructions, and it installs nothing.

Mixin context is loaded on demand (progressive disclosure). CLAUDE.md gets a one-line pointer, and the rules live in `~/.claude/kits-agent-context/sandboxed.md`. The kit's `description` is therefore written as a read trigger ("READ BEFORE running any command that touches the network…"). In testing, the agent read the file first on every network, server or file-handoff task.

## Rules

1. **Host commands:** `sbx` runs only on the host. Each command goes in its own block labelled "Run on host:", with real values filled in.
2. **Announce domains in advance:** domains allowed by `sbx policy init balanced` (AI services, package managers, code hosts and registries, major clouds and CDNs, OS mirrors) are used directly. For any other domain, the agent lists everything the command will reach (including redirects and CDNs) and gives one `sbx policy allow network a,b` command, then waits.
3. **On a 403, collect, then ask once:** the agent reads each 403 body, sorts it (pending approval, default deny, local deny rule, org policy), finishes the round's other calls, then sends one consolidated request.
4. **Scripts safe to retry:** idempotent scripts, no destroying state before a fetch succeeds, and the same command is rerun after approval.
5. **Short timeouts:** explicit limits for curl, wget, pip, npm, git and uv, and `timeout` for anything that may hang.
6. **Nothing opens on the host:** the agent gives the host path (translating the path in direct mode, `git fetch` in clone mode) or a link, never `xdg-open` or a browser.
7. **Servers:** bind to `0.0.0.0`. If `$SBX_PUBLISHED_PORTS` is set, the agent uses one of those already-published ports. Otherwise it gives the user `sbx port publish` / `unpublish` with the real sandbox name.

## Use

In `sbxenv.yaml`:

```yaml
kits:
  - ./kits/sandboxed
```

Or on the command line: `sbx run claude --kit ./kits/sandboxed .`

Check it with `sbx kit validate ./kits/sandboxed`.
