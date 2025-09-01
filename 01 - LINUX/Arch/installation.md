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

![](/98%20-%20META/IMAGES/arch_instalation/arch_terminal.png)
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

## Partitioning

1. Run the `lsblk` command to list all available disk drives:

![](/98%20-%20META/IMAGES/arch_instalation/command_lsblk.png)

2. Once you identify the target disk, start partitioning with:
```bash
# Disk name may vary, just replace 'sda' with the actual disk name
cfdisk /dev/sda
```

>[!NOTE] If you are prompted to select a disk label, choose `gpt`

### Creating Partitions

- Use the **↑ / ↓** arrow keys to select _Free space_.
- Use the **← / →** arrow keys to select _New_.
- Enter the partition size.

Create the following partitions:
- **1GB** for `/boot`
- **Remaining space (~20 GB or more)** for `/` (root)

- Highlight the **1 GB partition**, then change its **Type** to `EFI System`.
- The result should look similar to this:
![](/98%20-%20META/IMAGES/arch_instalation/arch_partition.png)

- Select **Write** and type `yes` to confirm.
- Finally, choose **Quit** to exit.

## Formatting

Before installing the system, the partitions need to be formatted with the proper file systems:  
- `/boot` → `FAT32`  
- `/` (root) → `ext4`

### Listing Partitions

Run `lsblk` again to verify the partitions you created:
![](/98%20-%20META/IMAGES/arch_instalation/command_lsblk_partition.png)

### Formatting Partitions

Use the `mkfs` command to format each partition:
```bash
# EFI partition (1 GB)
mkfs.fat -F32 /dev/<efi-partition>

# Root partition (remaining space, min. 20 GB)
mkfs.ext4 /dev/<root-partition>
```

### EXPECTED OUTPUT

![](/98%20-%20META/IMAGES/arch_instalation/command_mkfs.png)

## Using archinstall

Now that the partitions are ready, run the guided installer:
```bash
archinstall
```

You’ll be presented with a menu to configure the system. Below are the recommended options:

- **Locales →** Set keyboard layout, language, and encoding.
- **Mirrors and  repositories →** Choose the closest region for faster downloads.
- **Swap →** Make sure is `Enable`
- **Bootloader →** Choose `Grub`
- **Authentication →** Set a password for `root` and create a regular user with `sudo` privileges.
- **Kernel →** Keep the default `Linux`
- **Network Configuration →** Select `NetworkManager`
- **Timezone →** Select your current Timezone
- **OPTIONAL**
	- **Hostmane →** Set a custom name for your machine.
	- **Profile →** Choose a desktop environment. _(This time I selected `minimal` for a lightweight setup.)_

### Disk Configuration

This is one of the most important settings.

In `Disk configuration`, select **Partitioning → Manual Partitioning** and choose the disk you partitioned earlier.

- For the **FAT32** partition → press `Enter`, then assign the mountpoint to `/boot`.  
- For the **ext4** partition → press `Enter`, then assign the mountpoint to `/`. 

Once done, confirm the changes and return to the main menu. Select **Install** to continue.

>[!WARNING]
> Make sure you assign the correct mountpoints to the correct partitions — otherwise the installation will fail.
> If you plan to dual boot Arch with Windows, make a backup or create a [System Restore Point](https://support.microsoft.com/en-us/windows/system-restore-a5ae3ed9-07c4-fd56-45ee-096777ecd14e)

If all steps are followed correctly, you’ll see a prompt. Choose **chroot into installation** to proceed with extra configuration.

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
![](/98%20-%20META/IMAGES/arch_instalation/GRUB.png)


### Setting up Windows Boot Manager (Dual Boot Only)

If you installed Arch Linux alongside Windows but don’t see Windows in the GRUB menu, regenerate the GRUB configuration:
```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

You should see a line indicating that **Windows Boot Manager** was found.
>[!NOTE]
>Since I’m working inside a VM, I can’t show this output right now. I’ll update this section once I set up Arch in a dual boot on real hardware.

#### Troubleshooting

If Windows still doesn’t appear, follow these steps:
1. **Install a text editor** (for example, `nano`):
```bash
sudo pacman -S nano

# open the file using nano
sudo nano /etc/default/grub
```

2. **Edit GRUB configuration**:
```bash

```

- Find the line `#GRUB_DISABLE_OS_PROBER=false` and remove the `#`.
- Save and exit.

3. **Install required packages**:
```bash
sudo pacman -S os-prober fuse3
```

4. **Regenerate GRUB configuration again**:
```bash
# run one more time the grub config command
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

After this, you should see **Windows Boot Manager** in the output.  
On the next reboot, it will appear in the GRUB menu for selection.