# Role: Node

Use `tasks_from: main` to install NodeJS.

Use `tasks_from: modules-ai` to install node modules related to AI (CLIs and other tools).

The role is intended to be used on all machines (RasPi and workstation).

## Expected Variables

| Variable             | Description                                                          |
|----------------------|----------------------------------------------------------------------|
| `{{ ansible_user }}` | The user to install and configure for (typically the logged-in user) |

## Optional Variables

The following variables are optional and have default values:

| Variable             | Description                   | Default |
|----------------------|-------------------------------|---------|
| `{{ node_version }}` | The NodeJS version to install | `25.x`  |
