---
name: newsession
description: Use when the user types /newsession, or asks Claude to open, start or hand work off to a new Claude Code session (e.g. "hand this off to a new session", "open a new session in the api repo"), to open it in a new Terminal window, optionally with a first prompt.
argument-hint: <repo> [account] [— first prompt]
---
Open a new Terminal.app window that starts Claude in the named repo, then stop.

## Repos and accounts (edit these two tables)

| repo key | folder |
|---|---|
| `web` | `$HOME/code/web-app` |
| `api` | `$HOME/code/api-server` |

| account key | command |
|---|---|
| `default` | `claude` |
| `work` | `CLAUDE_CONFIG_DIR=$HOME/.claude-work claude` |

- `<DIR>`: the folder for the named repo. A repo not in the table: ask for its folder, never guess one.
- `<CMD>`: the command for the named account; `default` when none is named. An alias name works too (the new
  window runs an interactive shell, so aliases load).

## Open the window

Without a first prompt:

```bash
osascript -e 'tell application "Terminal" to do script "cd \"<DIR>\" && <CMD>"' -e 'tell application "Terminal" to activate'
```

With a first prompt (anything after "—" in the request, or the handoff instruction the user asked for): write the
prompt to a file first, so no quote in it can break the command, then pass it to Claude:

```bash
P=<temp dir>/newsession_prompt_<short-name>.txt      # Write the prompt here with the Write tool; no spaces in this path
osascript -e 'tell application "Terminal" to do script "cd \"<DIR>\" && <CMD> \"$(cat '"$P"')\""' -e 'tell application "Terminal" to activate'
```

## Rules

- Invoked by Claude rather than typed: only when the user asked in this conversation for a new session or a handoff
  to one, at the moment they asked for (e.g. "when the tests finish"). Say in the reply that it was opened, where,
  under which account, and with what first prompt.
- Confirm it started (`ps` shows the new `claude` process) before saying so.

Request: $ARGUMENTS
