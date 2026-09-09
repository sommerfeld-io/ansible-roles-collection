# Role: VSCode

With `tasks_from: main` The role installs and configures VSCode.

With `tasks_from: plugins` The role installs a set of useful plugins. This should not be run as `root`.

The role is intended to be used on Ubuntu Desktop machines.

On Arch Linux, the role installs `code` (Code - OSS) from the official Arch repos via `pacman`. This is the open-source build without Microsoft's proprietary branding, telemetry, or marketplace access, unlike the Ubuntu path which installs Microsoft's own `code` snap. VS Code is not preinstalled on Omarchy.

## Expected Variables

| Variable             | Description                                                          |
|----------------------|----------------------------------------------------------------------|
| `{{ default_user }}` | The user to install and configure for (typically the logged-in user) |
