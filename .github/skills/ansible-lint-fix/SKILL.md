---
name: ansible-lint-fix
description: 'Run ansible-lint via task lint and systematically fix violations. Use when: fixing lint errors, resolving ansible-lint warnings, fixing var naming, task naming, when conditions, changed_when, or become issues in Ansible roles.'
argument-hint: "[role-name or file to fix, or leave blank for all]"
---

# Ansible Lint Fix

Fix ansible-lint violations in this repository by running the linter and systematically applying corrections.

## When to Use

- After writing or modifying an Ansible role and `task lint` is failing
- When CI reports ansible-lint errors in the pipeline
- When adding a new role and wanting to validate it before committing
- To clean up var naming, task naming, OS detection, or state-reporting issues

## Procedure

### Step 1 - Run the Linter

Run ansible-lint via the project's lint task (which uses Docker):

```bash
docker compose up lint-ansible --exit-code-from lint-ansible 2>&1
```

> Do NOT run `ansible-lint` directly - the project uses `pipelinecomponents/ansible-lint:0.79.11` via Docker Compose with config at [.ansible-lint.yml](../../.ansible-lint.yml). Running outside Docker may miss the correct version or config.

If a specific role or file was specified, scan that path first. Otherwise scan all roles.

### Step 2 - Parse the Output

Ansible-lint output lines follow the pattern:

```
path/to/file.yml:LINE:COL: RULE-ID rule description
```

Collect all violations and group them by rule ID. Common rule IDs in this project:

| Rule ID | Description | Fix |
|---------|-------------|-----|
| `var-naming` | Variable doesn't match `^[a-z_][a-z0-9_]*$` | Rename to `role_name_varname` |
| `name[missing]` | Task is missing a `name:` field | Add `name: Role  ----  Section  ----  Action` |
| `name[casing]` | Task name doesn't match conventions | Rename to `Role  ----  Section  ----  Action` |
| `no-changed-when` | Command task missing `changed_when` | Add `changed_when: false` or appropriate expression |
| `risky-become-sudo` / `become` | Privilege escalation issue | Ensure `become: true` is explicit; avoid `sudo:` shorthand |
| `fqcn[action-core]` | Module name not fully qualified | Prefix with `ansible.builtin.` |
| `fqcn[action]` | Collection module not fully qualified | Prefix with `community.general.` etc. |
| `yaml[truthy]` | Use `true`/`false` not `yes`/`no` | Replace `yes`/`no` with `true`/`false` |
| `key-order` | Wrong key ordering in task | Reorder: `name`, `module`, `args`, `become`, `when`, `register`, `changed_when` |

### Step 3 - Apply Fixes Systematically

Fix violations in this priority order:

1. **Variable naming** (`var-naming`):
   - Variables must be lowercase, underscore-separated, prefixed with role name
   - Pattern: `rolename_varname` (no hyphens, no uppercase)
   - Update both `defaults/main.yml` and all task files that reference the variable
   - Update the role's `README.md` variable table to match

2. **Task naming** (`name[missing]`, `name[casing]`):
   - Format: `Role Name  ----  Section  ----  Action` (two ` ----  ` separators)
   - Role Name = human-readable role title (e.g. `Docker Setup`, `Bash Config`)
   - Section = logical group (e.g. `Install`, `Configure`, `Uninstall`)
   - Action = what the task does (e.g. `Add apt repository`, `Copy config file`)

3. **OS detection** - never hardcode distro paths; use `when:` guards:
   ```yaml
   when: ansible_os_family == "Debian"
   when: ansible_os_family == "Archlinux"
   ```

4. **Module FQCN** (`fqcn[action-core]`, `fqcn[action]`):
   - `apt:` → `ansible.builtin.apt:`
   - `copy:` → `ansible.builtin.copy:`
   - `pacman:` → `community.general.pacman:`

5. **State reporting** (`no-changed-when`):
   - Add `changed_when: false` to read-only / informational tasks
   - Add `changed_when: <result>.rc != 0` or similar for commands that do real work

6. **YAML booleans** (`yaml[truthy]`):
   - Replace `yes`/`no` with `true`/`false` throughout

### Step 4 - Re-run and Confirm

After applying fixes, run the linter again:

```bash
docker compose up lint-ansible --exit-code-from lint-ansible 2>&1
```

Repeat until output shows no violations (exit code 0). Also run the full lint suite to catch YAML and filename issues:

```bash
task lint
```

### Step 5 - Report

Summarize what was fixed:
- Files modified
- Rule IDs resolved
- Any remaining warnings (not errors) that are acceptable per [.ansible-lint.yml](../../.ansible-lint.yml) `warn_list`
