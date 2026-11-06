# Marc OS

Marc's own Claude plugin. This repo is also its marketplace.

| Part | What it does |
|---|---|
| `library` skill | Connects to his library and runs the skills and rules kept there. The skills live in the library, not here, so they improve without a plugin update. |
| `setup` skill | A guided setup: creates the library on Google, connects it to the Mac, checks it works. Also disconnects. |
| `connector/` | The library connection, a self-contained Mac program (Apple chip and Intel). Nothing to install. |

## What's not here, on purpose

This repo is public. No keys, no passwords, no account names, no library contents and no details of where the library is. Each Mac keeps its own: the key in the Keychain, the settings in `~/.config/marc-os/`.

## Install

In the Claude desktop app: **Customize**, then **Plugins**, then **Add**, then **Add marketplace**. Enter `flynnkristina/marc-os` and install **Marc OS**. Turn on **Sync automatically**.

It worked if typing `/` in a new Cowork session shows `setup` and `library` (Cowork lists them by their short names). Then say "set up my library".

Claude Code:

```bash
claude plugin marketplace add flynnkristina/marc-os
```

```bash
claude plugin install marc-os@marc-os
```

## Ship a change

1. Edit the skill.
2. Raise `version` in `.claude-plugin/plugin.json`. Without a new version, installed copies don't change.
3. Run `claude plugin validate .` (prints a pass), then commit and push.

The connector's source lives in [storyeq-plugins](https://github.com/flynnkristina/storyeq-plugins), under `connector/`.

## Rules for this repo

- Never commit keys, tokens or passwords.
- No top-level `bin/` folder; it stops claude.ai and Cowork installing the plugin.
- Nothing about who uses it or what they work on.
