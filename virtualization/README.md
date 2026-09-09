# Role: Virtualization

The role installs and configures Virtualbox and Vagrant including all dependencies and relevant plugins.

The role is intended to be used on Ubuntu Desktop machines.

VirtualBox support for Arch Linux (installed via `pacman`) is not implemented yet - this is tracked as a TODO in [issue #34](https://github.com/sommerfeld-io/ansible-roles-collection/issues/34). Vagrant has no official Arch Linux package (AUR-only) and is therefore only installed on Ubuntu/Debian.

## Expected Variables

| Variable             | Description                                                          |
|----------------------|----------------------------------------------------------------------|
| `{{ default_user }}` | The user to install and configure for (typically the logged-in user) |
