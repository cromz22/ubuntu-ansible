# ubuntu-ansible

Ansible playbooks for provisioning a local Ubuntu machine as either a desktop workstation or a server.

This repository targets `localhost`. `desktop.yml` sets up a graphical workstation with baseline packages, keyboard defaults, `fcitx5` Japanese input, `zsh`, Rust via `rustup`, Node.js via `nvm`, Bun, Pixi, `uv`, selected Snap packages, and a GNOME idle timeout. `server.yml` sets up a headless Ubuntu Server with the shared command-line tooling.

## Structure

- `ansible.cfg`: points Ansible at the local inventory.
- `inventory/hosts`: defines `localhost` with `ansible_connection=local`.
- `desktop.yml`: master playbook for Ubuntu Desktop.
- `server.yml`: master playbook for Ubuntu Server.
- `playbooks/`: standalone playbooks for individual setup tasks, documented in `playbooks/README.md`.

## Prerequisites

- Ubuntu with Ansible installed
- `sudo` access for playbooks that use `become: true`
- Internet access for external package repositories and installer-based playbooks such as the optional Azure CLI playbook, `nvm`, Bun, Pixi, and `uv`
- The `community.general` collection for the Snap playbook

## Assumptions

This repository is meant to be easy to modify, but the current defaults assume a particular kind of setup:

- The machine already has the latest stable Ubuntu installed
- The hostname and primary user account are already configured
- Ansible is already installed before running these playbooks, whether manually or through another bootstrap step such as cloud-init
- You are provisioning your own local Ubuntu machine, not a remote host
- You run Ansible as your normal user and use `sudo` when prompted
- You are happy to use `zsh` as your default shell. If not, adjust `playbooks/zsh.yml` and any playbooks that update `~/.zshrc`.
- On desktop systems, you use GNOME and want desktop settings applied in your logged-in session. If not, review `desktop.yml` and the related playbooks described in [playbooks/README.md](./playbooks/README.md).
- The desktop setup assumes a Japanese keyboard and input environment. If you want different keyboard or input defaults, change `playbooks/xkb.yml` and `playbooks/fcitx5.yml`.
- The developer tooling reflects the current defaults in this repository, including Rust, Node.js via `nvm`, Bun, Pixi, and `uv`. If you want a different toolset, change `server.yml` or the individual tool playbooks described in [playbooks/README.md](./playbooks/README.md).
- Some desktop apps are installed by default, currently `slack` and `rclone`. If you want different apps, change `playbooks/snap_packages.yml`.
- GPU workstation setup is intentionally out of scope. GPU hardware and NVIDIA driver requirements vary by machine, so driver installation should be handled separately. CUDA and cuDNN are also left out so users can choose and manage the versions they need more flexibly, for example with tools such as Pixi.

## Usage

Run the full setup:

```bash
ansible-playbook desktop.yml -K
```

Run the server setup:

```bash
ansible-playbook server.yml -K
```

Run a single playbook (e.g., Azure CLI):

```bash
ansible-playbook playbooks/azure_cli.yml -K
```

For any playbook that uses `become: true`, run Ansible as your normal user and let Ansible prompt for sudo with `-K` when needed.

Do not run `sudo ansible-playbook ...` for the full desktop setup in `desktop.yml` or for playbooks that also manage user-session state. In this repository that especially applies to `playbooks/xkb.yml`, `playbooks/fcitx5.yml`, and `playbooks/idle.yml`, which update the logged-in user's X11, DBus, or home-directory state and should execute in your user session rather than entirely as root.

## Included Playbooks

`server.yml` includes:

- `playbooks/apt_packages_base.yml`
- `playbooks/zsh.yml`
- `playbooks/rust.yml`
- `playbooks/nvm.yml`
- `playbooks/bun.yml`
- `playbooks/pixi.yml`
- `playbooks/uv.yml`

The main ordering constraint is that `playbooks/bun.yml` runs after `playbooks/nvm.yml` so Node.js is already available.

`desktop.yml` includes everything in `server.yml`, plus:

- `playbooks/apt_packages_desktop.yml`
- `playbooks/xkb.yml`
- `playbooks/fcitx5.yml`
- `playbooks/snap_packages.yml`
- `playbooks/idle.yml`

The following playbooks are only meant for standalone usage:

- `playbooks/azure_cli.yml`

Detailed descriptions for each standalone playbook are in [playbooks/README.md](./playbooks/README.md).
