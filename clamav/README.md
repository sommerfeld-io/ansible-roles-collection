# Role: ClamAV

This r

This role is intended to be used on all Ubuntu machines (RasPi and workstation).

## How to use

```yaml
## Install
- ansible.builtin.include_role:
    name: ../roles/ansible-roles-collection/clamav
    tasks_from: install

## Run a scan on demand
- ansible.builtin.include_role:
    name: ../roles/ansible-roles-collection/clamav
    tasks_from: scan
  vars:
    scan_path: /some/path/to/scan
```

> **:bulb: NOTE:** This role does not offer a `main.yml`, so you always have to specify a file.

## Expected Variables

| Variable                 | Description                                                          |
|--------------------------|----------------------------------------------------------------------|
| `{{ ansible_user }}`     | The user to install and configure for (typically the logged-in user) |
| `{{ scan_path }}`        | The path to scan with ClamAV                                         |

## Based on

We followed the description from <https://oneuptime.com/blog/post/2026-02-21-how-to-use-ansible-to-configure-clamav-antivirus/view>. But we stopped after installing ClamAV and Configuring `freshclam` (Signature Updates). We did not install any daemons.
