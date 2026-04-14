# ubuntu-ansible

Ansible playbooks for provisioning a local Ubuntu workstation.

This repository targets `localhost` and is intended for setting up a machine with baseline packages, system keyboard defaults, `fcitx5` Japanese input, `zsh`, Rust via `rustup`, Node.js via `nvm`, Bun, Pixi, `uv`, selected Snap packages, and a GNOME idle timeout.

## Structure

- `ansible.cfg`: points Ansible at the local inventory.
- `inventory/hosts`: defines `localhost` with `ansible_connection=local`.
- `site.yml`: master playbook that runs the full setup in the expected order.
- `playbooks/`: standalone playbooks for individual setup tasks.

## Prerequisites

- Ubuntu with Ansible installed
- `sudo` access for playbooks that use `become: true`
- Internet access for installer-based playbooks such as `nvm`, Bun, Pixi, and `uv`
- The `community.general` collection for the Snap playbook

## Usage

Run the full setup:

```bash
ansible-playbook site.yml -K
```

Run a single playbook:

```bash
ansible-playbook playbooks/nvm.yml
```

For any playbook that uses `become: true`, run Ansible as your normal user and let Ansible prompt for sudo with `-K` when needed.

Do not run `sudo ansible-playbook ...` for the full setup in `site.yml` or for playbooks that also manage user-session state. In this repository that especially applies to `playbooks/xkb.yml`, `playbooks/fcitx5.yml`, and `playbooks/idle.yml`, which update the logged-in user's X11, DBus, or home-directory state and should execute in your user session rather than entirely as root.

## Playbook Order

`site.yml` imports playbooks in this order:

1. `playbooks/apt_packages.yml`
2. `playbooks/xkb.yml`
3. `playbooks/fcitx5.yml`
4. `playbooks/zsh.yml`
5. `playbooks/rust.yml`
6. `playbooks/nvm.yml`
7. `playbooks/bun.yml`
8. `playbooks/pixi.yml`
9. `playbooks/uv.yml`
10. `playbooks/snap_packages.yml`
11. `playbooks/idle.yml`

This ordering matters because `playbooks/bun.yml` expects Node.js to already be available through `nvm`.

## Playbook Summary

- `playbooks/apt_packages.yml`: installs baseline APT packages such as `build-essential`, `clang`, `curl`, `fcitx5`, `fd-find`, `git`, `ripgrep`, `tmux`, `vim`, and `zsh`, ensures `~/.local/bin` is on `PATH`, and exposes Debian's `fdfind` as `~/.local/bin/fd`
- `playbooks/xkb.yml`: writes `/etc/default/keyboard`, reapplies the system keyboard config, updates the current X11 session with `setxkbmap`, and syncs the GNOME user-level `xkb-options` override when present
- `playbooks/fcitx5.yml`: selects `fcitx5` with `im-config` and writes `fcitx5` hotkey/profile configuration for Japanese input in the logged-in user's home directory
- `playbooks/zsh.yml`: ensures `zsh` is installed and sets it as the default shell
- `playbooks/rust.yml`: installs Rust via `rustup`, adds Cargo to `PATH`, verifies `clang` is available for native Cargo builds, and installs `procs`, `git-delta`, `difftastic`, and `tree-sitter-cli` with `cargo install --locked`
- `playbooks/nvm.yml`: installs `nvm`, updates `~/.zshrc`, and installs the current Node LTS
- `playbooks/bun.yml`: installs Bun, updates `~/.zshrc`, and installs global Bun packages
- `playbooks/pixi.yml`: installs Pixi and adds it to `PATH`
- `playbooks/uv.yml`: installs `uv` via the official installer and adds `~/.local/bin` to `PATH`
- `playbooks/snap_packages.yml`: installs configured Snap packages
- `playbooks/idle.yml`: sets the GNOME idle timeout for the logged-in user session
