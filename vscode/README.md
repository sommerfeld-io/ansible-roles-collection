# Role: VSCode

With `tasks_from: main` The role installs and configures VSCode.

With `tasks_from: plugins` The role installs a set of useful plugins. This should not be run as `root`.

The role is intended to be used on Ubuntu Desktop machines.

## Expected Variables

| Variable             | Description                                                          |
|----------------------|----------------------------------------------------------------------|
| `{{ ansible_user }}` | The user to install and configure for (typically the logged-in user) |
