# Installing Arch Linux

## Requirements

Before installing Arch, you need:

- A virtualization software such as [VirtualBox](https://www.virtualbox.org/wiki/Downloads) or [VMware Workstation/Fusion](https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion).
- The latest [Arch Linux ISO](https://archlinux.org/download/).
- A reliable internet connection (for package installation during setup).

---
## My VM Setup

This is the virtual machine configuration I used for installing Arch:

- **RAM**: 8 GB  
- **Processors**: 4  
- **Video Memory**: 128 MB  
- **Storage**: 40 GB (dynamically allocated)  
- **Network**: NAT  
- **Boot**: Mounted Arch ISO as startup disk

## Booting the ISO

When the ISO is booted, you’ll be prompted to start the installation media.  
Just hit **Enter**, and you’ll land in the terminal:

![](arch_terminal.png)
> [!NOTE]
> You can increase the font size with:
> ```bash
> setfont ter-120n
> ```
> Replace `120` with a higher or lower number depending on your preference.

## Preparing the System

Before continuing, make sure the essential packages are up to date:

- **archlinux-keyring →** contains trusted keys used to verify package signatures.  
- **archinstall →** a guided installer that helps automate Arch Linux installation. 

```bash
sudo pacman -Sy archlinux-keyring archinstall
```

### Partitioning

1. Run the `lsblk` command to list all available disk drives:

![](command_lsblk.png)

2. Once you identify the target disk, start partitioning with:
```bash
# Disk name may vary, just replace 'sda' with the actual disk name
cfdisk /dev/sda
```

>[!NOTE] If you are prompted to select a disk label, choose `gpt`

- To create partition use the `up` and `down` arrows to select `free space`, then `left` and `right` arrows to select `new` and add the size of it. Add this partitions:
	- **1GB** to mount the `/boot` folder
	- **20GB - rest of the disk** to mount the root `/` folder

- After making the partitioning, go the the `1GB` partition and change the `type` to `EFI System`. You should end with something like this:
![](arch_partition.png)

- Select `Write` and type `yes` to save the changes. Then `Quit`

### Formatting

Before we can install the SO we need to ensure the partitions have the right file system, the `/boot` will need a `fat32` and the root `/` an `ext4`.

- use the `lsblk` command to see the drives again and we should see the partitions we made
![](command_lsblk_partition.png)

- to format the partitions we use the `mkfs` command:
```bash
# EFI location is the 1GB partition
mkfs.fat -F32 /dev/<efi-location>

# Root location is the other partition with the rest (or 20GB minimun)
mkfs.ext4 /dev/<root-location>
```
**EXPECTED OUTPUT:**
![](command_mkfs.png)

### Using archinstall

Now we can execute the `archinstall` command to start the guided installation.

- **Locales →** Change the keyboard layout, language and encoding
- **Mirrors and  repositories →** Select the closest region to have the most speed when downloading packages
- **Disk configuration** → 
- **Swap →** Make sure is `Enable`
- **Bootloader →** Select `Grub`
- **Authentication →** Create a `root` password and a user with `super user` or `sudo` permissions
- **Kernel →** Leave the default `Linux`
- **Network Configuration →** Use `NetworkManager`
- **Timezone →** Select your current Time zone
- **OPTIONAL**
	- **Hostmane →** change the name of the host (Redundant I know)
	- **Profile →** Select your desktop environment (we are going with minima this time)

### Disk Configuration

The most important setting. On `Disk configuration` select `Partitioning -> Manual Partitioning` and select the disk we partitioned previously.
- On the **Fat32** partition hit `enter` then `assing mountpoint` to `/boot`
- Do the same on the **ext4** partition, but mount on the root `/`
- Confirm and exit and go back to the Main menu and select `install`.

>[!WARNING]
> Make sure you follow the exacts steps with the drives or else the installation will fail.
> If you want to dual boot Arch, make sure to make a backup or [restoring point on windows](https://support.microsoft.com/en-us/windows/system-restore-a5ae3ed9-07c4-fd56-45ee-096777ecd14e)

If we do all the steps the right way, a prompt should appear, select `chroot into instalation` to configure some extra steps

### Post installation configuration

After installation we need to make sure the GRUB is installed and configured properly.
```bash
sudo pacman -S efibootmgr grub mtools dosfstools
```

And this command to configure GRUB 
```bash
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB

# save the configuration to a file
grub-mkconfig -o /boot/grub/grub.cfg
```

Reboot using the `exit` and `reboot` commands.

###### Expected output
![](GRUB.png)


## Set up windows boot manager (only for dual boot instalations)

If you do a dual boot installation and Windows does not appear in the GRUB, go to the terminal and run again the grub configuration command
```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

There you should see the `Windows boot manager founded`
>[!NOTE]
>I can’t show this because I’m on a VM, but i will update this file soon, I intend to use Arch myself on a dual boot.

If not, we need to change some settings:
- make sure to have a text editor install (like nano)
```bash
sudo pacman -S nano

# open the file using nano
sudo nano /etc/default/grub
```

- Go to the last file and uncomment the last line `GRUB_DISABLE_OS_PROBER=false` and save the file. Also make sure `os-prober` and `fuse3` is installed
```bash
sudo pacman -S os-prober fuse3

# run one more time the grub config command
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

After that you should see the `Windows Boot Manager` on the output, and after a reboot, it should appear on the GRUB list to select.