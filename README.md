# Dotfiles for [Aurora](https://getaurora.dev/)

## Live ISO / Installation

Set the keyboard layout to `ca-multix` (search for `French (CSA)`). This is
done via the installer UI. It must be done for the LUKS encryption passphrase
and my user password.  Otherwise, they won't match once I boot in the installed
system.

## Setup After Installation

1. Create and enter distrobox for various GUIs/CLIs.

   ```bash
   distrobox create --image ghcr.io/ublue-os/fedora-toolbox --name "Universal_Blue_Fedora_Toolbox" && distrobox enter Universal_Blue_Fedora_Toolbox
   ```

2. Setup 1Password GUI/CLI and Chromium

   1. Install 1Password GUI and CLI, then make them available to the host with `distrobox-export`.

      _1Password instructions taken from its [official website](https://support.1password.com/install-linux/).

      ```bash
      sudo rpm --import https://downloads.1password.com/linux/keys/1password.asc &&
        sudo sh -c 'echo -e "[1password]\nname=1Password Stable Channel\nbaseurl=https://downloads.1password.com/linux/rpm/stable/\$basearch\nenabled=1\ngpgcheck=1\nrepo_gpgcheck=1\ngpgkey=\"https://downloads.1password.com/linux/keys/1password.asc\"" > /etc/yum.repos.d/1password.repo'a &&
        sudo dnf install 1password 1password-cli &&
        distrobox-export --app 1password &&
        distrobox-export --bin $(which op) &&
        distrobox-export --bin /opt/1Password/op-ssh-sign
      ```

   2. Install Chromium, then make it available to the host with `distrobox-export`.

      ```bash
      sudo dnf install chromium &&
        distrobox-export --app chromium
      ```

   3. Add 1Password to autostart in KDE. Do not forget to add `--silent` to the
      arguments (just before `%U`) to start the GUI in the system tray.

   4. Add Chromium to autostart in KDE.

   5. Enable [1Password SSH agent](https://developer.1password.com/docs/ssh/get-started/#step-3-turn-on-the-1password-ssh-agent).

   6. [Sign Git commits with my SSH key](https://developer.1password.com/docs/ssh/git-commit-signing/).

   7. Configure the 1Password GUI to match the settings stored in my 1Password notes.

3. Open Firefox and connect to Firefox Sync. This restores my extensions and
   settings. The linkding and 1Password extensions must be configured. See my 1Password notes
   for 1Password's extension settings.

   **Note that the 1Password extension cannot be unlocked with the 1Password
   GUI from distrobox. This is an issue since Firefox is natively installed. This
   issue isn't present in distrobox' Chromium.**

4. Install Anytype.

   ```bash
   flatpak install io.anytype.anytype
   ```

5. Set zsh as the default shell for my user.

   ```bash
   sudo usermod --shell $(which zsh) $(whoami)
   ```

6. Setup chezmoi with my dotfiles repository.

   ```bash
   brew install chezmoi &&
     chezmoi init git@github.com:dmarcoux/dotfiles-aurora.git
   ```

7. Install CLI applications from Brewfile.

   ```bash
   brew bundle --file $(dirname "$(chezmoi source-path)")/Brewfile
   ```
