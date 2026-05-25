# TODO: Immutable NVIDIA Driver Layer Support for HoloISO

## Goal

Add optional NVIDIA support to HoloISO/SteamOS-style immutable rootfs
without affecting AMD/Intel users or requiring pacman after install.

## Architecture

- Base root image contains:
  - Mesa drivers
  - AMD/Intel Vulkan/OpenGL stack
  - Standard SteamOS userspace
- NVIDIA support is shipped as optional squashfs driver layers.
- Only one NVIDIA layer may be active at once.

## Driver Layers

- /usr/share/holoiso/driver-layers/
  - nvidia-open.squashfs
  - nvidia-580xx.squashfs
  - nvidia-470xx.squashfs
  - all other drivers needed for gpu or any other devices needed separate drivers loading

## Layer Contents

Each NVIDIA layer contains ONLY runtime files, for example:

- /usr/lib/libnvidia*
- /usr/lib/modules/*
- /usr/share/vulkan/*
- /usr/share/glvnd/*
- /usr/share/egl/*
- /usr/bin/nvidia-*
- /etc/modprobe.d/*
- /etc/modules-load.d/*

Do NOT include:

- pacman db
- docs
- locales
- cache
- unrelated dependencies

## Build Process

1. Use pacstrap into temporary roots:
   - /build/nvidia/open
   - /build/nvidia/580xx

2. Extract only required runtime files.

3. Package stripped runtime into squashfs:
   - mksquashfs overlay nvidia-open.squashfs

4. Place generated squashfs layers into:
   - /usr/share/holoiso/driver-layers/

## Boot Flow

1. Boot base immutable image.
2. systemd service detects GPU.
3. Select matching driver layer:
   - RTX/Turing+ -> nvidia-open
   - Pascal/Maxwell -> nvidia-580xx
   - AMD/Intel -> no NVIDIA layer
4. Mount selected squashfs layer.
5. Bind/overlay mount driver files over rootfs.
6. Run:
   - depmod -a
   - modprobe nvidia
   - modprobe nvidia_modeset
   - modprobe nvidia_uvm
   - modprobe nvidia_drm
7. Start SDDM/gamescope.

## Important Rules

- NVIDIA layer must match shipped kernel version exactly.
- Userspace NVIDIA version must match kernel modules.
- Only ONE NVIDIA layer may be mounted simultaneously.
- Driver layer must load BEFORE display-manager.service.
- only works if NVIDIA is NOT required in initramfs.

## Advantages

- Single universal immutable image.
- No pacman usage after install.
- Keeps AMD/Intel SteamOS behavior untouched.
- Allows optional NVIDIA support.
- No separate ISO builds per GPU vendor.
- Compatible with squashfs root layout already used.
