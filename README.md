# ubuntu-ansible

Ansible playbooks for provisioning a local Ubuntu workstation.

This repository targets `localhost` and is intended for setting up a machine with baseline packages, `zsh`, Node.js via `nvm`, Bun, Pixi, selected Snap packages, and a GNOME idle timeout.

## Structure

- `ansible.cfg`: points Ansible at the local inventory.
- `inventory/hosts`: defines `localhost` with `ansible_connection=local`.
- `site.yml`: master playbook that runs the full setup in the expected order.
- `playbooks/`: standalone playbooks for individual setup tasks.

## Prerequisites

- Ubuntu with Ansible installed
- `sudo` access for playbooks that use `become: true`
- Internet access for installer-based playbooks such as `nvm`, Bun, and Pixi
- The `community.general` collection for the Snap playbook

## Usage

Run the full setup:

```bash
ansible-playbook site.yml
```

Run a single playbook:

```bash
ansible-playbook playbooks/nvm.yml
```

## Playbook Order

`site.yml` imports playbooks in this order:

1. `playbooks/apt_packages.yml`
2. `playbooks/zsh.yml`
3. `playbooks/nvm.yml`
4. `playbooks/bun.yml`
5. `playbooks/pixi.yml`
6. `playbooks/snap_packages.yml`
7. `playbooks/idle.yml`

This ordering matters because `playbooks/bun.yml` expects Node.js to already be available through `nvm`.

## Playbook Summary

- `playbooks/apt_packages.yml`: installs baseline APT packages such as `curl`, `git`, `tmux`, `vim`, and `zsh`
- `playbooks/zsh.yml`: ensures `zsh` is installed and sets it as the default shell
- `playbooks/nvm.yml`: installs `nvm`, updates `~/.zshrc`, and installs the current Node LTS
- `playbooks/bun.yml`: installs Bun, updates `~/.zshrc`, and installs global Bun packages
- `playbooks/pixi.yml`: installs Pixi and adds it to `PATH`
- `playbooks/snap_packages.yml`: installs configured Snap packages
- `playbooks/idle.yml`: sets the GNOME idle timeout
