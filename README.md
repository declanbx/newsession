# newsession

A Claude Code skill that opens a new Claude Code session in its own Terminal window: in the repo you name, under
the account you name, and optionally with a first prompt. Use it to start a fresh session without leaving the one
you are in, or to have one session hand a task to another.

Requires macOS, Terminal.app and Claude Code.

## Install

```bash
mkdir -p ~/.claude/skills/newsession
curl -fsSL https://raw.githubusercontent.com/declanbx/newsession/main/SKILL.md -o ~/.claude/skills/newsession/SKILL.md
```

Then open `~/.claude/skills/newsession/SKILL.md` and fill in its two tables:

- **Repos:** a short key for each project and its folder. Write folders as absolute paths or start them with
  `$HOME/`; a leading `~` is not expanded inside the quotes the skill uses.
- **Accounts:** a short key for each Claude account and the command that starts it (see below). With one account,
  keep only `default`.

Neither table may contain double quotes (`"`): the skill wraps both in quotes of its own.

The first time the skill runs, macOS may ask to let your terminal control Terminal.app. Allow it.

## Use

Type it, or ask in plain words:

| You type | What opens |
|---|---|
| `/newsession thesis` | A new window in the `thesis` folder, running your default account |
| `/newsession website work` | A new window in the `website` folder, running the `work` account |
| `/newsession website work — fix the broken contact form and report back` | The same, with that text as the new session's first prompt |
| "hand this off to a new session in my thesis folder" | Claude writes the handoff prompt and opens the window |

Claude only opens a session when you ask for one, but it can open one itself when you do: you don't have to type
the command. It confirms the new `claude` process is running before telling you it opened.

Do not add `disable-model-invocation: true` to the skill's header. That setting means only you can start the skill
by typing `/newsession`, so a request like "hand this off to a new session when the tests finish" silently does
nothing.

## More than one Claude account

Claude Code keeps each account's login, settings and skills in one config folder, `~/.claude` by default. Setting
`CLAUDE_CONFIG_DIR` points it at another folder, which gives you a second account that is logged in separately.

1. Run `CLAUDE_CONFIG_DIR=$HOME/.claude-work claude` once and log in with the second account.
2. Add it to the skill's accounts table: key `work`, command `CLAUDE_CONFIG_DIR=$HOME/.claude-work claude`.
3. Make the skill available inside that account too, since each account reads skills from its own folder:

   ```bash
   mkdir -p ~/.claude-work/skills
   ln -s ~/.claude/skills/newsession ~/.claude-work/skills/newsession
   ```

   One copy of the skill then serves both accounts, so the tables only need editing once.

A shell alias works in the command column as well. For example, with
`alias claude-work='CLAUDE_CONFIG_DIR=$HOME/.claude-work claude'` in your shell's startup file, the command can
simply be `claude-work`.

## How it works

The skill tells Claude to run `osascript`, which asks Terminal.app to open a window and type
`cd "<folder>" && <command>` into it. A first prompt is written to a file and passed as `"$(cat <file>)"`, so
quotes or special characters in it cannot break the command.

To use another terminal app (iTerm2, Ghostty and others), replace the two `osascript` lines in `SKILL.md` with that
app's equivalent.

## License

MIT
