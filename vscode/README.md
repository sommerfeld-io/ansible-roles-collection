# Role: VSCode

With `tasks_from: main` The role installs and configures VSCode.

With `tasks_from: plugins` The role installs a set of useful plugins. This should not be run as `root`.

The role is intended to be used on Ubuntu Desktop machines.

On Arch Linux, the role first removes `code` (Code - OSS, the open-source build without Microsoft's proprietary branding, telemetry, or marketplace access) if present, then installs `visual-studio-code-bin` from the AUR via `yay` - the official Microsoft build, same as the Ubuntu path's `code` snap. This is required to pass the Dev Containers extension's build check and use the Microsoft Marketplace. `visual-studio-code-bin` is AUR-only, so this is a deliberate exception to this collection's usual policy of not depending on an AUR helper (see `docs/omarchy-workstation-role-matrix.md`); it relies on `yay`, which Omarchy ships by default. VS Code is not preinstalled on Omarchy.

`yay` internally shells out to its own `sudo pacman -U ...` to install the built AUR package, which Ansible cannot see or feed a password to. To make this work non-interactively, the role temporarily grants `{{ default_user }}` passwordless `sudo` for `/usr/bin/pacman` only, immediately before running `yay`, and revokes it again immediately afterward - regardless of whether the install succeeds or fails.

## Expected Variables

| Variable             | Description                                                          |
|----------------------|----------------------------------------------------------------------|
| `{{ default_user }}` | The user to install and configure for (typically the logged-in user) |
