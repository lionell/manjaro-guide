# Manjaro installation guide
Notes to reproduce my [Manjaro](https://manjaro.org) setup.

## Preinstall

1.  Do a FULL BACKUP.
1.  Download Manjaro i3 community edition from [here](https://manjaro.org/community-editions/).
1.  Write `.iso` image to the USB drive. Here is how to do it from Linux:
    1.  Find out the name of your USB drive with `lsblk`. If it's mounted run `umount /where/it/is/mounted`.
    1.  Run the following command, replacing `/dev/sdx` with your drive, e.g. `/dev/sdb/`. (Do **not** apppend a partition number).
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
1.  List all available kernels `mhwd-kernel -li`
    
    **ISSUE!** If you are seeing `error: GPGME error: No data` try running `sudo pacman-key --init`.