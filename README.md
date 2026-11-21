# Introduction

---
**Update: Starting with systemd version 248, TPM2 unlock support is built-in and it is much faster than clevis. Follow [this]( https://wiki.archlinux.org/title/Systemd-cryptenroll) guide to get it working.**

**Update 2: Do not attempt to use the systemd version of TPM2 unlock if you don't use systemd.**

---

A simple hook to unlock LUKS devices on boot using TPM and `clevis`.

Tested Systems:
* Manjaro Linux 20.2.1 with `systemd-boot` and `mkinitcpio`.
* Artix Linux OpenRC (Linux 5.17.4-artix1-1) with `grub` and `mkinitcpio`.
* Arch Linux (Linux 5.17.4.arch1-1) with `grub` and `mkinitcpio`.
* Artix Linux Dinit (Linux 7.2.15.artix1-1) with uki (no bootloader) and `mkinitcpio`.

# Installing

## Manual Method
1. `git clone` this repo

2. `cd arch-mkinitcpio-clevis-hook/`

3.  `makepkg -si`

4. Add `clevis` binding to your LUKS device.
    ```sh
    sudo clevis luks bind -d <device> tpm2 '{"pcr_ids":"0,1,2,3,4,5,6,7"}'
    ```

5. Add the clevis hook to the mkinitcpio
    ```
    sudo vim /etc/mkinitcpio.conf
    # Edit the hooks and add clevis before the 'encrypt' hook. Eg:
    # HOOKS=(.. clevis encrypt ..)
    ```

    Note: If you are using `plymouth`, replace the `plymouth-encrypt` hook with `encrypt`. `plymouth-encrypt` is reported to be buggy when the device is already unlocked. [More info.](https://github.com/kishorv06/arch-mkinitcpio-clevis-hook/issues/1)
7. Generate `initramfs` image.
    ```sh
    sudo mkinitcpio -P
    ```
    
8. Reboot.

# Updating

If you have updated any of the settings in BIOS, changed anything in the kernel options, you have to recreate the  `clevis` binding as TPM will not be able to unlock the device.

```sh
sudo clevis luks unbind -d <device> -s <slot-id>
sudo clevis luks bind -d <device> tpm2 '{"pcr_ids":"0,1,2,3,4,5,6,7"}'
```

NOTE: `slot-id` is normally 1, but this can be checked by running `sudo cryptsetup luksDump <encrypted device>`

# Troubleshooting

Usually unlocking fails only when any of the TPM registers were updated as part of a system configuration change. Try rebooting the system and re adding the `clevis` LUKS binding. In most cases this should fix the issue. Feel free to create an issue if your problem is not resolved.

# Credits

Originally forked from [arch-clevis](https://gitlab.com/cosandr/arch-clevis) by [Andrei Costescu](https://gitlab.com/cosandr). I just simplified, fixed some bugs & added a clear readme, and @SimPilotAdamT just adapted it so this can be used in the AUR for easier install and uninstall.

Further forked from [arch-mkinitcpio-clevis-hook](https://github.com/kishorviswanathan/arch-mkinitcpio-clevis-hook) by [Andrei Costescu](https://gitlab.com/cosandr) to implement missing binaries from the `initramfs` (debuged with chatGPT, the fix was done by hand and suggested by it).
