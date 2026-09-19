# Omarchy Workstation Role Compatibility Matrix

This matrix tracks, for every role (and every bundled tool inside the `packages`/`virtualization` roles), whether it is supported on Ubuntu/Debian, whether it is supported on Arch Linux, whether the tool ships preinstalled on [Omarchy](https://github.com/basecamp/omarchy) (an Arch/Hyprland desktop distribution), and whether it is part of the default Omarchy workstation profile (see [`group_vars/workstation-profile-omarchy.yml`](../group_vars/workstation-profile-omarchy.yml)).

Arch support only ever uses `community.general.pacman` against the official Arch repositories. Tools that exist only in the AUR are never installed automatically by these roles - see the "AUR-only / excluded" notes below. The deliberate exceptions are `vscode` and `virtualization` (Vagrant) (see their rows and notes below).

Omarchy's default package set was read from [`basecamp/omarchy` `install/omarchy-base.packages`](https://github.com/basecamp/omarchy/blob/master/install/omarchy-base.packages).

## Matrix

| Role                             | Tool / `tasks_from`        | Ubuntu | Arch | Preinstalled on Omarchy | Needed on Omarchy | Compatible w/ Omarchy | Decision |
|-----------------------------------|-----------------------------|--------|------|--------------------------|--------------------|-------------------------|----------|
| `ansible`                         | Ansible                    | yes    | yes  | no                       | yes                | yes                     | include  |
| `atuin`                           | Atuin                      | yes    | yes  | no                       | yes                | yes                     | include  |
| `bash`                            | Bash config                | yes    | yes  | n/a (base OS)            | yes                | yes                     | include  |
| `clamav`                          | ClamAV                     | yes    | yes  | no                       | yes                | yes                     | include  |
| `claude`                          | Claude Code CLI            | yes    | yes  | **yes**                  | no                 | yes                     | include (dedicated role kept) |
| `conky`                           | Conky                      | yes    | yes  | no                       | yes                | yes                     | include  |
| `cron`                            | cron / cronie              | yes    | yes  | no                       | yes                | yes                     | include  |
| `docker`                          | Docker                     | yes    | yes  | **yes**                  | no                 | yes                     | include (dedicated role kept) |
| `filesystem`                      | Timezone, dirs, wallpapers | yes    | yes  | n/a                      | yes                | yes                     | include  |
| `git`                             | Git                        | yes    | yes  | likely (base-devel dep)  | yes                | yes                     | include  |
| `git-repositories`                | Git repo cloning           | yes    | yes  | n/a                      | yes                | yes                     | include  |
| `github-cli`                      | GitHub CLI                 | yes    | yes  | **yes**                  | no                 | yes                     | include (dedicated role kept) |
| `hardening`                       | login.defs UMASK           | yes    | yes  | n/a                      | yes                | yes                     | include  |
| `helm`                            | Helm                       | yes    | yes  | no                       | yes                | yes                     | include  |
| `kubectl`                         | kubectl                    | yes    | yes  | no                       | yes                | yes                     | include  |
| `minikube`                        | Minikube                   | no (disabled) | no (disabled) | no | unknown            | n/a                     | exclude (pre-existing tech debt, out of scope) |
| `node`                            | Node.js                    | yes    | yes  | no                       | yes                | yes                     | include  |
| `ollama`                          | Ollama                     | yes    | yes  | no                       | yes                | yes                     | include  |
| `packages` (`main`)               | Base CLI packages          | yes    | yes  | partial overlap          | yes                | yes                     | include  |
| `packages` (`desktop` - basics)   | GNOME Tweaks, Dell monitor ctl | yes | partial | no                    | yes                | partial                 | include (Tilix/rar/7z/Dell-monitor Arch pkgs still open TODO, pre-existing) |
| `packages` (`desktop` - dev-tools)| FileZilla, rpi-imager      | yes    | yes  | no                       | yes                | yes                     | include  |
| `packages` (`desktop` - dev-tools)| Postman                    | **removed** | **removed** | no          | no                 | no (AUR-only)           | removed entirely - never installed, purged on Ubuntu |
| `packages` (`desktop` - sublime)  | Sublime Text               | yes    | yes  | no                       | yes                | yes                     | include  |
| `packages` (`desktop` - chrome)   | Chromium                   | yes    | yes  | **yes**                  | yes                | yes                     | include (dedicated tool kept, same treatment as Docker/GitHub CLI/Claude Code) |
| `packages` (`desktop` - chrome)   | Google Chrome (real)       | **removed** | **removed** | no          | no                 | n/a                     | removed entirely - never installed, purged on Ubuntu |
| `packages` (`desktop` - media)    | VLC, ffmpeg, flac, wavpack, vorbis-tools, brasero, libdvdcss | yes | yes | no | yes | yes | include |
| `packages` (`desktop` - media)    | Spotify                    | yes (snap) | yes (Omarchy only) | **yes** (Omarchy's own repo) | yes | yes (on Omarchy) | include (dedicated tool kept, same treatment as Docker/GitHub CLI/Claude Code); no-ops/fails on vanilla Arch (no package there), so skipped in test/CI like the Ubuntu snap install |
| `packages` (`desktop` - media)    | asunder                    | **removed** | **removed** | no        | no                 | no (AUR-only)           | removed entirely - never installed, purged on Ubuntu |
| `packages` (`desktop` - uninstall)| Remove preinstalled bloat  | yes    | yes  | n/a                      | yes                | yes                     | include  |
| `taskfile-dev`                    | Task (go-task)             | yes    | yes  | no                       | yes                | yes                     | include  |
| `virtualization` (VirtualBox)     | VirtualBox                 | yes    | yes  | no                       | yes                | yes                     | include  |
| `virtualization` (Vagrant)        | Vagrant                    | yes    | yes (AUR via yay) | no             | yes                | yes (needs yay)         | include (AUR exception, documented in role README) |
| `vscode`                          | VS Code                    | yes (MS build, snap) | yes (MS build, AUR via yay) | no | yes | yes (needs yay)         | include (AUR exception, documented in role README) |

## Notes

- **`minikube`** is fully commented out today for both operating systems (pre-existing state, not a regression introduced here). It is left untouched and flagged here as backlog rather than silently implemented as part of this change.
- **Removed entirely** (not just "excluded from Arch" - never installed on any OS, and actively uninstalled/purged where they previously were): Postman, `asunder`, IntelliJ IDEA (Ultimate/Community), Google Chrome. See `packages/tasks/desktop/90-uninstall.yml` for the Ubuntu purge tasks.
- **`vscode` is a deliberate exception** to the no-AUR-helper policy: `visual-studio-code-bin` (the real Microsoft build) is AUR-only, required to pass the Dev Containers extension's build check and use the Microsoft Marketplace. The role uninstalls Code - OSS (official repos, `pacman`) first, then installs `visual-studio-code-bin` via `yay`, which Omarchy ships by default. The install step is skipped in test/CI (`is_test`) the same way other Omarchy-only installs are, since `yay` is not available there.
- **`virtualization` (Vagrant) is a second deliberate exception** to the no-AUR-helper policy: Vagrant has no official Arch package and is AUR-only, so it is installed via `yay`, which Omarchy ships by default. Like `vscode`, the role temporarily grants `default_user` passwordless `sudo` for `pacman` while `yay` runs (needed because `yay` internally shells out to `sudo pacman -U ...`, and that nested `sudo` call has no way to prompt for a password under Ansible), then revokes it again regardless of outcome. VirtualBox and Vagrant are entirely excluded from the Docker-based test setup (see `tests/compose/README.md`), so this install path is not exercised in CI.
- **"Dedicated tool kept" tools** (Docker, GitHub CLI, Claude Code, Chromium, Spotify): Omarchy preinstalls these already, but ansible still ensures they are installed rather than skipping them - they stay idempotent (`pacman`/script installs no-op or upgrade when already present) and remain usable standalone on non-Omarchy Arch or Ubuntu systems. Spotify is the one exception that only actually resolves on Omarchy itself (via its own package repository); on vanilla Arch there is no package for it at all, so the task is skipped in test/CI the same way the Ubuntu snap install already is.
