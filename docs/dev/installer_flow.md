# Installer flow documentation

This document provides a detailed overview of the installer flow for the HoloISO operating system. It covers each step from the initial boot of the installation media to the completion of the installation process, including partitioning, package selection, and configuration.

## Table of Contents

- [Installer flow documentation](#installer-flow-documentation)
  - [Table of Contents](#table-of-contents)
  - [Booting the Installer](#booting-the-installer)
  - [Downloading Installer Files](#downloading-installer-files)
  - [Partitioning the Disk](#partitioning-the-disk)
  - [Bootstraping and Base System Installation](#bootstraping-and-base-system-installation)
  - [Post Installation and bootloader setup](#post-installation-and-bootloader-setup)
  - [Finishing Up](#finishing-up)

## Booting the Installer

1. The user boots from the installation media (USB/DVD). Must boot from UEFI, legacy BIOS not supported. Max support will be given to BIOS with UEFI compatibility mode. (must run .EFI files)
2. GRUB loads the Linux kernel and initial RAM disk (initrd) from the installation media.
3. Installer boots into a live environment, simmilar to SteamOS KDE desktop environment. (/etc/sddm.conf.d/autologin.conf) 
4. User launches the installer application from the desktop. (/home/holoiso/Desktop/install.desktop)

## Downloading Installer Files

1. Installer first checks if installer is in online mode (has files included or not on the installation media) (holoiso-getlatest)
2. If online mode is enabled, installer connects to the internet and downloads necessary installation files from the HoloISO servers. (holoiso-gui-download)
3. Checksums and verifies the integrity of the downloaded files to ensure they are not corrupted. (holoiso-gui-download --sha256 shasum_url)
4. Once downloads, installer runs holoiso-gui-installer

## Partitioning the Disk

Once user selects their target disk, name and passwords, its passed to "holoiso-installer" script. This script creates partitioning as this:

- EFI (/boot/efi | holo_efi) - boot,esp,rw - 256MB - FAT32
- Root (/ | holo_root) - ro - 15GB - BTRFS
- Var (/var | holo_var) - rw - 16GB - BTRFS
- Home (/home | holo_home) - rw - Remaining Space - BTRFS

## Bootstraping and Base System Installation

holoiso-install passes name of user and passwords, then does the following:

1. Mounts root to /tmp/holo_root
2. Mounts home to /tmp/holo_home
3. Uses btrfs snapshots to decompress and move base system to /tmp/holo_root
4. Mounts other partitions (var, efi, again home) to rootfs on /tmp/holo_root

## Post Installation and bootloader setup

1. After base system installation, the installer configures system settings such as hostname, user accounts, and network settings.
2. Installs and configures the GRUB bootloader on the target disk to ensure the system can boot properly.
3. Installs additional fixes for devices as Ayo, Steam deck, etc.

## Finishing Up

Once all configurations are complete, the installer unmounts all partitions and prompts the user to reboot into their new HoloISO installation.
