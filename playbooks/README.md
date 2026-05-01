# Playbooks

Standalone playbook reference.

## Summary

- `apt_packages_base.yml`: installs shared APT packages and prepares `~/.local/bin`, including an `fd` symlink for Debian's `fdfind`
- `apt_packages_desktop.yml`: installs desktop-only APT packages for input methods and clipboard support, plus Slack from Slack's official `.deb` package
- `azure_cli.yml`: adds Microsoft's APT repository and installs Azure CLI
- `bun.yml`: installs Bun, updates `~/.zshrc`, and installs configured global Bun packages
- `fcitx5.yml`: configures `fcitx5` and its Japanese input profile in the user's home directory
- `idle.yml`: sets the GNOME idle timeout for the current user session
- `nvm.yml`: installs `nvm`, updates `~/.zshrc`, and installs the current Node.js LTS
- `pixi.yml`: installs Pixi and adds it to `PATH`
- `rust.yml`: installs Rust with `rustup`, adds Cargo to `PATH`, and installs configured Cargo tools
- `snap_packages.yml`: installs the configured Snap packages
- `uv.yml`: installs `uv` and adds `~/.local/bin` to `PATH`
- `xkb.yml`: writes keyboard defaults and applies keyboard settings to console and desktop session state
- `zsh.yml`: ensures `zsh` is installed and sets it as the default shell
