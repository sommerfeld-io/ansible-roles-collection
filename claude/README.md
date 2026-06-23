# Role: claude

Installs [Claude Code](https://code.claude.com/docs/en/overview) and deploys a set of custom slash-command skills to the user's global Claude commands directory (`~/.claude/commands/`).

The install step runs the official Claude Code setup script and is skipped automatically if the binary already exists. Skills are deployed as plain markdown files and are immediately available as `/skill-name` commands inside any Claude Code session.

## Expected Variables

The following variables are expected to be set globally or passed to the role:

| Variable                        | Description                                                                                          |
|---------------------------------|------------------------------------------------------------------------------------------------------|
| `{{ default_user }}`            | The user to install and configure Claude Code for (typically the logged-in user)                     |
