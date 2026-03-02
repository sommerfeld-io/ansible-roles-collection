# Role: Bash Secrets

Configures secrets to use in bash and to delegate to other tools. Should be stored in an Ansible Vault.

The role is intended to be used on all machines (RasPi and workstation).

## Expected Variables

| Variable                 | Description                                                                     |
|--------------------------|---------------------------------------------------------------------------------|
| `{{ ansible_user }}`     | The user to install and configure for (typically the logged-in user)            |
| `{{ SONARCLOUD_TOKEN }}` | A token (best from an Ansible Vault) to use interact with SonarCloud MCP Server |
