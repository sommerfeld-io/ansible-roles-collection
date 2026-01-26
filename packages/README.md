# Role: Packages

With `tasks_from: main` this role installs packages, which are common to all machines, via the system package manager. This should be run as `root`.

With `tasks_from: ubuntu` this role installs packages specific to Ubuntu-based systems. This should be run as `root`.

With `tasks_from: raspi` this role installs packages specific to Raspberry Pi systems. This should be run as `root`.

First, run `tasks_from: main`, then either `tasks_from: ubuntu` or `tasks_from: raspi`.

## Expected Variables

| Variable             | Description |
|----------------------|-------------|
| `{{ ansible_user }}` | The user to install and configure for (typically the logged-in user). |
| `{{ architecture }}` | The system architecture (`amd64` or `arm64`). |

## Optional Variables

The following variables are optional and have default values:

| Variable                      | Description                                             | Default   |
|-------------------------------|---------------------------------------------------------|-----------|
| `{{ packages_ctop_version }}` | Install for desktop (`amd64`) or Raspberry Pi (`arm64`) | `0.7.7`   |
