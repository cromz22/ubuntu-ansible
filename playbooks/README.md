# Playbooks

Standalone playbook reference.

## Summary

- `apt_packages_base.yml`: installs shared APT packages and prepares `~/.local/bin`, including an `fd` symlink for Debian's `fdfind`
- `apt_packages_desktop.yml`: installs desktop-only APT packages for input methods, clipboard support, and WezTerm from WezTerm's official APT repository, plus Slack and Zoom from their official `.deb` packages
- `apt_packages_network.yml`: installs common networking and diagnostic APT packages
- `azure_cli.yml`: adds Microsoft's APT repository and installs Azure CLI
- `bun.yml`: installs Bun, updates `~/.zshrc`, and installs configured global Bun packages
- `claude.yml`: installs Claude Code CLI via the native installer and installs the `bubblewrap` and `socat` sandbox packages; rerunning this playbook does not upgrade Claude
- `codex.yml`: checks that the Codex CLI is available and installs Linux sandbox prerequisites, including `bubblewrap`; on Ubuntu 24.04 it loads the `bwrap` AppArmor profile when available, otherwise it applies the Codex-documented user namespace sysctl fallback
- `docker.yml`: adds Docker's official APT repository, installs Docker Engine with Buildx and Compose plugins, starts the Docker service, and adds the invoking user to the `docker` group
- `fcitx5.yml`: configures the fcitx5 IME layer, including the Japanese input profile, autostart, and IME hotkeys in the user's home directory
- `idle.yml`: sets the GNOME idle timeout for the current user session
- `mount_disk.yml`: prepares a selected disk with a partition and filesystem, mounts it, and persists it in `/etc/fstab`
- `nvidia_driver.yml`: installs the configured NVIDIA driver from the official runfile with DKMS support, and holds installed apt-managed NVIDIA driver packages so apt upgrades do not replace runfile-managed driver components
- `nvidia_docker.yml`: installs NVIDIA Container Toolkit and configures Docker to run GPU containers
- `nvm.yml`: installs `nvm`, updates `~/.zshrc`, and installs the current Node.js LTS
- `pixi.yml`: installs Pixi and adds it to `PATH`
- `rust.yml`: installs Rust with `rustup`, adds Cargo to `PATH`, and installs configured Cargo tools
- `snap_packages.yml`: installs the configured Snap packages
- `ssh_server.yml`: installs OpenSSH server, requires key-based authentication, enables the `ssh` service, and configures UFW to allow SSH only from private local networks
- `uv.yml`: installs `uv` and adds `~/.local/bin` to `PATH`
- `xkb.yml`: configures the XKB keyboard layer, including the Japanese keyboard layout and Ctrl/Caps Lock swap for console and desktop session state
- `zsh.yml`: ensures `zsh` is installed and sets it as the default shell

## Sudo prompt

The following playbooks use `become: true` and should be used with `-K`.

- Needs `-K`: `apt_packages_base.yml`, `apt_packages_desktop.yml`, `apt_packages_network.yml`, `azure_cli.yml`, `claude.yml`, `codex.yml`, `docker.yml`, `fcitx5.yml`, `idle.yml`, `mount_disk.yml`, `nvidia_docker.yml`, `nvidia_driver.yml`, `snap_packages.yml`, `ssh_server.yml`, `xkb.yml`, `zsh.yml`
- Does not need `-K`: `bun.yml`, `nvm.yml`, `pixi.yml`, `rust.yml`, `uv.yml`

