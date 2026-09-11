# Role: Bash

Configures bash settings and create an SSH keypair.

The role is intended to be used on all machines (RasPi and workstation).

On Arch Linux, also auto-starts an ssh-agent and loads the managed key in
`.bashrc` so `SSH_AUTH_SOCK` is available for tools like VS Code devcontainer
forwarding.

## Expected Variables

| Variable                 | Description                                                                     |
|--------------------------|---------------------------------------------------------------------------------|
| `{{ default_user }}`     | The user to install and configure for (typically the logged-in user)            |
| `{{ ps1 }}`              | The bash prompt string. This variable is set in the host inventory              |
| `{{ ps1_root }}`         | The bash prompt for the user `root`. This variable is set in the host inventory |
