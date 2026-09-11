# Role: Virtualization

The role installs and configures Virtualbox and Vagrant including all dependencies and relevant plugins.

The role is intended to be used on Ubuntu Desktop and Arch Linux workstations.

VirtualBox is installed via `pacman` on Arch Linux (`virtualbox` and `virtualbox-host-modules-arch`). Vagrant has no official Arch Linux package (AUR-only) and is installed via `yay` instead - a deliberate exception to this collection's usual no-AUR-helper policy, the same treatment as `visual-studio-code-bin` in the `vscode` role. The role temporarily grants `default_user` passwordless `sudo` for `pacman` while `yay` runs, then revokes it again, since `yay` internally shells out to `sudo pacman -U ...` to install the finished package and that nested `sudo` call cannot prompt for a password under Ansible.

## Expected Variables

| Variable             | Description                                                          |
|----------------------|----------------------------------------------------------------------|
| `{{ default_user }}` | The user to install and configure for (typically the logged-in user) |
