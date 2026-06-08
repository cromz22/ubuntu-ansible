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
    - OpenSSH server enabled and running with key-only authentication, with UFW allowing SSH only from private local networks (`ssh_server.yml`)
    - [Zsh](https://www.zsh.org/) as the default shell (`zsh.yml`)
    - [nvm](https://github.com/nvm-sh/nvm) as the node version manager (`nvm.yml`) and [Bun](https://bun.sh/) as the JavaScript package manager (`bun.yml`)
        - `nvm.yml` should run before `bun.yml` because bun requires Node.js.
        - OpenAI [Codex CLI](https://developers.openai.com/codex/cli) is installed via bun.
    - OpenAI Codex CLI setup checks and Linux sandbox prerequisites (`codex.yml`)
    - [Rust](https://rust-lang.org/) language and some useful Rust-made CLI tools (`rust.yml`)
    - [Pixi](https://pixi.prefix.dev/latest/) for package management of projects that mainly use Python (`pixi.yml`)
    - [uv](https://docs.astral.sh/uv/) for package management of projects that use only Python (`uv.yml`)

- Desktop (`desktop.yml`)
    - Additional desktop packages via apt (`apt_packages_desktop.yml`)
        - [WezTerm](https://wezterm.org/) is installed from WezTerm's official APT repository.
        - [Slack](https://slack.com/) is installed from Slack's official `.deb` package.
        - [Zoom](https://zoom.us/) is installed from Zoom's official latest `.deb` package.
    - Japanese keyboard layout and physical key mapping via XKB, including Ctrl and Caps Lock swapped (`xkb.yml`)
    - Japanese IME setup via fcitx5, using 半角/全角 to toggle IME, 変換 for IME on, and 無変換 for IME off (`fcitx5.yml`)
    - [Snap](https://snapcraft.io/) to manage desktop apps (`snap_packages.yml`)
        - [RCLONE](https://rclone.org/) is installed via snap.
    - GNOME idle timeout (`idle.yml`)

- Standalone playbooks
    - Below are the playbooks that are not imported in `desktop.yml` nor `server.yml` because of its specific nature.
        - `azure_cli.yml`
        - `docker.yml`
        - `mount_disk.yml`
        - `nvidia_driver.yml`

## Usage

### Basic server setup

```bash
ansible-playbook server.yml -K
```

### Basic desktop setup

```bash
ansible-playbook desktop.yml -K
```

### Installing networking packages (Optional)

```bash
ansible-playbook playbooks/apt_packages_network.yml -K
```

### GPU workstation setup

- GPU hardware and NVIDIA driver requirements vary by machine, so the driver installation should be handled with care. CUDA and cuDNN are intentionally left out the scope of this repository, so that the users can choose and manage the versions they need flexibly, e.g. with Pixi.

- NVIDIA driver installation steps

    1. Find out the suitable nvidia driver version for your device through manual search: https://www.nvidia.com/en-us/drivers/
    1. Edit driver version in `nvidia_driver.yml`
    1. Switch to multi-user target to disable GUI:

        ```
        sudo systemctl isolate multi-user.target
        ```

    1. Run `nvidia_driver.yml`

        ```
        ansible-playbook playbooks/nvidia_driver.yml -K
        ```

    The playbook installs the driver from NVIDIA's official runfile and marks installed apt-managed NVIDIA driver packages as held, so `apt upgrade` does not replace driver components managed by the runfile. NVIDIA Container Toolkit packages are not held.

    After running the playbook, you can confirm the held packages:

        ```
        apt-mark showhold
        ```

    You can then run normal apt maintenance:

        ```
        sudo apt update
        sudo apt upgrade
        ```

    Before confirming an upgrade, check that apt is not proposing to install or upgrade Ubuntu's `nvidia-driver-*`, `nvidia-dkms-*`, `nvidia-kernel-*`, `nvidia-firmware-*`, or `libnvidia-*` driver packages.

- Additionally, if you would like to use docker with GPU, install docker and set it up for GPU.

    - Install docker (This is not related to GPU, so you can run this alone if you simply want to use docker):

        ```
        ansible-playbook playbooks/docker.yml -K
        ```

    - After Docker setup, log out and back in, or start a new login shell (`exec -l zsh`), before running `docker` without `sudo`.

    - Set it up for GPU:

        ```
        ansible-playbook playbooks/nvidia_docker.yml -K
        ```

### Azure CLI setup (Run on local)

```bash
ansible-playbook playbooks/azure_cli.yml -K
```

### Azure disk setup (Run on remote)

```bash
ansible-playbook playbooks/mount_disk.yml -K
```

### Notes

For any playbook that uses `become: true`, run Ansible as the normal user and let Ansible prompt for sudo with `-K` when needed.
Do not run `sudo ansible-playbook ...` for the full desktop setup in `desktop.yml` or for playbooks that also manage user-session state.
If you do so, that will change the settings for the root user, not the normal user.
