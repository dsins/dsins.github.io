# VirtualBox: Installation, Extension Pack & Guest Additions on Ubuntu

---

## Installation

### Method A - Ubuntu repos (quick, but often outdated version)

```bash
sudo apt update
sudo apt install virtualbox
```

### Method B - Official Oracle repo (recommended, always current)

```bash
# Add Oracle GPG key
wget -O- https://www.virtualbox.org/download/oracle_vbox_2016.asc \
  | sudo gpg --dearmor --yes -o /usr/share/keyrings/oracle-virtualbox.gpg

# Add repo (adjust "noble" to your Ubuntu codename: jammy=22.04, noble=24.04)
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox.gpg] \
  https://download.virtualbox.org/virtualbox/debian noble contrib" \
  | sudo tee /etc/apt/sources.list.d/virtualbox.list

# Install
sudo apt update
sudo apt install virtualbox-7.1
```

Check your Ubuntu codename if unsure:
```bash
lsb_release -cs
```

---

## Extension Pack

Adds USB 2.0/3.0 support, RDP (remote display), NVMe, PCI passthrough, and disk encryption.

### Install via apt (requires multiverse repo)

```bash
sudo add-apt-repository multiverse
sudo apt update
sudo apt install virtualbox-ext-pack
# Accept the license with Tab → OK → Yes
```

### Install manually (matches any VirtualBox version exactly)

```bash
# Check your installed version first
vboxmanage --version
# Example output: 7.1.4r165100

# Download matching Extension Pack from:
# https://www.virtualbox.org/wiki/Downloads

# Install via CLI
sudo vboxmanage extpack install Oracle_VirtualBox_Extension_Pack-7.1.4.vbox-extpack

# Verify
vboxmanage list extpacks
```

> Extension Pack version must match VirtualBox version exactly - mismatch causes install failure.

---

## Guest Additions (Install Inside the VM)

Guest Additions enable shared folders, clipboard sharing, drag-and-drop, auto-resize display, and better mouse integration. Install them inside each guest VM, not on the host.

### On a Linux guest (Ubuntu/Debian)

```bash
# In the VM: Devices menu → Insert Guest Additions CD Image
# Then in the VM terminal:
sudo apt install build-essential dkms linux-headers-$(uname -r)
sudo mount /dev/cdrom /mnt
sudo /mnt/VBoxLinuxAdditions.run
sudo reboot
```

### On a Windows guest

Insert the CD image via Devices menu → run `VBoxWindowsAdditions.exe` from the mounted drive → reboot.

### Verify Guest Additions are running (Linux guest)

```bash
lsmod | grep vboxguest
# Should show: vboxguest  <size>  2 vboxsf,vboxvideo
```

---

## Add Your User to the vboxusers Group

Required to access USB devices from VMs:

```bash
sudo usermod -aG vboxusers $USER
# Log out and back in for the group change to take effect
groups $USER  # verify vboxusers appears
```

---

## Shared Folders (Host ↔ VM)

```bash
# On the host, add a shared folder via VM Settings → Shared Folders
# Or via CLI:
vboxmanage sharedfolder add "VMName" --name "shared" --hostpath "/home/user/shared" --automount

# Inside the Linux guest, mount manually if not auto-mounted:
sudo mkdir -p /mnt/shared
sudo mount -t vboxsf shared /mnt/shared

# Make it persistent (add to /etc/fstab inside guest):
shared  /mnt/shared  vboxsf  defaults,uid=1000,gid=1000  0  0
```

---

## Useful VBoxManage Commands

```bash
# List all VMs
vboxmanage list vms

# List running VMs
vboxmanage list runningvms

# Start a VM headless (no GUI window)
vboxmanage startvm "VMName" --type headless

# Take a snapshot
vboxmanage snapshot "VMName" take "snapshot-name"

# Restore a snapshot
vboxmanage snapshot "VMName" restore "snapshot-name"

# List snapshots
vboxmanage snapshot "VMName" list

# Modify VM CPU/RAM
vboxmanage modifyvm "VMName" --cpus 4 --memory 4096

# Export VM to OVA
vboxmanage export "VMName" -o /path/to/output.ova

# Import OVA
vboxmanage import /path/to/file.ova
```

---

## Uninstall

```bash
# Remove VirtualBox
sudo apt purge virtualbox virtualbox-7.1

# Remove Extension Pack
sudo apt purge virtualbox-ext-pack
# Or via vboxmanage:
sudo vboxmanage extpack uninstall "Oracle VirtualBox Extension Pack"

# Clean up leftover dependencies
sudo apt autoremove
```

VMs and their disk images in `~/VirtualBox VMs/` are **not** deleted by purge - remove manually if needed:
```bash
rm -rf ~/VirtualBox\ VMs/
```

---

## Troubleshooting

### Kernel module fails to load after kernel update

```bash
sudo /sbin/vboxconfig
# Rebuilds kernel modules for the current kernel
```

If it fails, ensure headers are installed:
```bash
sudo apt install linux-headers-$(uname -r)
sudo /sbin/vboxconfig
```

### USB devices not visible in VM

- Confirm user is in `vboxusers` group (see above) and has re-logged in
- Extension Pack must be installed for USB 2.0/3.0
- USB 1.1 (OHCI) works without Extension Pack

### VT-x/AMD-V not available

```
VT-x is not available (VERR_VMX_NO_VMX)
```

Enable virtualization in BIOS/UEFI: look for **Intel VT-x**, **AMD-V**, or **SVM** setting. If running VirtualBox inside another VM (nested virtualization), the outer hypervisor must expose these flags.

### Display too small, no auto-resize

Guest Additions not installed or outdated. Reinstall matching the current VirtualBox host version.

---

## Notes

- VirtualBox is a **Type 2 hypervisor** (runs on top of the host OS). For production server virtualization, consider KVM/QEMU (Type 1, built into Linux kernel) - better performance, no GUI dependency.
- Snapshots consume disk space proportional to changes made after the snapshot. Don't accumulate snapshots indefinitely.
- `.vdi` (VirtualBox native), `.vmdk` (VMware compatible), and `.vhd` (Hyper-V compatible) disk formats are all supported - useful when migrating VMs between hypervisors.
