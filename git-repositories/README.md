# Role: Git Repositories

Clone Git repositories.

This role is intended to be used on all machines (RasPi and workstation).

## Expected Variables

| Variable             | Description                                                           |
|----------------------|-----------------------------------------------------------------------|
| `{{ ansible_user }}` | The user to install and configure for (typically the logged-in user). |
| `{{ repositories }}` | A list of Git repositories to clone. Each entry should be a dictionary with keys `dest` (the directory to where the repository should be cloned), `org`, and `repo`. |

## Optional Variables

The following variables are optional and have default values:

| Variab      le                    | Description                                    | Default          |
|-----------------------------------|------------------------------------------------|------------------|
| `{{ git_repositories_ssh_base }}` | SSH Base-URL to clone repositories from GitHub | `git@github.com` |
