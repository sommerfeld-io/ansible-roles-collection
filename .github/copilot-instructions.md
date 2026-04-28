# GitHub Copilot Instructions

This is `ansible-roles-collection` by sommerfeld-io - a set of 26+ reusable Ansible roles for configuring Linux systems (Ubuntu/Debian and Arch Linux). See [README.md](../README.md) for the full project overview.

## Commit Messages: Conventional Commits

Always use [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/#summary) for every commit message.

**Format:** `<type>(optional scope): <description>`

| Type | Effect | When to use |
|------|--------|-------------|
| `fix` | PATCH release | Patches a bug |
| `feat` | MINOR release | Introduces a new feature |
| `BREAKING CHANGE` footer | MAJOR release | Introduces a breaking API change |
| `build`, `chore`, `ci`, `docs`, `style`, `refactor`, `perf`, `test` | No release | All other changes |

**Rules:**
- A scope may be added in parentheses for extra context: `feat(parser): add ability to parse arrays`. A scope may not be with a slash (`/`).
- Breaking changes must include `BREAKING CHANGE:` in the footer: `feat: drop support for Node 6`
- Commit message titles must also match the project pattern: `^(fix|feat|build|chore|ci|docs|style|refactor|perf|test)/[a-z0-9._-]+$`

Write commit messages using the Conventional Commits format, ensuring the header (`type(scope): summary`) is clear and descriptive, as it will be displayed on GitHub release pages and used for changelogs. Focus the header on user-visible, meaningful change descriptions and avoid vague wording. Always document breaking changes explicitly in the footer using `BREAKING CHANGE:` (do not use the `!` notation).

## Project Overview

This is the **`sommerfeld-io` organization's `.github` meta-repository**. It serves as:
- Fallback community health files (PR templates, issue templates, code of conduct) for all org repos without their own `.github/` directory
- Reusable GitHub Actions workflow library referenced by other repositories
- Conventions and standards for all `sommerfeld-io` projects

Key docs: [Development Guide](../docs/development-guide.md) | [Contributing](../CONTRIBUTING.md)

## Commands

```bash
task lint          # Run all linters (YAML, Ansible, filenames, markdown-links) via Docker
task test          # Full test: Docker Compose (multi-version Ubuntu) + Ansible provisioning
task cleanup       # Remove .cache, node_modules, .ansible, Docker artifacts
```

### Before committing

- Always run `task test` to validate all roles and playbooks in the Docker-based test environment. This is required for all changes except those that affect only documentation files (e.g., README.md or other Markdown files or inline comments).
- Linters and other checks/validations are run automatically from pre-commit hooks, so you do not need to run them manually.
- You may skip `task test` only for pure documentation changes.

## Role Structure

Every role follows this layout:

```
role-name/
├── README.md             # Documents variables and usage
├── defaults/main.yml     # Default variables (always role-prefixed)
├── handlers/main.yml     # Handlers (optional)
├── tasks/
│   ├── main.yml          # Imports numbered subtasks
│   └── NN-name.yml       # Numbered subtasks: 10-, 20-, 90- (uninstall)
├── templates/            # Jinja2 templates
└── files/                # Static files
```

Some roles expose multiple entry points via `tasks_from` (e.g. `packages` has `main`, `desktop`, `raspi`; `clamav` has `install`, `scan`).

## Conventions

**Variable naming** (enforced by ansible-lint): `^[a-z_][a-z0-9_]*$`
Always prefix variables with the role name: `docker_version`, `packages_ctop_version`, `helm_architecture`.

**Task naming**:
```yaml
- name: Role Name  ----  Section  ----  Action
```

**OS detection** - always use `when:` with `ansible_os_family`, never hardcode paths:
```yaml
when: ansible_facts['os_family'] == "Debian"
when: ansible_facts['os_family'] == "Archlinux"
```

**Privilege model**: root tasks use `become: true`; user-space tasks use `become: false`. The test playbook runs these in separate phases - see [tests/ansible-playbook.yml](../tests/ansible-playbook.yml).

**State reporting**: Use `changed_when: false` on informational/read-only tasks. Handlers only fire when a task reports `changed`.

**Task imports**: prefer `ansible.builtin.import_tasks` for static imports; use `include_role` with `tasks_from` for multi-entry-point roles.

## Testing

Tests use Docker Compose and Taskfile for orchestration. See [tests/README.md](../tests/README.md) for the full Docker-based test setup and instructions.

## Ansible Lint

Config in [.ansible-lint.yml](../.ansible-lint.yml): profile `basic`, offline mode, `yaml[line-length]` skipped.

## CI/CD

Pipeline at [.github/workflows/pipeline.yml](./workflows/pipeline.yml): ShellCheck → lint matrix → semantic-release on `main`.
