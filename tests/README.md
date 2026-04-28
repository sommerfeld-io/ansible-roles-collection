# Tests Documentation

This directory contains test configurations and scripts for validating the Ansible roles in this collection. The recommended approach is to use Docker Compose to spin up test containers for different operating systems and run Ansible playbooks against them.

> **:bulb: NOTE:** This folder does NOT contain any ansible role code. Only test-related files and playbooks used to validate the roles.

## Docker-based Test Process with dedicated Dockerfiles

The official Ubuntu images from Docker Hub are minimal and do **not** include:

- An SSH server (required for Ansible to connect)
- Python (required for Ansible modules)
- A non-root user (for realistic user-space testing)
- Sudo configuration (for privilege escalation)

Each test container uses a dedicated Dockerfile (e.g. `Dockerfile.ubuntu-26.04`, `Dockerfile.ubuntu-25.10`) to:

- Install SSH, Python, sudo, netcat, and snapd (for snap package support)
- Create a real user (`testuser`) with a home directory and password
- Configure passwordless sudo for `testuser`
- Set up SSH for both root and testuser

This ensures your Ansible roles are tested in an environment that more closely matches real-world systems, and that all required dependencies for Ansible are present.

### Running the tests

```sh
# From the project root
task test
```

Multiple Ubuntu containers (e.g. 26.04, 25.10) are built and started as test targets.

The Ansible runner container waits for all targets to be healthy, then runs your playbook against them.

- Inventory
    - The inventory file is `hosts-docker.yml`.
    - Update this file to match your test targets if you add more containers.
- Playbook
    - The default playbook is `ansible-playbook.yml`.
    - Edit this file to specify which roles or tasks to test.
- Extending
    - To test other OS versions, add more services to `docker-compose.yml` and update the inventory accordingly.

### Limitations: Snap, VirtualBox, Vagrant

> **Note:** The following technologies have limitations in container-based CI environments.

- Snap packages are not tested
    - Snapd requires a full init system (systemd) to function properly.
    - Docker containers used for systems under test do not run systemd, so snapd and snap packages cannot be reliably tested.
    - All snap-related Ansible tasks are skipped during tests using the `is_test: true` variable in the test inventory.
    - No snap packages are installed or validated in CI tests.
- VirtualBox and Vagrant are not tested
    - VirtualBox and Vagrant require kernel modules and privileged access that are not available in Docker containers.
    - Any Ansible roles or tasks related to VirtualBox or Vagrant are not executed or validated in this test setup.
- Docker is tested
    - Docker-related roles and tasks are tested, as Docker can run inside Docker containers ("Docker-in-Docker") with the appropriate configuration.
    - This allows validation of Docker installation and configuration tasks within the test environment.

### Why Docker-based testing is preferred over Vagrant

While Docker-based testing has limitations (such as not supporting systemd, snap, or kernel modules), using Vagrant is not a better alternative for modern Ubuntu testing:

- **No official Ubuntu Vagrant boxes since 22.04:** Ubuntu no longer provides official Vagrant box images for releases after 22.04. Using community boxes don't seem to cover this gap. And updating through the releases from 22.04 until the target version is a fragile, error-prone approach.
- **Stable, reproducible environments:** Using Docker allows to use the exact Ubuntu version by specifying the correct Docker tag. This ensures a consistent and reproducible test environment.
- **Extra packages for realism:** We install additional packages (like SSH, sudo, cron, etc.) in the Docker images to make them more closely resemble a real Ubuntu system, while still benefiting from Docker's speed and reproducibility.
- **Vagrant cannot test VirtualBox or itself:** Even with Vagrant, you cannot reliably test VirtualBox or Vagrant roles.
- **Better CI integration:** Docker works seamlessly in GitHub actions, while Vagrant inside  GitHub Actions is not working well. So Vagrantboxes too have limitations.

**Summary:** Docker-based testing is more stable, reproducible, and CI-friendly approach for Ansible role testing, even if it requires some extra setup and cannot cover every possible scenario.

## Testing different Ansible versions

This setup also functions as a safe testing ground for newer Ansible versions. Simply change the `ansible-runner` image in `docker-compose.yml` to a newer tag to validate  roles and playbooks before upgrading in production.

## Adding a new System under Test for a new operating system

To add a new System under Test, update all of the following:

- **Create a Dockerfile for the new version**
    - Copy an existing Dockerfile (e.g., `Dockerfile.ubuntu-26.04`) and adapt it for the new version.
    - Name it `Dockerfile.ubuntu-XX.XX` (e.g., `Dockerfile.ubuntu-27.04`) or some name that reflects the operating system.
- **Update `docker-compose.yml`**
    - Add a new service for the new Ubuntu version and a corresponding  linter service for the Dockerfile.
    - In the `ansible-runner` service, add a new `depends_on` entry for the new System under Test with `condition: service_healthy`.
- **Update the Ansible inventory**  `ansible/hosts.yml` and add a new host entry.

This ensures your new Ubuntu test target is fully integrated into the Docker-based test workflow.
