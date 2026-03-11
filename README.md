# claude-config

Global Claude Code configuration, distributed via git.

## Setup

Clone the repo and symlink files into `~/.claude/`:

```sh
git clone git@github.com:FranckErnewein/claude-config.git ~/claude-config

ln -sf ~/claude-config/CLAUDE.md ~/.claude/CLAUDE.md
ln -sf ~/claude-config/settings.json ~/.claude/settings.json
```

## Structure

- `CLAUDE.md` — Global instructions (principles, code style, zergling mode, git conventions)
- `settings.json` — Global settings (plugins, MCP servers, permissions)
