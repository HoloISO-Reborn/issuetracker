# Boot Flow Documentation

This document outlines the boot flow process for the HoloISO operating system, detailing each stage from power-on to the desktop environment loading. Understanding this flow is crucial for developers working on system optimization and troubleshooting boot-related issues.

1. Grub2 is initialized as the primary bootloader. After one seccond, it loads the linux-lljs kernel and the initial RAM disk (initrd) from the EFI system partition.
2. Starts systemd as the init system.
3. systemd mounts the root filesystem from the BTRFS partition labeled "holo_root".
4. systemd then mounts additional BTRFS subvolumes for /home (holo_home) and /var (holo_var), as well as the EFI system partition (holo_efi) to /boot/efi.
5. Starts plymouth to display the boot splash screen.
6. systemd initializes essential services and targets required for the system to operate.
7. Once finished, systemd hands over control to the display manager (SDDM).
8. SDDM loads the gamescope-session.desktop (/usr/share/wayland-sessions/gamescope-session.desktop) to start gamescope as the Wayland compositor.
More soon...
