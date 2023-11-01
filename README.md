# Arch Linux Installation Guide

**Arch Linux** is a popular Linux distribution known for its simplicity, minimalism, and high level of customization. It is favored by experienced Linux users who appreciate a do-it-yourself (DIY) approach to creating their ideal computing environment. Key features of Arch Linux include:

1. **Rolling Release Model:** Arch Linux follows a rolling release model, which means that it doesn't have fixed release versions. Instead, it offers continuous software updates, allowing users to access the latest software versions as soon as they are available.

2. **Minimal Installation:** Arch Linux provides a minimal base installation with only essential components. Users have the flexibility to install and configure additional software, packages, and desktop environments according to their preferences.

3. **Arch User Repository (AUR):** The Arch User Repository is a community-driven repository of user-contributed package builds. It enables users to easily install software that is not available in the official repositories. AUR packages require users to build them, but helper tools like "yay" simplify the process.

4. **Customization:** Arch Linux is known for its high degree of customization. Users have full control over their system's configuration, allowing them to build a Linux environment that precisely suits their needs.

5. **Pacman Package Manager:** Arch Linux uses the "pacman" package manager, a command-line tool for installing, updating, and managing software packages. It is lauded for its simplicity and efficiency.

6. **Documentation:** Arch Linux offers comprehensive documentation, including the Arch Wiki, which provides detailed information and guides on various aspects of the system, software installation, and troubleshooting.

7. **Community Involvement:** The Arch Linux community is strong and active. Users are encouraged to participate in discussions, forums, and share their knowledge with one another.

8. **Lightweight:** Arch Linux's minimalist nature makes it a lightweight distribution, making it suitable for older hardware or systems where resource efficiency is a priority.

While Arch Linux's DIY philosophy and focus on simplicity may not be the best fit for beginners or users seeking an out-of-the-box experience, it remains an excellent choice for those who want to learn more about Linux, have full control over their computing environment, and build a system tailored to their specific needs.

This **guide** will walk you through the installation of Arch Linux on your machine. Make sure to follow each step carefully for a successful installation.

## Step 0: Download Arch Linux ISO and Create a Bootable USB Drive:

Before you can begin the installation, you need to obtain the Arch Linux installation media and create a bootable USB drive. Follow these steps:

### Step 0.1: Download the Arch Linux ISO:

- Visit the official Arch Linux download page at [https://archlinux.org/download/](https://archlinux.org/download/).
- Select the ISO image that matches your system architecture (e.g., x86_64) and your preferred download mirror. You can download it using a web browser.

### Step 0.2: Verify the Download (Optional but recommended):

Arch Linux provides SHA-1 and SHA-256 checksums for their ISOs. After downloading the ISO, you can verify its integrity by comparing the checksum of the downloaded file with the provided checksum. To calculate the SHA-256 checksum, you can use a tool like `sha256sum`. For example:

```shell
sha256sum archlinux-<version>-x86_64.iso
```

Compare the generated checksum with the one provided on the Arch Linux download page to ensure that your download is not corrupted.

### Step 0.3: Create a Bootable USB Drive:

Create a Bootable USB Drive:

To create a bootable USB drive, you can use a tool like Balena Etcher (available for Windows, macOS, and Linux) or Rufus (Windows only). Here's how to do it with Balena Etcher:

- Download and install Balena Etcher.
- Insert a USB drive with sufficient capacity (at least 2 GB) into your computer.
- Open Balena Etcher.
- Click the "Select Image" button and choose the Arch Linux ISO you downloaded.
- Click the "Select Target" button and choose the USB drive you want to write the ISO to.
- Click the "Flash" button to write the ISO to the USB drive. This process will erase all data on the USB drive, so make sure to back up any important files.

Now, you have a bootable USB drive with the Arch Linux ISO, and you can proceed to install Arch Linux following the steps provided in the original guide, starting with "Step 1."

## Step 1: Increase Font Size

This step increases the console font size to improve readability. The command setfont ter-132n sets the console font to `ter-132n`

```shell
setfont ter-132n
```

## Step 2: Connect to Wi-Fi

This section is all about connecting to a Wi-Fi network.

### Step 2.1: Unblock Hardware-Locked Wi-Fi Adapter

If your Wi-Fi adapter is hardware-locked, you can unblock it using rfkill.

```shell
rfkill unblock all

# This command unblocks any hardware-locked Wi-Fi adapters by using the rfkill utility. It ensures that the wireless hardware is enabled.

```

Check the status of devices using rfkill:

```shell
rfkill

# This command checks the status of wireless devices using the rfkill utility
```

### Step 2.2: Connect to Wi-Fi using ```iwctl```

Scan for available networks and connect to your desired Wi-Fi network.

```shell
iwctl
# This command opens the iwctl interactive tool for managing wireless connections.

station wlan0 scan
# Scans for available Wi-Fi networks using the specified wireless interface (wlan0 in this case).

station wlan0 connect <ssid>
# Connects to the desired Wi-Fi network by specifying the network's SSID. It will prompt you for the Wi-Fi password
```

You will be prompted for the Wi-Fi password.

Test your network connection by pinging a website:

```shell
ping archlinux.org

# This command tests the network connection by pinging the archlinux.org website to ensure that you have internet access
```

## Step 3: Format Your Drives

This section involves preparing your storage devices for installation.

### Step 3.1: Wipe Disk

View the current drive status using lsblk. Then, use gdisk to wipe the drive. Replace /dev/nvme0n1 with your drive if it's different.

Select 'x' for expert mode and 'z' to wipe the drive.

```shell
lsblk

# Lists information about block devices, showing the current drive status. It helps you identify the target drive.

gdisk /dev/nvme0n1

#Launches the gdisk utility to manage disk partitions. You specify the target drive as /dev/nvme0n1. Inside gdisk, you can select 'x' for expert mode and 'z' to wipe the drive. This clears existing partitions and prepares the drive for new ones.

```


### Step 3.2: Partition the Drive

Use cgdisk to partition the drive according to your needs. The example partitions are for reference; adjust them as required.

```shell
cgdisk /dev/nvme0n1

# Opens the cgdisk utility for creating and configuring partitions on the target drive, which is specified as /dev/nvme0n1. You can customize the partition layout according to your needs, creating boot, swap, root, and home partitions.

```

My personal drive setup, feel free to edit it

- Boot partition: 1024MiB, Hex Code: EF00
- Swap partition: 16GiB, Hex Code: 8200
- Root partition: 50GiB, Hex Code: 8300 (default)
- Home partition: All remaining, Hex Code: 8300 (default)

Write the changes to the disk and confirm by running lsblk to check the partitions.

### Step 3.3: Format the Drives

Format the partitions with the required file system formats.

```shell
mkfs.fat -F32 /dev/nvme0n1p1

# Formats the first partition as a FAT32 file system, which is typically used for the EFI system partition.

mkswap /dev/nvme0n1p2
swapon /dev/nvme0n1p2

# Formats the second partition as a swap file system and activates it using swapon.

mkfs.ext4 /dev/nvme0n1p3
mkfs.ext4 /dev/nvme0n1p4

# These commands format the third and fourth partitions with the ext4 file system, which is commonly used for Linux system and home partitions.

```

### Step 3.4: Mount the Partitioned Drives
```shell
mount /dev/nvme0n1p3 /mnt

# Mounts the root partition to the /mnt directory, which serves as the root of the new Linux file system.

mkdir /mnt/boot
mkdir /mnt/home

# Creates directories for the boot and home partitions.

mount /dev/nvme0n1p1 /mnt/boot
mount /dev/nvme0n1p4 /mnt/home

# Mounts the EFI system partition and home partition to the appropriate directories under /mnt.

```

## Step 4: Setup Pacman

Pacman is Arch Linux's package manager, and this step configures its settings.

### Step 4.1: Configure Pacman

Edit the `pacman.conf` in /etc/pacman.conf file to configure Pacman settings.

```shell
vim /etc/pacman.conf

# Opens the pacman.conf file in the Vim text editor for editing. You'll modify various settings in this file.

# The following lines are edited to enable or configure specific options in pacman.conf, such as enabling color output, displaying download progress bars, and specifying the number of parallel downloads.

```

Edit the following lines:
```shell
# Misc Options
# UseSyslog
# Color
# NoProgressBar
Check Space
VerbosePkgLists
# ParallelDownloads = 5
```

Uncomment this to

```shell
# Misc Options
# UseSyslog
Color
ILoveCandy
# NoProgressBar
Check Space
VerbosePkgLists
ParallelDownloads = 5
```

Now run the following commands:

```shell
pacman-key --init
pacman-key --populate archlinux

# Initializes and populates the keyring for package verification.

pacman -Sy
pacman -S archlinux-keyring

# Updates the package databases and installs the Arch Linux keyring package.
```

### Step 4.2: Update Pacman Mirror Lists

#### Step 4.2.1: Backup the Mirror List

```shell
cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup

# Creates a backup of the mirror list in case you need to restore it later.
```

#### Step 4.2.2: Rank the Mirrors

Install the pacman-contrib package and use the rankmirrors utility to rank the mirrors.

```shell
pacman -S pacman-contrib

# Installs the pacman-contrib package, which includes the rankmirrors utility.

rankmirrors -n 10 /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist

# Ranks the mirror list, optimizing it for faster package downloads. This helps you select the best mirrors based on speed and availability.

```

## Step 5: Install the Arch Linux Packages

This step uses pacstrap to install essential Arch Linux packages to the specified installation directory (/mnt).

```shell
pacstrap -i /mnt base base-devel linux linux-firmware linux-headers vim nano bash-completion

# Installs essential packages required for a basic Arch Linux system, including the base system, development tools, the Linux kernel, firmware, and various utilities for text editing and command-line usage

```

## Step 6: Generate the `fstab` file:

This step generates the fstab (file system table) to manage mounting of partitions at boot.

```shell
genfstab -U -p /mnt >> /mnt/etc/fstab

# Uses genfstab to automatically detect and generate the fstab entries for the partitions mounted under /mnt. The -U flag indicates to use UUIDs, and the -p flag ensures that the process is non-interactive

```

## Step 7: Chroot into the installation:

Enter into the installation using the arch-chroot command

```shell
arch-chroot /mnt

# This command changes the root directory to the installation at /mnt, effectively entering the newly installed system environment.

```

## Step 8: Localization and Time Zone Setup:

### Step 8.1: Localization:

Edit the `/etc/locale.gen` file, uncomment `en_US.UTF-8`, and run `locale-gen`. Set the locale in `locale.conf` and set the time zone.

```shell
# Do not fuck this up
echo LANG=en_US.UTF-8 > /etc/locale.conf

# Sets the system's default locale to en_US.UTF-8.

export LANG=en_US.UTF-8

# Exports the LANG variable to make the locale setting active

```

### Step 8.2: Timezone:

```shell
ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
# Creates a symbolic link to set the system's time zone to "Asia/Kolkata."

hwclock --systohc
# Sets the hardware clock to match the system time.
```

## Step 9: Setup Device Hostname:

Set your device's hostname

```shell
echo <hostname> > /etc/hostname

# Sets the hostname for the device by echoing the desired hostname into the /etc/hostname file.

```

## Step 10: SSD Configuration:

Enable SSD optimization by enabling the `fstrim.timer` service.

```shell
systemctl enable fstrim.timer

# Enables the fstrim.timer service, which is used to periodically trim SSDs for optimization.

```

## Step 11: Root User Password and User Configuration:

Set the root user password, create a new user, and configure sudo access for that user.

### Step 11.1: Create the root user password

```shell
passwd

# Sets the password for the root user.

```

### Step 11.2: Create the local user:

```shell
useradd -m -g users -G wheel,storage,power -s /bin/bash <username>

# Adds a new user with specific group memberships and a specified shell.

passwd <username>

# Sets the password for the newly created user.

```

### Step 11.3: Edit the sudoers file:

Edit the sudoers file:

```shell
EDITOR=nano visudo

# Opens the sudoers file for editing, using the Nano text editor.

# The commands within the sudoers file edit the configuration to allow users in the "wheel" group to run commands with elevated privileges using sudo.

```

Uncomment the %wheel line and add the following at the end of the file:

```shell
Defaults rootpw
```

### Step 12: Mount Efivars

```shell
mount -t efivarfs efivarfs /sys/firmware/efi/efivarfs

# Mounts the efivarfs file system, which is required for UEFI firmware configuration.

```

## Step 13: Install GRUB Bootloader

The commands in this step install the GRUB bootloader for UEFI systems.

```shell
# Now install the required dependencies for grub
pacman -S grub efibootmgr dosfstools os-prober mtools

# Create the EFI directory
mkdir /boot/EFI

# Install the bootloader to the disk
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB --recheck

# Copy the english locale to grub
cp /usr/share/locale/en@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo
```

## Step 14: Install NetworkManager

Install NetworkManager and enable its service.

The commands install NetworkManager, a network connection manager, and enable its service to manage network connections.

```shell
# Install networkmanager 
sudo pacman -S networkmanager

# Start the network manager service
sudo systemctl enable NetworkManager.service
```

## Step 15: Install Nvidia and Intel Drivers:

This step installs graphics drivers for both Nvidia and Intel integrated graphics.

### Step 15.1: Intel Integrated Graphics:

install the required graphics drivers

```shell
sudo pacman -Syu
sudo pacman -S mesa lib32-mesa vulkan-intel lib32-vulkan-intel

# These commands install Mesa (a graphics library), Vulkan libraries for Intel graphics, and configure mkinitcpio.conf to include the i915 module.

```

Edit the `mkinitcpio.conf` in `/etc/mkinitcpio.conf` file to add i915 to the MODULES section.

### Step 15.2: Nvidia Drivers:

```shell
sudo pacman -S nvidia libglvnd nvidia-utils opencl-nvidia lib32-libglvnd lib32-nvidia-utils lib32-opencl-nvidia nvidia-settings
```

Edit the `mkinitcpio.conf` file to add `nvidia nvidia_modeset nvidia_uvm nvidia_drm` to the MODULES section.

Finally the `mkinitcpio.conf` should look like this

```shell
MODULES=(i915 nvidia nvidia_modeset nvidia_uvm nvidia_drm)

BINARIES=()

FILES=()

HOOKS=(base udev autodetect modconf kms keyboard keymap consolefont block filesystems fsck)
```

### Step 15.3: Run `mkinitcpio`:

Run the `mkinitcpio` command

```shell
mkinitcpio -P

# Regenerates the initramfs to include the new modules and configurations for graphics drivers.

```

### Step 15.3: Edit the GRUB configuration file:

Add the following line to /etc/default/grub:

The specified lines are added to /etc/default/grub to set kernel parameters for Nvidia graphics.

```shell
GRUB_CMDLINE_LINUX="nvidia_drm.modeset=1 rd.driver.blacklist=nouveau modprobe.blacklist=nouveau"
```

now regenerate the GRUB configuration:

```shell
sudo grub-mkconfig -o /boot/grub/grub.cfg

# Regenerates the GRUB configuration to apply the new settings.

```

### Step 15.4: Pacman Hook

Add a Pacman hook to ensure Nvidia driver is recompiled on kernel updates.

Create /etc/pacman.d/hooks/nvidia.hook with the following content:

```shell
[Trigger]
Operation=Install
Operation=Upgrade
Operation=Remove
Type=Package
Target=nvidia

[Action]
Depends=mkinitcpio
When=PostTransaction
Exec=/usr/bin/mkinitcpio -P
```

## Step 16: Reboot

You have successfully installed Arch Linux. Reboot your system and enjoy your new setup.

```shell
exit

umount -R /mnt

reboot
```

## Step 17: Minimal Install Completed:

succesfully completed the mininmal install of Arch Linux now you are free to install whichever Desktop Environment or Window Manager
