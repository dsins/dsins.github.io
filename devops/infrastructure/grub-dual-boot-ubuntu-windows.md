# GRUB: Manually Adding Windows to Ubuntu Boot Menu

**When you need this:** Ubuntu doesn't detect Windows in GRUB after install, or `update-grub` finds it but then loses it after kernel updates. This guide covers manual entry via `40_custom` — the permanent, update-proof approach.

> **Warning:** Editing GRUB incorrectly can prevent the system from booting. Read each step before running it. Have a live USB ready as a fallback.

---

## Prerequisites — Understand Your Setup First

### Is your system UEFI or Legacy BIOS?

```bash
[ -d /sys/firmware/efi ] && echo "UEFI" || echo "Legacy BIOS"
```

This guide covers **UEFI** (the common case for any machine made after ~2012). Legacy BIOS uses different chainloader syntax.

### Find the Windows EFI partition

```bash
sudo fdisk -l
```

Look for a partition with type `EFI System` — typically small (100–500 MB), formatted as FAT32. Example output:

```
Device           Start      End  Sectors  Size Type
/dev/nvme0n1p1    2048   206847   204800  100M EFI System   ← this one
/dev/nvme0n1p2  206848  ..       ...      16M  Microsoft reserved
/dev/nvme0n1p3  ...              ...     ~C:  Microsoft basic data
```

Note the device path (e.g., `/dev/nvme0n1p1` for NVMe, `/dev/sda1` for SATA).

---

## Step 1 — Get the EFI Partition UUID

```bash
sudo blkid /dev/nvme0n1p1
```

Example output:
```
/dev/nvme0n1p1: UUID="2B11-51E9" BLOCK_SIZE="512" TYPE="vfat" PARTLABEL="EFI System Partition"
```

Copy the `UUID` value — you'll use it in the GRUB entry. The short format (e.g., `2B11-51E9`) is correct for FAT32 EFI partitions.

---

## Step 2 — Verify the Windows Boot File Exists

Mount the EFI partition and confirm the Windows bootloader is there:

```bash
sudo mkdir -p /mnt/efi
sudo mount /dev/nvme0n1p1 /mnt/efi
ls /mnt/efi/EFI/
# Should show: Microsoft  ubuntu  (and possibly other entries)

ls /mnt/efi/EFI/Microsoft/Boot/
# Should include: bootmgfw.efi

sudo umount /mnt/efi
```

If `bootmgfw.efi` is missing, Windows boot files are damaged — this GRUB entry won't fix that. You'd need Windows recovery media.

---

## Step 3 — Add the Custom GRUB Entry

```bash
sudo nano /etc/grub.d/40_custom
```

Add below the existing comment lines (don't delete the `exec tail` line at the top):

```bash
menuentry 'Windows 11' {
    insmod part_gpt
    insmod fat
    insmod chain
    search --fs-uuid --no-floppy --set=root 2B11-51E9
    chainloader (${root})/EFI/Microsoft/Boot/bootmgfw.efi
}
```

> **Why `40_custom` and not `40_custom` alternatives?**
> Files in `/etc/grub.d/` are executed in numeric order. `40_custom` runs after OS detection scripts (`10_linux`, `30_os-prober`) but before the closing script (`90_disable_os_prober`). Entries here survive `update-grub` calls and kernel upgrades intact.

Save: `Ctrl+O` → `Enter` → `Ctrl+X`

---

## Step 4 — Set Permissions

```bash
sudo chmod o-w /etc/grub.d/40_custom
# Remove write permission for others — GRUB ignores world-writable scripts as a security measure
```

---

## Step 5 — Update GRUB and Reboot

```bash
sudo update-grub
```

Check the output — you should see a line like:
```
Found Windows Boot Manager on /dev/nvme0n1p1@/EFI/Microsoft/Boot/bootmgfw.efi
```

Or for the custom entry:
```
Adding boot menu entry for GRUB bootloader
```

```bash
sudo reboot
```

Windows 11 should now appear in the GRUB menu.

---

## GRUB Customization (Optional)

### Change default boot entry

```bash
sudo nano /etc/default/grub
```

```bash
# Boot the first entry (Ubuntu) by default — 0-indexed
GRUB_DEFAULT=0

# Or boot a specific entry by name
GRUB_DEFAULT="Windows 11"

# Or remember the last selection
GRUB_DEFAULT=saved
GRUB_SAVEDEFAULT=true
```

```bash
sudo update-grub
```

### Change boot menu timeout

```bash
# In /etc/default/grub
GRUB_TIMEOUT=10      # show menu for 10 seconds
GRUB_TIMEOUT=0       # boot immediately (no menu shown)
GRUB_TIMEOUT=-1      # wait indefinitely for user input
```

### One-time boot override (no config change needed)

```bash
# Boot into Windows on the next reboot only, then revert to default
sudo grub-reboot "Windows 11"
sudo reboot

# Set a new permanent default by entry index
sudo grub-set-default 0
```

To find entry indices:

```bash
grep -E "^menuentry|submenu" /boot/grub/grub.cfg | nl -ba
```

---

## Troubleshooting

### `update-grub` doesn't find Windows automatically

`os-prober` may be disabled. Check:

```bash
grep GRUB_DISABLE_OS_PROBER /etc/default/grub
```

If it's set to `true`, either remove the line or set it to `false`, then run `sudo update-grub`. Note: on some distros `os-prober` is disabled by default since GRUB 2.06.

### GRUB menu doesn't appear at all (boots straight to Ubuntu)

```bash
# In /etc/default/grub, ensure:
GRUB_TIMEOUT_STYLE=menu   # not "hidden" or "countdown"
GRUB_TIMEOUT=5
```

Then `sudo update-grub`.

### Entry appears but selecting Windows gives error

Common errors and causes:

| Error | Cause |
|---|---|
| `error: file '/EFI/Microsoft/Boot/bootmgfw.efi' not found` | Wrong UUID, or EFI partition not mounted as `root` |
| `error: invalid signature` | Secure Boot mismatch — try disabling Secure Boot in BIOS |
| Boots to Windows recovery | Windows fast startup wrote dirty state — disable Fast Startup in Windows |

**Verify UUID is correct:**
```bash
sudo blkid | grep -i efi
```

**Check Windows Fast Startup** (in Windows): Control Panel → Power Options → Choose what the power buttons do → Turn off fast startup.

### Revert a bad GRUB change

If the system won't boot after editing GRUB, boot from Ubuntu live USB and:

```bash
sudo mount /dev/nvme0n1p2 /mnt          # mount your Ubuntu root partition
sudo mount /dev/nvme0n1p1 /mnt/boot/efi # mount EFI
sudo mount --bind /dev /mnt/dev
sudo mount --bind /proc /mnt/proc
sudo mount --bind /sys /mnt/sys
sudo chroot /mnt

# Fix the bad config
nano /etc/grub.d/40_custom
update-grub
exit
sudo reboot
```

---

## Notes

- Always use `40_custom` for manual entries, not `grub.cfg` directly — `grub.cfg` is auto-generated and overwritten by `update-grub`.
- On NVMe drives, partition names are `nvme0n1p1`, `nvme0n1p2`, etc. On SATA, they're `sda1`, `sda2`, etc.
- If you have multiple Windows installations or multiple EFI partitions, repeat Steps 1–3 for each, using different `menuentry` names.
- `insmod part_gpt`, `insmod fat`, `insmod chain` lines ensure the required GRUB modules are loaded — safe to include even if already loaded globally.
