# Role: Packages

With `tasks_from: main` The role installs packages, which are common to all machines, via the system package manager. This should be run as `root`.

With `tasks_from: ubuntu` The role installs packages specific to Ubuntu-based systems. This should be run as `root`.

With `tasks_from: raspi` The role installs packages specific to Raspberry Pi systems. This should be run as `root`.

First, run `tasks_from: main`, then either `tasks_from: ubuntu` or `tasks_from: raspi`.

With `tasks_from: desktop` The role installs desktop-specific packages (browser, media players, dev tools) common to workstation setups, on Ubuntu and Arch Linux. `asunder`, Postman, and IntelliJ IDEA are not installed on either OS and are actively uninstalled/purged on Ubuntu (no official Arch package exists, and they were dropped from the Ubuntu path too). Spotify is installed via `pacman` on Arch, but only resolves on Omarchy (which configures its own package repository for it) - it is not installed on vanilla Arch or in test/CI. See [docs/omarchy-workstation-role-matrix.md](../docs/omarchy-workstation-role-matrix.md) for the full compatibility matrix.

## Expected Variables

| Variable             | Description                                                          |
|----------------------|----------------------------------------------------------------------|
| `{{ default_user }}` | The user to install and configure for (typically the logged-in user) |
| `{{ architecture }}` | The system architecture (`amd64` or `arm64`)                         |

## Optional Variables

The following variables are optional and have default values:

| Variable                      | Description                                             | Default   |
|-------------------------------|---------------------------------------------------------|-----------|
| `{{ packages_ctop_version }}` | Install for desktop (`amd64`) or Raspberry Pi (`arm64`) | `0.7.7`   |
