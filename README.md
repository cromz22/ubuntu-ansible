# ubuntu-ansible

Ansible playbooks for provisioning a local Ubuntu machine as either a desktop workstation or a server.

## Structure

- `ansible.cfg`: points Ansible at the local inventory.
- `inventory/hosts`: defines `localhost` with `ansible_connection=local`.
- `desktop.yml`: master playbook for Ubuntu Desktop.
- `server.yml`: master playbook for Ubuntu Server.
- `playbooks/`: standalone playbooks for individual setup tasks, documented in [playbooks/README.md](./playbooks/README.md).

## Prerequisites

- Ubuntu OS
- The hostname and primary user account are already configured
- Local machine setup, not a remote host
- `sudo` access (for playbooks that use `become: true`)
- Internet access (for external package repositories and installer-based playbooks)
- [Ansible](https://github.com/ansible/ansible) installed on the machine (Install before running the playbooks, e.g. manually via apt)

## What is configured / installed

The following configurations and installations are done. If they don't match your preference, change the corresponding yaml files.
Detailed descriptions of each standalone playbook are in [playbooks/README.md](./playbooks/README.md).

- Server and Desktop (`server.yml`)
    - Basic packages via apt (`apt_packages_base.yml`)
    - [Zsh](https://www.zsh.org/) as the default shell (`zsh.yml`)
    - [nvm](https://github.com/nvm-sh/nvm) as the node version manager (`nvm.yml`) and [Bun](https://bun.sh/) as the JavaScript package manager (`bun.yml`)
        - `nvm.yml` should run before `bun.yml` because bun requires Node.js.
        - OpenAI [Codex CLI](https://developers.openai.com/codex/cli) is installed via bun.
    - [Rust](https://rust-lang.org/) language and some useful Rust-made CLI tools (`rust.yml`)
    - [Pixi](https://pixi.prefix.dev/latest/) for package management of projects that mainly use Python (`pixi.yml`)
    - [uv](https://docs.astral.sh/uv/) for package management of projects that use only Python (`uv.yml`)

- Desktop (`desktop.yml`)
    - Additional desktop packages via apt (`apt_packages_base.yml`)
    - A Japanese keyboard and input environment, with preferences of Ctrl and Caps Lock keys swapped, and using 変換 for IME on and 無変換 for IME off (`xkb.yml` and `fcitx5.yml`)
    - [Snap](https://snapcraft.io/) to manage desktop apps (`snap_packages.yml`)
        - [Slack](https://slack.com/) and [RCLONE](https://rclone.org/) are installed via snap.
    - GNOME idle timeout (`idle.yml`)

- Standalone script
    - `azure_cli.yml` installs the [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/).

- GPU workstation setup is intentionally out of scope. GPU hardware and NVIDIA driver requirements vary by machine, so driver installation should be handled separately. CUDA and cuDNN are also left out so users can choose and manage the versions they need more flexibly, e.g. with Pixi.

## Usage

Run the server setup:

```bash
ansible-playbook server.yml -K
```

Run the desktop setup:

```bash
ansible-playbook desktop.yml -K
```

Run a single playbook (e.g., Azure CLI):

```bash
ansible-playbook playbooks/azure_cli.yml -K
```

For any playbook that uses `become: true`, run Ansible as your normal user and let Ansible prompt for sudo with `-K` when needed.
Do not run `sudo ansible-playbook ...` for the full desktop setup in `desktop.yml` or for playbooks that also manage user-session state (specifically `xkb.yml`, `fcitx5.yml`, and `idle.yml`).
If you do so, that will change the settings for the root user, not the normal user.
