# FT_Linux

## Description:

First project of the Kernel branch @42. The purpose is to create a simple LFS that will be used to create your own Linux distribution on which you will work for the next projects.

---

## Resources:

[LFS](https://www.linuxfromscratch.org/lfs/view/stable/index.html)

[BLFS](https://www.linuxfromscratch.org/blfs/view/svn/index.html)

[ft_linux_basic](https://intra.42.fr/uploads/document/document/426/ft_linux_basic.sh)

[ft_linux_other](https://intra.42.fr/uploads/document/document/425/ft_linux_others.sh)

[LFS wiki](https://en.wikibooks.org/wiki/Linux_From_Scratch)

### Tasks to do:

- Disk partitioning
    - You must use at least 3 different partitions. (root, /boot and a swap partition)
- Build of cross-compilation tools
- Packages from sources
    - You must use a software for central management and configuration, like SysV or SystemD
- kernel configuration & build
    - You must use a kernel version >= 4.0. Stable or not, as long as it’s a 4.0 >= version
    - The kernel sources must be in /usr/src/kernel-$(version)
    - The kernel binary located in /boot must be named like this: vmlinuz-<linux_version>-<student_login>
- system configuration
    - The distribution hostname must be your student login
    - Your distribution must boot with a boot loader, like LILO or GRUB
- internet connection

### Summary for LFS.

1. Get a host system running. On which the LFS system will be built.
2. Partition disk for LFS.
3. Build cross toolchain on host for LFS.
4. Chang root to $LFS in order to log into LFS.
5. Build tools and bins for LFS.
6. Configure and Build Linux Kernel for LFS.
7. Setup Grub to load LFS, configuration for udev and sysV and network etc.
8. Reboot.

---

# LFS

## Host System: Debian

### Set LFS: `export LFS=/mnt/lfs`

### Partition Disk:

Here we ill create three partitions: the boot, swap and root.

To start eh program type:

`fdisk /dev/sdb` 

### Bios Boot Partition:

Using fdisk. +1M size and change type to 4(BIOS BOOT) type.

### Boot Partition:

1. Type **n** to create a new partition, fdisk will display:
    
    Command (m for help): nCommand actione extendedp primary partition (1-4)
    
2. Select **p** to select a primary partition, (/dev/hda1)
3. Then **1** to select the first primary partition.
4. Then choose the default the 1-xxxxx refers to the number of cylinders on your specific drive, which will vary according to size.
5. Hit enter for the default
    
    First cylinder (1-xxxx, default 1)
    
6. Then fdisk will display:
    
    Last cylinder or +size or +sizeM or +sizeK (1-xxxx, default xxxx):
    
    - For this part you will create a 100MB boot partition, so enter +100m
    - If you print the partition (p), it should display your partitions

### Swap Partition

1. Follow the same procedure for creating the boot partition,
    - Type **n** to create a new partition, fdisk will display
    - Select **p** to select a primary partition,
    - Then **2** to select the second primary partition. (/dev/hda2)
2. fdisk will start at the next available cylinder(196), hit  for the default.
    
    enter
    
3. Then type +512M to create a partition 512MB swap partition
4. Now, type **t** to set the partition type, **2** to select the partition you just created and then type in **82** to set the partition type to "Linux Swap".

### Root Partition:

All default and finally hit **w** to Save fdisk.

### Creating file systems(format partitioned disk to make a file system format inside the partition):

- boot and root to ext2 or ext4 file system: `mkfs.ext4(ext2) -v /dev/sdb4`
- initialize swap partition: `mkswap /dev/sdb3`
- activate swap partition: `swapon /dev/sdb3`

### Mount file systems:

`mount /dev/sbd3 $LSF`

## Download Sources

Download Sources from wget list for next step

[wget list stable](https://www.linuxfromscratch.org/lfs/downloads/stable/wget-list)

I find some Sources can not be downloaded by wget. Then go to web and download the source zip yourself and scp into the machine.

## Set Up Building Env

```bash
cat > ~/.bash_profile << "EOF"
exec env -i HOME=$HOME TERM=$TERM PS1='\u:\w\$ ' /bin/bash
EOF

cat > ~/.bashrc << "EOF"
set +h
umask 022
LFS=/mnt/lfs
LC_ALL=POSIX
LFS_TGT=$(uname -m)-lfs-linux-gnu
PATH=/usr/bin
if [ ! -L /bin ]; then PATH=/bin:$PATH; fi
PATH=$LFS/tools/bin:$PATH
CONFIG_SITE=$LFS/usr/share/config.site
export LFS LC_ALL LFS_TGT PATH CONFIG_SITE
EOF

source ~/.bash_profile
```

## ****Building the LFS Cross Toolchain and Temporary Tools****

follow the tutorial step and step. It’s well explained how to compile all.

### Cross-Toolchain

- [Binutils-2.40 - Pass 1](https://www.linuxfromscratch.org/lfs/view/stable/chapter05/binutils-pass1.html)
- [GCC-12.2.0 - Pass 1](https://www.linuxfromscratch.org/lfs/view/stable/chapter05/gcc-pass1.html)
- [Linux-6.1.11 API Headers](https://www.linuxfromscratch.org/lfs/view/stable/chapter05/linux-headers.html)
- [Glibc-2.37](https://www.linuxfromscratch.org/lfs/view/stable/chapter05/glibc.html)
- [Libstdc++ from GCC-12.2.0](https://www.linuxfromscratch.org/lfs/view/stable/chapter05/gcc-libstdc++.html)

### Cross compiling temporary tools

- [M4-1.4.19](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/m4.html)
- [Ncurses-6.4](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/ncurses.html)
- [Bash-5.2.15](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/bash.html)
- [Coreutils-9.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/coreutils.html)
- [Diffutils-3.9](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/diffutils.html)
- [File-5.44](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/file.html)
- [Findutils-4.9.0](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/findutils.html)
- [Gawk-5.2.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/gawk.html)
- [Grep-3.8](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/grep.html)
- [Gzip-1.12](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/gzip.html)
- [Make-4.4](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/make.html)
- [Patch-2.7.6](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/patch.html)
- [Sed-4.9](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/sed.html)
- [Tar-1.34](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/tar.html)
- [Xz-5.4.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/xz.html)
- [Binutils-2.40 - Pass 2](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/binutils-pass2.html)
- [GCC-12.2.0 - Pass 2](https://www.linuxfromscratch.org/lfs/view/stable/chapter06/gcc-pass2.html)

### Enter the new environment and start building tools for new system

enter Chroot enviroment. change to /mnt/lfs as root. So go into the system we are building.

```bash
chroot "$LFS" /usr/bin/env -i   \
    HOME=/root                  \
    TERM="$TERM"                \
    PS1='(lfs chroot) \u:\w\$ ' \
    PATH=/usr/bin:/usr/sbin     \
    /bin/bash --login
```

### Building additional temporary tools

- [Gettext-0.21.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter07/gettext.html)
- [Bison-3.8.2](https://www.linuxfromscratch.org/lfs/view/stable/chapter07/bison.html)
- [Perl-5.36.0](https://www.linuxfromscratch.org/lfs/view/stable/chapter07/perl.html)
- [Python-3.11.2](https://www.linuxfromscratch.org/lfs/view/stable/chapter07/Python.html)
- [Texinfo-7.0.2](https://www.linuxfromscratch.org/lfs/view/stable/chapter07/texinfo.html)
- [Util-linux-2.38.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter07/util-linux.html)

## Installing Basic System Software

- [Man-pages-6.03](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/man-pages.html)
- [Iana-Etc-20230202](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/iana-etc.html)
- [Glibc-2.37](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/glibc.html)
- [Zlib-1.2.13](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/zlib.html)
- [Bzip2-1.0.8](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/bzip2.html)
- [Xz-5.4.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/xz.html)
- [Zstd-1.5.4](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/zstd.html)
- [File-5.44](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/file.html)
- [Readline-8.2](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/readline.html)
- [M4-1.4.19](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/m4.html)
- [Bc-6.2.4](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/bc.html)
- [Flex-2.6.4](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/flex.html)
- [Tcl-8.6.13](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/tcl.html)
- [Expect-5.45.4](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/expect.html)
- [DejaGNU-1.6.3](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/dejagnu.html)
- [Binutils-2.40](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/binutils.html)
- [GMP-6.2.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/gmp.html)
- [MPFR-4.2.0](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/mpfr.html)
- [MPC-1.3.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/mpc.html)
- [Attr-2.5.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/attr.html)
- [Acl-2.3.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/acl.html)
- [Libcap-2.67](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/libcap.html)
- [Shadow-4.13](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/shadow.html)
- [GCC-12.2.0](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/gcc.html)
- [Pkg-config-0.29.2](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/pkg-config.html)
- [Ncurses-6.4](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/ncurses.html)
- [Sed-4.9](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/sed.html)
- [Psmisc-23.6](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/psmisc.html)
- [Gettext-0.21.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/gettext.html)
- [Bison-3.8.2](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/bison.html)
- [Grep-3.8](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/grep.html)
- [Bash-5.2.15](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/bash.html)
- [Libtool-2.4.7](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/libtool.html)
- [GDBM-1.23](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/gdbm.html)
- [Gperf-3.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/gperf.html)
- [Expat-2.5.0](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/expat.html)
- [Inetutils-2.4](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/inetutils.html)
- [Less-608](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/less.html)
- [Perl-5.36.0](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/perl.html)
- [XML::Parser-2.46](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/xml-parser.html)
- [Intltool-0.51.0](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/intltool.html)
- [Autoconf-2.71](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/autoconf.html)
- [Automake-1.16.5](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/automake.html)
- [OpenSSL-3.0.8](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/openssl.html)
- [Kmod-30](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/kmod.html)
- [Libelf from Elfutils-0.188](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/libelf.html)
- [Libffi-3.4.4](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/libffi.html)
- [Python-3.11.2](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/Python.html)
- [Wheel-0.38.4](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/wheel.html)
- [Ninja-1.11.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/ninja.html)
- [Meson-1.0.0](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/meson.html)
- [Coreutils-9.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/coreutils.html)
- [Check-0.15.2](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/check.html)
- [Diffutils-3.9](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/diffutils.html)
- [Gawk-5.2.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/gawk.html)
- [Findutils-4.9.0](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/findutils.html)
- [Groff-1.22.4](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/groff.html)
- [GRUB-2.06](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/grub.html)
- [Gzip-1.12](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/gzip.html)
- [IPRoute2-6.1.0](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/iproute2.html)
- [Kbd-2.5.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/kbd.html)
- [Libpipeline-1.5.7](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/libpipeline.html)
- [Make-4.4](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/make.html)
- [Patch-2.7.6](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/patch.html)
- [Tar-1.34](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/tar.html)
- [Texinfo-7.0.2](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/texinfo.html)
- [Vim-9.0.1273](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/vim.html)
- [Eudev-3.2.11](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/eudev.html)
- [Man-DB-2.11.2](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/man-db.html)
- [Procps-ng-4.0.2](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/procps-ng.html)
- [Util-linux-2.38.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/util-linux.html)
- [E2fsprogs-1.47.0](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/e2fsprogs.html)
- [Sysklogd-1.5.1](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/sysklogd.html)
- [Sysvinit-3.06](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/sysvinit.html)

## System Configuration

## Making the LFS system Bootable

### Configuring the system

/etc/fstab file

`/dev/sda4      /            ext4     defaults            1     1
/dev/sda2      /boot        ext2     defaults            1     1
/dev/sda3      swap         swap     pri=1               0     0`

### Set kernel version:

In **`make menuconfig` gui. Go general setting, set local version to `-login`**

```
General setup --->
	Local Version. [add your login here]
Processor type and features --->
   [*] Build a relocatable kernel [CONFIG_RELOCATABLE]
   [*]   Randomize the address of the kernel image (KASLR) [CONFIG_RANDOMIZE_BASE]
General setup --->
   [ ] Compile the kernel with warnings as errors [CONFIG_WERROR]
   < > Enable kernel headers through /sys/kernel/kheaders.tar.xz [CONFIG_IKHEADERS]
General architecture-dependent options  --->
   [*] Stack Protector buffer overflow detection [CONFIG_STACKPROTECTOR]
   [*]   Strong Stack Protector [CONFIG_STACKPROTECTOR_STRONG]
Device Drivers  --->
  Graphics support --->
   Frame buffer Devices --->
      <*> Support for frame buffer devices --->
   Console display driver support --->
      [*] Framebuffer Console support [CONFIG_FRAMEBUFFER_CONSOLE]
  Generic Driver Options  --->
   [ ] Support for uevent helper [CONFIG_UEVENT_HELPER]
   [*] Maintain a devtmpfs filesystem to mount at /dev [CONFIG_DEVTMPFS]
   [*]   Automount devtmpfs at /dev, after the kernel mounted the rootfs [CONFIG_DEVTMPFS_MOUNT]
Enable some additional features if you are building a 64-bit system. If you are using menuconfig, enable them in the order of CONFIG_PCI_MSI first, then CONFIG_IRQ_REMAP, at last CONFIG_X86_X2APIC because an option only shows up after its dependencies are selected.

Processor type and features --->
  [*] Support x2apic [CONFIG_X86_X2APIC]
Device Drivers --->
  [*] PCI Support ---> [CONFIG_PCI]
    [*] Message Signaled Interrupts (MSI and MSI-X) [CONFIG_PCI_MSI]
  [*] IOMMU Hardware Support ---> [CONFIG_IOMMU_SUPPORT]
    [*] Support for Interrupt Remapping [CONFIG_IRQ_REMAP]
```

### support for network

```
// connect to internet
[*] Networking support --->        [CONFIG_NET]
      Networking options --->
        <*> Packet socket          [CONFIG_PACKET]
        <*> The IPv6 Protocol ---> [CONFIG_IPV6]
//briging
[*] Networking support --->            [CONFIG_NET]
      Networking options --->
        <*/M> 802.1d Ethernet Bridging [CONFIG_BRIDGE]
// wifi
[*] Networking support  --->                                [CONFIG_NET]
  [*] Wireless  --->                                        [CONFIG_WIRELESS]
    <*/M> cfg80211 - wireless configuration API             [CONFIG_CFG80211]
    < /*/M> Generic IEEE 802.11 Networking Stack (mac80211) [CONFIG_MAC80211]
Device Drivers  --->
  [*] Network device support  --->                          [CONFIG_NETDEVICES]
    [*] Wireless LAN  --->                                  [CONFIG_WLAN]
```

### Some BLFS package Configure

```jsx
Master section --->
  Subsection --->
    [*]     Required parameter                     [CONFIG_REQU_PAR]
    <*>     Required parameter (not as module)     [CONFIG_REQU_PAR_NMOD]
    <*/M>   Required parameter (could be a module) [CONFIG_REQU_PAR_MOD]
    <*/M/ > Optional parameter                     [CONFIG_OPT_PAR]
    [ ] Incompatible parameter                     [CONFIG_INCOMP_PAR]
    < > Incompatible parameter (even as module)    [CONFIG_INCOMP_PAR_MOD]
```

### Compile Linux Kernel

Just compile as LFS says, nothing special.

### Install Grub and configure grub to load the new kernel in the next reboot

**`grub-install /dev/sda`**

copy bin to boot directory:

`cp -iv arch/x86/boot/bzImage /boot/vmlinuz-5.13.12-login`

configure:

```
cat > /boot/grub/grub.cfg << "EOF"
# Begin /boot/grub/grub.cfg
set default=0
set timeout=5

insmod ext2
set root=(hd0,2)

menuentry "GNU/Linux, Linux 6.1.11-lfs-11.3" {
        linux   /vmlinuz-6.1.11-lfs-11.3 root=/dev/sda2 ro
}
EOF
```

## Concept

### Udev (in package eudev)

udev is a generic device manager runing as a daemon on a linux system and listening to uevents the kernel sends out if a new device is initialised or a device is removed from the system.

### SysVinit

SysVinit (which will be referred to as “init” from now on) uses a run levels scheme. There are seven run levels, numbered 0 to 6. (Actually, there are more run levels, but the others are for special cases and are generally not used. See `init(8)` for more details.) Each one of the seven corresponds to actions the computer is supposed to perform when it starts up or shuts down. The default run level is 3. Here are the descriptions of the different run levels as they are implemented in LFS:

0: halt the computer

1: single-user mode

2: reserved for customization, otherwise the same as 3

3: multi-user mode with networking

4: reserved for customization, otherwise the same as 3

5: same as 4, it is usually used for GUI login (like GNOME's **gdm** or LXDE's **lxdm**)

6: reboot the computer

### bootloader (grub)

Boot loader is a program the is responsible for booting a computer.

## Setting up Network Interface and Connect to Internet

There are two ways to connect to the Internet. Using static ip to connect or use DHCP to allocate ip for you.

Both method all involve in setting `/etc/sysconfig/ifconfig.<interface, enp0s3>` and `****/etc/resolv.conf****` for setting up interface and DNS server ip.

### Static method

[link](https://www.linuxfromscratch.org/lfs/view/stable/chapter09/network.html)

here is an example of static ip address setting. Often times it might not be working because the ip value is not suitable in the network.

**ifconfig.enp0s3**

```java
ONBOOT=yes
IFACE=enp0s3
SERVICE=ipv4-static
IP=192.168.1.2
GATEWAY=192.168.1.1
PREFIX=24
BROADCAST=192.168.1.255
```

**resolve.conf**

```java
nameserver 8.8.8.8
```

### DHCP Method

[link](https://www.linuxfromscratch.org/blfs/view/svn/basicnet/dhcp.html)

This method need to install DHCP-4.4.3-P1 so that the protocol could manage the allocation of ip for your. Follow the tutorial and finishing installing the program. Then create the following files.

**ifconfig.enp0s3**

```
ONBOOT="yes"
IFACE="enp0s3"
SERVICE="dhclient"
DHCP_START=""
DHCP_STOP=""

PRINTIP="yes"
PRINTALL="yes"
```

resolve.conf will be autogenerated by dhclient.

Just reboot to test the connection to internet.
