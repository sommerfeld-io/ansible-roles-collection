# Role: ClamAV

The role installs and configures Ollama.

The role is intended to be used on all Ubuntu machines (RasPi and workstation). Run with `become: true`.

## How to use

```yaml
- ansible.builtin.include_role:
    name: ../roles/ansible-roles-collection/ollama
```

Verify the install:

- `ollama list`
- `ollama run <the-model-name>`

> **:zap: NOTE:** The role only installs or updates Ollama, when `/usr/local/bin/ollama` exists. When you want to update, remove the binary and run your playbook with this role again.

- Chec

## Expected Variables

| Variable                 | Description                                                          |
|--------------------------|----------------------------------------------------------------------|
| `{{ ansible_user }}`     | The user to install and configure for (typically the logged-in user) |

## Optional Variables

The following variables are optional and have default values:

| Variable              | Description                        | Default                             |
|-----------------------|------------------------------------|-------------------------------------|
| `{{ ollama_models }}` | List of language models to install | see [`main.yml`](defaults/main.yml) |
