# Role: Docker

With `tasks_from: main` The role installs and configures Docker, a popular containerization platform. This should be run as `root`.

With `tasks_from: scout` The role installs and configures Docker Scout, a tool for managing and securing container images. This should be run after Docker is installed and not as `root`.

The role is intended to be used on all machines (RasPi and workstation).

## Expected Variables

| Variable             | Description                                                          |
|----------------------|----------------------------------------------------------------------|
| `{{ ansible_user }}` | The user to install and configure for (typically the logged-in user) |

## See

- Install Docker on Arch Linux: <https://wiki.archlinux.org/title/Docker>
