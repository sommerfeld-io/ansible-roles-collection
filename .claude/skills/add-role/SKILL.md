---
name: add-role
description: 'Scaffold a new Ansible role with the correct file layout following project conventions. Use when adding a new role to this collection. Takes a role name (required, lowercase, hyphen-separated) and an optional one-sentence description.'
argument-hint: 'role-name [description]'
---

# Add Role

Scaffold a new Ansible role in this repository following the project conventions defined in [.github/copilot-instructions.md](../../../.github/copilot-instructions.md).

## When to Use

- Adding a brand new role to this collection.

## Inputs

The user will provide (via argument or prompt):
- **Role name** (required): lowercase, hyphen-separated (e.g. `my-role`). This becomes the top-level directory name.
- **Description** (optional): one-sentence description of what the role does.
- **Has defaults** (optional): whether to create `defaults/main.yml` (default: yes if the role has configurable variables).
- **OS-specific** (optional): whether tasks need `when: ansible_os_family` branches.
- **Needs handler** (optional): whether to create `handlers/main.yml`.

If the role name is missing, ask the user for it before proceeding.

## Procedure

### 1. Create `<role-name>/tasks/main.yml`

Import numbered subtasks using `ansible.builtin.import_tasks`. Use this skeleton:
```yaml
---
- name: <Role Title>  ----  Install
  ansible.builtin.import_tasks: 10-install.yml

- name: <Role Title>  ----  Configure
  ansible.builtin.import_tasks: 20-configure.yml

- name: <Role Title>  ----  Uninstall
  ansible.builtin.import_tasks: 90-uninstall.yml
```

### 2. Create `<role-name>/tasks/10-install.yml`

Stub install tasks. If OS-specific, include both Debian and Archlinux blocks with `when:` guards:
```yaml
---
- name: <Role Title>  ----  Install  ----  Install package (Debian)
  ansible.builtin.apt:
    name: <package>
    state: present
  become: true
  when: ansible_facts['os_family'] == "Debian"

- name: <Role Title>  ----  Install  ----  Install package (Arch)
  community.general.pacman:
    name: <package>
    state: present
  become: true
  when: ansible_facts['os_family'] == "Archlinux"
```

### 3. Create `<role-name>/tasks/20-configure.yml`

Stub configure tasks (empty or minimal placeholder).

### 4. Create `<role-name>/tasks/90-uninstall.yml`

Stub uninstall tasks with `state: absent`.

### 5. Create `<role-name>/defaults/main.yml` (if has defaults)

```yaml
---
<role_name>_version: "1.0.0"
```
All variables **must** be prefixed with the role name (underscores, no hyphens): e.g. `my_role_version`.

### 6. Create `<role-name>/handlers/main.yml` (if needs handler)

```yaml
---
- name: Restart <service>
  ansible.builtin.service:
    name: <service>
    state: restarted
  become: true
```

### 7. Create `<role-name>/README.md`

Use this exact structure:
```markdown
# Role: <role-name>

<Description of what the role does and when to use it.>

## Expected Variables

The following variables are expected to be set globally or passed to the role:

| Variable             | Description                                                          |
|----------------------|----------------------------------------------------------------------|
| `{{ default_user }}` | The user to install and configure for (typically the logged-in user) |

## Optional Variables

The following variables are optional and have default values:

| Variable                          | Description              | Default  |
|-----------------------------------|--------------------------|----------|
| `{{ <role_name>_version }}`       | Version to install       | `1.0.0`  |
```

### 8. Follow these conventions throughout

- Task names: `Role Title  ----  Section  ----  Action`
- Variables: lowercase, underscore-separated, prefixed with role name. Never use hyphens.
- OS detection: always `when: ansible_facts['os_family'] == "Debian"` or `"Archlinux"`, never hardcode paths
- Root tasks: `become: true`; user-space tasks: `become: false`
- Read-only/informational tasks: add `changed_when: false`
- Use `ansible.builtin.import_tasks` for static subtask imports

### 9. Wrap up

1. Confirm all files were created.
2. Remind the user to:
   - Add the role to [tests/compose/ansible/playbook.yml](../../../tests/compose/ansible/playbook.yml) in the correct phase (root or user-space)
   - Run `task lint` to validate the new role files
