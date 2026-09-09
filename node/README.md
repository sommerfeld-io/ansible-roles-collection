# Role: Node

Use `tasks_from: main` to install NodeJS. Run with `become: true`!

Use `tasks_from: modules-ai` to install node modules related to AI (CLIs and other tools). Run **not** with `become: true`!

> **:zap: NOTE:** With the tasks from `modules-ai`, Gemini CLI and Copilot CLI are installed but not fully configured by Ansible. Most likely, there is still some manual config to be done when running the tools for the first time.

The role is intended to be used on all machines (RasPi and workstation).

On Arch Linux, `nodejs` and `npm` are installed straight from the official Arch repos via `pacman`. `{{ node_version }}` only applies to the Ubuntu/Debian NodeSource apt-repo path — Arch always tracks whatever version is current in the Arch repos.

## Expected Variables

| Variable             | Description                                                          |
|----------------------|----------------------------------------------------------------------|
| `{{ default_user }}` | The user to install and configure for (typically the logged-in user) |

## Optional Variables

The following variables are optional and have default values:

| Variable             | Description                   | Default |
|----------------------|-------------------------------|---------|
| `{{ node_version }}` | The NodeJS version to install | `25.x`  |
