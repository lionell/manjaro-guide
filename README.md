# Manjaro installation guide

Notes to reproduce my [Manjaro](https://manjaro.org) setup.

![screenshot](https://raw.githubusercontent.com/lionell/manjaro-guide/master/2017-07-31-221323_2560x1440_scrot.png)

## Preinstall

1.  Do a **FULL BACKUP**.
1.  Download Manjaro i3 community edition from [here](https://manjaro.org/community-editions/).
1.  Write `.iso` image to the USB drive. Here is how to do it from Linux:
    1.  Find out the name of your USB drive with `lsblk`. If it's mounted run `umount /where/it/is/mounted`.
    1.  Run the following command, replacing `/dev/sdx` with your drive, e.g. `/dev/sdb/`. 
        (Do **not** apppend a partition number).
        ```shell
        sudo dd bs=4M if=/path/to/image.iso of=/dev/sdx status=progress && sync
        ```
    1.  Wait for `sync` to complete before unplugging USB drive.

## Install

1.  Select US-Colemak keyboard layout during installation.
1.  Partition table should look like this:

    | size | fs    | path      |
    |------|-------|-----------|
    | 500M | FAT32 | /boot/efi |
    | 20G  | SWAP  |           |
    | 100G | EXT4  | /         |
    | 350G | EXT4  | /home     |

## Setup

### Update kernel
1.  List all available kernels `mhwd-kernel -l`
    
    **ISSUE!** If you see `error: GPGME error: No data` try running `sudo pacman-key --init`

1.  Look for latest stable kernel and run `sudo mhwd-kernel -i linuxYY` to install.

Find more information [here](https://wiki.manjaro.org/index.php/Manjaro_Kernels).

### Update pacman mirrors

1.  To generate mirrorlist run `sudo pacman-mirrors -g`
1.  Sync pacman local database with new mirrorlist by running `sudo pacman -Syy`

Find more information [here](https://wiki.manjaro.org/index.php?title=Pacman-mirrors)

### Upgrade your system

Run `sudo pacman -Suy` to upgrade all installed software.

### Remap CTRL -> ALT -> WIN -> CTRL

1.  Open `/usr/share/X11/xkb/symbols/pc` for writing.
1.  Substitute `include "altwin(alt_meta)"` with `include "altwin(ctrl_alt_win)"`
1.  Open `~/.i3/config` for writing.
1.  Change `set $mod Mod4` to `set $mod Mod1`
1.  Restart the system `sudo reboot`

For more options see `/usr/share/X11/xkb/symbols/ctrl` and `/usr/share/X11/xkb/symbols/altwin`

### Tune power usage

1.  Install TLP `sudo pacman -S tlp`
1.  Start TLP `sudo tlp start`
1.  Mask unnecessarry services.
    ```shell
    sudo systemctl mask systemd-rfkill.service
    sudo systemctl mask systemd-rfkill.socket
    ```
1.  Install ThinkPad specific kernel modules.
    ```shell
    sudo pacman -S linuxYY-tp_smapi
    sudo pacman -S linuxYY-acpi_call
    ```
1.  Install PowerTOP `sudo pacman -S powertop`
1.  Calibrate PowerTOP `sudo powertop --calibrate`
1.  Navigate to "Tunables" tab and fix all the remaining issues by pressing enter staying on it.

Find more information about [TLP](https://wiki.archlinux.org/index.php/TLP) and [PowerTOP](https://wiki.archlinux.org/index.php/powertop).

### Install Google Chrome

Run `yaourt google-chrome` and select stable chanel.

### Setup git and generate new SSH keypair

1.  Run `sudo pacman -S git` to install.
1.  Generate new SSH keypair by running `ssh-keygen -t rsa -b 4096 -C 'xlionell@gmail.com'`
1.  Start SSH agent in background `eval $(ssh-agent -s)`
1.  Add key to SSH agent `ssh-add ~/.ssh/id_rsa`
1.  Install xclip `sudo pacman -S xclip`
1.  Copy public key to clipboard `xclip -sel clip < ~/.ssh/id_rsa.pub`
1.  Go to [github.com/settings/keys](https://github.com/settings/keys) and add new key.

### Pull .dotfiles from GitHub

1.  Install Apache Stow `sudo pacman -S stow`
1.  Pull repo from GitHub `git --recursive -j8 clone git@github.com:lionell/.dotfiles.git`

### Config terminal emulator

1.  Install Termite `sudo pacman -S termite`
1.  Install Adobe Source Code Pro font `sudo pacman -S adobe-source-code-pro-fonts`
1.  Use config from dotfiles `stow termite`

### Configure program launcher
1.  Install Rofi `sudo pacman -S rofi`
1.  Use config from dotfiles `stow xresources`

### Config git

Just run `stow git`

### Setup i3
1.  Install Font Awesome `yaourt ttf-font-awesome`
1.  Install sysstat for `cpu_usage` script `sudo pacman -S sysstat`
1.  Install i3blocks `sudo pacman -S i3blocks`
1.  Merge i3 configs.
    **NOTE!** This should be manual step as configs can differ from version to version.

### Setup neovim
1.  Install neovim `sudo pacman -S neovim`
1.  Install python2 bindings for nvim `sudo pacman -S python2-neovim`
1.  To link `vi` to `nvim` run `sudo ln -s /usr/bin/nvim /usr/bin/vi`
1.  Open neovim and run `:PlugInstall` to install plugins from config.

### Setup tmux
1.  Install tmux `sudo pacman -S tmux`
1.  Use config from dotfiles `stow tmux`
1.  Add new systemd module to automatically start tmux server
    ```
    // Put this in /etc/systemd/system/tmux.service
    [Unit]
    Description=Tmux server

    [Service]
    Type=forking
    User=lionell
    ExecStart=/usr/bin/tmux new -s 0 -d
    ExecStop=/usr/bin/tmux kill-server

    [Install]
    WantedBy=multi-user.target
    ```
1.  Enable module `sudo systemctl enable tmux`

### Config fish
1.  Install fish `sudo pacman -S fish`
1.  Change default shell to fish `chsh -s $(which fish)`. You will need to login again to see changes.
1.  Install Oh-My-Fish `curl -L https://get.oh-my.fish | fish`
1.  Install `pure` theme `omf install pure`

### Usefull plugins for fish
1.  Install z `omf install z`

### Remove redundant stuff

```shell
sudo pacman -R morc_menu
```
