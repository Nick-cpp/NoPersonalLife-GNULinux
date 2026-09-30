# NoPersonalLife Linux

---

<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/a2f3ebcc-8405-400e-8a58-c0d6d98378ff" />

---

> **The ultimate minimal distribution for those who have better things to do than touch grass—like compiling everything from source.**

---

## Overview

**NoPersonalLife Linux** is a lightweight, bare-bones Linux distribution built entirely from source. Built around the strict principles of **KISS (Keep It Simple, Stupid)** and extreme minimalism, this distribution strips away all unnecessary bloat, modern desktop clutter, and background noise to give you full, raw control over your operating system.

It isn't designed to be friendly, automatic, or cozy. It's built for those who want a completely custom, transparent environment built with their own two hands.

---

## The Philosophy

* **Extreme Minimalism:** No pre-installed bloatware, no heavy desktop suites, no hidden background services. If you didn't compile or configure it yourself, it probably isn't there.
* **KISS Principle:** Simple POSIX-compliant script architecture, clear directory structures, and zero magic abstractions.
* **Absolute Control:** Built on fundamental base components (glibc, BusyBox, custom init framework) so you know exactly what every single byte on your disk is doing.
* **Pure Performance:** Boots instantly, consumes practically zero resources, and leaves your CPU completely free for the tasks that actually matter.

---

## System Architecture

* **Base Toolchain & Core:** Built directly from source using GNU C Library (`glibc`) and `BusyBox` for core userland utilities.
* **Init System:** Custom, hand-written minimalist shell initialization framework—no complex service managers required.
* **Package Management:** Own package manager ( mspm )

---

## Who Is This For?

* Minimalists who cringe at modern OS resource consumption.
* DIY enthusiasts who prefer hand-crafted configuration files over automated setup wizards.
* Anyone who unironically enjoys spending their weekends configuring system environments.

---

<p align="center">
  <i>You will loose your personal life if you install this distribution.<br>You have been warned!!</i>
</p>

---

# 1. Partitioning the disk and mounting the partitions

## 1. Partitioning the disk using cfdisk

cfdisk /dev/your-disk

Create an 256mb EFI partition
Swap partiton ( if you want swap partition )
Root partition

## 2. Formatting partitions
```
mkfs.fat -F 32 /dev/efi-pertition
mkswap /dev/swap-partition ( if created )
mkfs.ext4 /dev/root-partition
```
## 3. Mounting partitions
```
mount /dev/root-partition /mnt
mkdir /mnt/efi
mount /dev/efi-partition /mnt/efi
swapon /dev/swap-partition ( if created )
```
```
cd /mnt
```
# 2. Downloadng and extracting the stage archive with the base system

## 1. Downloading the stage archive
```
wget https://github.com/Nick-cpp/NoPersonalLife-Linux/releases/download/release/stage.tar.xz
```
## 2. Extracting the stage archive
```
tar -xJvf stage.tar.xz
```
# 3. Chrooting into the system

## 1. Preparing for chroot
```
mount -o bind /dev /mnt/dev
mount -o bind /dev/pts /mnt/dev/pts
mount -t proc proc /mnt/proc
mount -t sysfs sysfs /mnt/sys
mount -t tmpfs tmpfs /mnt/run 
```
## 2. Chrooting
```
chroot /mnt /bin/sh
source /etc/profile
```

```
chown -R root /
chmod 1777 /tmp
chmod 4755 /bin/busybox
```
# 4. Building and installing needed packages

## 1. Downloading, building and installing bash
```
cd
```
```
wget https://ftp.gnu.org/gnu/bash/bash-5.3.tar.gz
tar -xf bash-5.3.tar.gz
```
```
cd bash-5.3/
./configure --prefix=/usr --sysconfdir=/etc --libdir=/usr/lib --without-bash-malloc
make
make install
```

```
cd
```
## 2. Sync the mspm repository
```
mspm sync
```
## 3. Configure compile options in /etc/mspm/make.conf
example configuration:
```
CFLAGS="-march=native -O2 -pipe"
CXXFLAGS="-march=native -O2 -pipe"
MAKEFLAGS="${MAKEFLAGS} -j8"
```
## 4. Installing needed packages via mspm
```
mspm install m4 flex bison pkgconf gawk ncurses bash perl busybox npl-init openssl libexpat python util-linux libtool autoconf automake gettext popt efivar efibootmgr grub
```
## 5. Installing the NPL-Linux kernel

binary kernel:
```
mspm install kernel-bin
```
compile kernel:
```
mspm install elfutils kernel
```
## 6. Installing network daemon

dhcpcd:
```
mspm install dhcpcd
```
Enabling the service:
```
echo "dhcpcd" >> /etc/npl-init/sv/DEFAULT
```
iwd:
```
mspm install cmake dbus iwd
```
or:
```
mspm install cmake-bin dbus iwd
```
Enabling the services:
```
echo "dbus" >> /etc/npl-init/sv/DEFAULT
echo "iwd" >> /etc/npl-init/sv/DEFAULT
```
## 7. Upgrade packages in your system ( optional )
```
mspm update
```
# 5. Making the system bootable

1. Installing grub the bootloader
```
mount -t efivarfs efivarfs /sys/firmware/efi/efivars
```
```
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=NoPersonalLife-Linux --recheck
grub-mkconfig -o /boot/grub/grub.cfg
```

## 2. Creating the /etc/fstab

You may write the fstab by yourself or use `npl fstab generator`:

```
mspm install fstab-gen
```

```
fstab-gen > /etc/fstab
```

# 6. Final steps

## 1. Installing a password for root
```
passwd
```
## 2. exiting and rebooting
```
exit
```
```
cd
```
```
umount /mnt/sys/firmware/efi/efivars
```
```
umount /mnt/run
umount /mnt/sys
umount /mnt/proc
umount /mnt/dev/pts
umount /mnt/dev
umount /mnt/efi
umount /mnt
```
```
reboot
```
# 7. Ending

Put your service scripts in /etc/npl-init/sv

Also use the `/etc/npl-init/sv/DEFAULT` file to setup their autostart

npl-init repository: `https://github.com/Nick-cpp/npl-init`

mspm repository: `https://github.com/Nick-cpp/mspm`

**Thanks for using NoPersonalLife Linux!**
