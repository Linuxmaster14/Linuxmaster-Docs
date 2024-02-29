# 202: System Startup

## 202.1 Customising system startup (weight: 3)

{% hint style="success" %}
Candidates should be able to query and modify the behaviour of system services at various targets / run levels. A thorough understanding of the systemd, SysV Init and the Linux boot process is required. This objective includes interacting with systemd targets and SysV init run levels.

**Key Knowledge Areas:**

* Systemd
* SysV init
* Linux Standard Base Specification (LSB)

**The following is a partial list of the used files, terms and utilities:**

* /usr/lib/systemd/
* /etc/systemd/
* /run/systemd/
* systemctl
* systemd-delta
* /etc/inittab
* /etc/init.d/
* /etc/rc.d/
* chkconfig
* update-rc.d
* init and telinit
{% endhint %}

### The Linux Standard Base (LSB)

The goal of the LSB is to develop and promote a set of open standards that will increase compatibility among Linux distributions and enable software applications to run on any compliant system even in binary form. In addition, the LSB will help coordinate efforts to recruit software vendors to port and write products for Linux Operating Systems.

### Understanding Sys-V init

#### The init process

The init process or init boot is the first process that is called and used to start all other processes.

#### /etc/inittab

Defines three important items for the init process.

* The system’s default runlevel
* What processes to start, monitor, and restart if they terminate
* What actions to take when the system enters a new runlevel

#### Runveles

A runlevel is the initialization state that determines which system should be running.

| Runlevel                                | Description                                                                      |
| --------------------------------------- | -------------------------------------------------------------------------------- |
| 0 `Halt`                                | Shuts down the sysem                                                             |
| 1 `Single-user mod`                     | Does not configure network interfaces, startat daemons, or allow non-root logins |
| 2 `Multi-user mod (Without networking)` | Does not configure network interfaces or export network services                 |
| 3 `Multi-user mod (With networking)`    | Start the system normally                                                        |
| 4 `Undefined`                           | Not used / User-definable                                                        |
| 5 `Multi-user (with GUI)`               | Start the system normally with appropriate display manager                       |
| 6 `Reboot`                              | Reboot the system                                                                |

#### /etc/rc.d/ or /etc/rcN.d/

Once the default runlevel is discovered, scripts in the corresponding rc.d directory (or (rcN.d) are executed to bring the system to the desired states. The scripts are formatted as KNN or SNN where the K determines what is stopped and the S determines what is started. The number (NN) determines the order in which they are stopped or started.

#### /etc/init.d/

Contains initialization scripts for all the services on the host which are called by the kill and start scripts in `/etc/rc.d/` or `/etc/rcN.d/`.

#### chkconfig

Updates and queries runlevel information for system services

```bash
chkconfig [options]
```

<table><thead><tr><th width="174">Runlevel</th><th>Description</th></tr></thead><tbody><tr><td><code>--level</code></td><td>Specifies the run levels an operation should pertain to</td></tr><tr><td><code>--add</code></td><td>Adds a new service for management by chkconfig</td></tr><tr><td><code>--del</code></td><td>Remove a service from chkconfig management</td></tr><tr><td><code>--override</code></td><td>Change configuration for a service based on <code>/etc/chkconfig.d/</code></td></tr><tr><td><code>--list</code></td><td>List services (or service) for which chkconfig is aware</td></tr></tbody></table>

#### update-rc.d

Updates the SysV-init Script links in `/etc/runlevel.d/` (Debian)

```bash
update-rc.d [-n] [-f] name remove
update-rc.d [-n] name defaults
update-rc.d [-n] name disable|enable [S|2|3|4|5]
```

#### init

Change the current runlevel

```bash
init [options] ...
```

#### telinit

Change the current runlevel and allows for additional environment variables.

```bash
telinit [options] ... runlevel
```

#### runlevel

Display the current and previous runlevel

```bash
runlevel [options] ... [UTMP]
```

```bash
[root@linuxmaster ~]# cat /etc/inittab
...

id:5:initdefault:

# System initialization.
si::sysinit:/etc/rc.d/rc.sysinit

l0:0:wait:/etc/rc.d/rc 0
l1:1:wait:/etc/rc.d/rc 1
l2:2:wait:/etc/rc.d/rc 2
l3:3:wait:/etc/rc.d/rc 3
l4:4:wait:/etc/rc.d/rc 4
l5:5:wait:/etc/rc.d/rc 5
l6:6:wait:/etc/rc.d/rc 6

...
```

### Systemd Boot Process

**Systemd vs. SysVinit:** One of the biggest differences between `systemd` and `sysvinit` is the way that they start services. `sysvinit` starts service in a defined order, one at a time, and `systemd` allows for parallel startup of services. This allows `systemd` to start services simultaneously, based on dependencies or when a service is needed.

#### Systemd Unit Files

A unit file is a plain text init-style file that encodes information about a service, a socket, a device, a mount point, an automount point, a swap file or partition, a startup target, a watched file system path, a timer-controlled and supervised by `systemd`, a resource management slice, or a group of externally created processes.

Unit files are located in three different directories and operate based on precedence (greatest to least):

* `/etc/systemd/`: Unit files created with `systemctl` enable and unit files added for extending service.
* `/run/systemd/`: Unit files created at runtime.
* `/usr/lib/systemd`: Unit files distributed with installed RPM packages.

Unit files contain three main configuration sections:

* `[Unit]`: Contains generic options that are independent of the unit type, like the description, the unit’s behavior, and dependencies on other units.
* `[Unit_type]`: This heading reflects the unit type and the directives are particular to that kind of unit (type-specific).
* `[Install]`: Contains directives used by the `systemctl` `enable` and `disable` commands.

```bash
[root@linuxmaster ~]# cat /usr/lib/systemd/system/sshd.service
[Unit]
Description=OpenSSH server daemon
Documentation=man:sshd(8) man:sshd_config(5)
After=network.target sshd-keygen.service
Wants=sshd-keygen.service

[Service]
Type=notify
EnvironmentFile=/etc/sysconfig/sshd
ExecStart=/usr/sbin/sshd -D $OPTIONS
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
RestartSec=42s

[Install]
WantedBy=multi-user.target
```

#### Systemd Services

List installed unit files and their enablement state. e.g.

```bash
systemctl list-unit-files
```

List installed service files and their enablement state.

```bash
systemctl list-unit-files --type service
```

Reload `systemd` manager configuration. This will rerun all generators, reload all unit files, and recreate the entire dependency tree.

```bash
systemctl daemon-reload
```

Find overridden configuration files. The command allows for an optional prefix and suffix, which must be one of the directories containing configuration files: `/etc`, `/run`, or `/usr/lib`. Otherwise, it shows all overriding files.

```bash
systemd-delta
```

#### Systemd Targets

Targets or target units are used to link a group of units together to describe a desired system state. This can contain services or even other targets which have their own groups of services.

In addition to the dependencies mentioned in the unit file, a target can also have a “.wants” directory that links to units that will be started along with the target.

```bash
[root@linuxmaster ~]# cat /usr/lib/systemd/system/multi-user.target
[Unit]
Description=Multi-User System
Documentation=man:systemd.special(7)
Requires=basic.target
Conflicts=rescue.service rescue.target
After=basic.target rescue.service rescue.target
AllowIsolate=yes
```

Return the default target to boot into. This returns the target unit name `default.target` in aliases (symlinked).

```bash
systemctl get-default
```

Set the default target to boot into. This sets (symlinks) the `default.target` alias to the given target unit.

```bash
systemctl set-default
```

Start the unit specified on the command line and its dependencies and stop all others.

```bash
systemctl isolate
```

## 202.2 System recovery (weight: 4)

{% hint style="success" %}
Candidates should be able to properly manipulate a Linux system during both the boot process and during recovery mode. This objective includes using both the init utility and init-related kernel options. Candidates should be able to determine the cause of errors in loading and usage of bootloaders. GRUB version 2 and GRUB Legacy are the bootloaders of interest. Both BIOS and UEFI systems are covered.



**Key Knowledge Areas:**

* BIOS and UEFI
* NVMe booting
* GRUB version 2 and Legacy
* grub shell
* boot loader start and hand off to kernel
* kernel loading
* hardware initialisation and setup
* daemon/service initialisation and setup
* Know the different boot loader install locations on a hard disk or removable device.
* Overwrite standard boot loader options and using boot loader shells.
* Use systemd rescue and emergency modes.

**The following is a partial list of the used files, terms and utilities:**

* mount
* fsck
* inittab, telinit and init with SysV init
* The contents of /boot/, /boot/grub/ and /boot/efi/
* EFI System Partition (ESP)
* GRUB
* grub-install
* efibootmgr
* UEFI shell
* initrd, initramfs
* Master boot record
* systemctl
{% endhint %}

### BIOS, UEFI, and NVME Booting

#### BIOS (Basic Input/Output System):

* Firmware that is stored in a chip on the motherboard
* Detects and maps connected devices Perform a power-on-self-test (POST) of the system hardware
* Runs in 16-bit processor mode
* Support devices using the Master Boot Record (MBR)
* Limited to drives of 2.1 TB or less
* Initiates the boot sequence from a connected device

#### UEFI (Uni Extensible Firmware interface)

* Support both the Master Boot Record (MBR) and the GUID Partition Table (GPT)
* Support drives greater than 2.1 TB
* Runs in 32-bit or 64-bit mode
* Support secure boot
* Support networking

#### UEFI Services

* UEFI boot manager: A new method for interfacing between the operating system and the firmware. It is composed of data tables containing platform-related information as well as boot and runtime functions that are available to the operating system and the bootloader.
* EFI System Partition (ESP): This is a special GTP partition with configurations and binaries normally located at `/efi` or /`boot/efi`.
* UEFI shell: This shell can be used to launch EFI applications (including bootloaders) and gain information about the system or the firmware.
* `efibootmgr`: A userspace application used to modify the Extensible Firmware Interface (EFI) Boot Manager.

#### NVMe (Non-Volatile Memory Express)

* An interface specification for accessing non-volatile memory storage media
* Support for NVMe was added in the mainline kernel in version 3.3
* Booting from NVMe devices a motherboard with NVMe support.

### GRUB and GRUB2 Bootloaders

#### The Linux Boot Process

1. The BIOS/UEFI: Validates that the hardware is functioning properly and locates a bootable device
2. The Bootloader: Loads the kernel and `initramfs` images
3. The Kernel: Configures and system, mount the root file system and runs the main initialization process
4. Post Kernel/Systemd: Spawns the system and user-level processes

#### The GRUB Bootloader

* The GRUB Bootloader: The Grand Unified Bootloader (GRUB) takes over from the bios at boot time and then loads the kernel as well as the `initramfs` (or `initrd`) which supports the kernel booting the system.
* The `/boot` directory: This directory contains the kernel and `initrd` images as well as the `/grub` directory.
* `grub.conf`: This is the main configuration file for GRUB. It allows you to change things like the kernel boot order, menu timeout, menu image, and even supplying a password for editing the kernel arguments at boot.
* The GRUB shell: Severe system issues can lead to booting into the GRUB shell. This can also be done manually from the GRUB menu by pressing "c".

```bash
[root@linuxmaster ~]# cat /boot/grub/grub.conf
default=0
timeout=5
splashimage=(hd0,0)/grub/splash.xpm.gz
hiddenmenu
title CentOS (2.6.18-398.e15)
	root (hd0,0)
	kernel /vmliuz-2.6.18.398.e15 ro root=/dev/VolGroup00/LogVol00
	initrd /initrd-2.6.18.398.e15.img
```

#### The GRUB2 Bootloader

* The GRUB2 Bootloader: GRUB2 is version two of the legacy GRUB bootloader. It brings several improvements over GRUB, including support for UUIDs and a more programmatic approach to generating the GRUB configuration file.
* /`etc/grub.d` and `/etc/default/grub`: `/etc/grub.d` contains template files and `/etc/default/grub` contains settings, both of which are used to create the `grub.cfg` configuration file.
* `grub2-mkconfig` (CentOS) and `grub-mkconfig` (Ubuntu): These commands are used to generate a new `grub.cfg` by reading the contents of `/etc/grub.d` and `/etc/defaults/grub.`

```bash
grub-mkconfig [option]
```

* `grub2-install` (CentOS) and `grub-install` (Ubuntu): Install GRUB on a device.

```bash
grub-install [options] [install_device]
```

```bash
[root@linuxmaster ~]# ls -al /etc/grub
drwx------.   2 root root  4096 May 20 22:56 .
drwxr-xr-x. 104 root root 16384 Aug 19 19:00 ..
-rwxr-xr-x.   1 root root  8702 May 20 17:28 00_header
-rwxr-xr-x.   1 root root  1043 Mar 22  2019 00_tuned
-rwxr-xr-x.   1 root root   232 May 20 17:28 01_users
-rwxr-xr-x.   1 root root 10781 May 20 17:28 10_linux
-rwxr-xr-x.   1 root root 10275 May 20 17:28 20_linux_xen
-rwxr-xr-x.   1 root root  2559 May 20 17:28 20_ppc_terminfo
-rwxr-xr-x.   1 root root 11169 May 20 17:28 30_os-prober
-rwxr-xr-x.   1 root root   214 May 20 17:28 40_custom
-rwxr-xr-x.   1 root root   216 May 20 17:28 41_custom
-rw-r--r--.   1 root root   483 May 20 17:28 README
```

```bash
[root@linuxmaster ~]# cat /etc/default/grub
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="consoleblank=0 systemd.show_status=true elevator=noop no_timer_check console=tty1 console=ttyS0,115200n8"
GRUB_DISABLE_RECOVERY="true"
```

### Recovering Filesystems and Working with Rescue and Emergency Targets

* `fsck`: Check and repair a Linux filesystem

Check and optionally repairs one or more Linux filesystems. A filesystem can be a device name, a mount point, an ext2 label, or UUID specifier. If no filesystem is given and the -A option is not specified, fsck will default to checking filesystems in /`etc/fstab`.

The `fsck` the command is a frontend for the various file system checkers (e.g. `fsck.fstype`)

```bash
fsck [options] [filesystem] -- [fs-specific-options]
```

| Option | Purpose                                             |
| ------ | --------------------------------------------------- |
| `-t`   | Specify the type of filesystem to be checked        |
| `-A`   | Check filesystems in /etc/fstab                     |
| `-C`   | Display progress bars for the check                 |
| `-N`   | Perform a dry run                                   |
| `-a`   | Automatically repair a filesystem                   |
| `-n`   | print errors but do not repair                      |
| `-r`   | Interactivity repair filesystems                    |
| `-y`   | Always attempt to repair a filesystem automatically |

### Systemd Rescue and Emergency Targets

#### rescue.target

A special target unit pulls in the base system (including system mounts) and spawns a rescue shell. Isolate to this target in order to administer the system in single-user mode with all file systems mounted but with no services running, except for the most basic.

* Attempts to mount all local filesystems and start basic services
* Does not activate networking
* Does not allow logins
* Enter the resuce target by running `systemctl isolate resuce.target` or adding `system.unit=rescue.target` or rescue to the kernel boot parameters

#### emergency.target

A special target unit starts an emergency shell on the main console. This target does not pull in other services or mounts. It is the most minimal version of starting the system in order to acquire an interactive shell.

* Does not mount local filesystems (except `/root`)
* Mounts the root filesystem as read-only
* Provides a basic shell and starts very basic services
* Enter the emergency target by running `systemctl isolate emergency.target` or adding `system.unit=emergency.target` or emergency to the kernel boot parameters

## 202.3 Alternate Bootloaders (weight: 2)

{% hint style="success" %}
Candidates should be aware of other bootloaders and their major features.

\
**Key Knowledge Areas:**

* SYSLINUX, ISOLINUX, PXELINUX
* Understanding of PXE for both BIOS and UEFI
* Awareness of systemd-boot and U-Boot

**The following is a partial list of the used files, terms and utilities:**

* syslinux
* extlinux
* isolinux.bin
* isolinux.cfg
* isohdpfx.bin
* efiboot.img
* pxelinux.0
* pxelinux.cfg/
* uefi/shim.efi
* uefi/grubx64.efi
{% endhint %}

### SYSLINUX Project

#### SYSLINUX

A boot loader for the Linux operating system that runs on an MS-DOS/Windows FAT filesystem. In simplifies first-time installation of Linux and creation of rescue and othe special purpose boot disks.

The SYSLINUX configuration files is syslinux.cfg and is searched for in the following order:

* `/boot/syslinux/syslinux.cfg`
* `/syslinux/syslinux.cfg`
* `/syslinux.cfg`

Key configuration options:

* DEFAUT - Default boot target
* LABEL - Name of target
* SAY - Text to print when chosen
* KERNEL - Name of the kernel file
* APPEND - Additional kernel options

```bash
syslinux --directory /boot/syslinux/ -- install /dev/sdb1 #Installation Example
```

#### EXTLINUX

A SYSLINUX variant that boots from a Linux filesystem. Originally, only supported EXT filesystems.

* Supports many filesystem types: EXT, FAT, NTFS, Btrfs, XFS and UFS/FFS
* Installer runs on a mounted filesystem rather than a raw devices. Ex: `extlinux --install /mount/point`
* The configuration file is `extlinux.conf` and shoud be in the same directory as the EXTLINUX installation
* The configuration options are the same as SYSLINUX

#### ISOLINUX

A SYSLINUX variant that creates a bootable CD-ROM for ISO 9660 filesystems.

* All packages and binaries should be places in a directory called `CD_root`.
* `isolinux.bin` and `isolinux.cfg` should be placed in a directory called `isolinux` within `CD_root`.
* Isohybrid allows ISOLINUX to be installed on image files or block devices (the main binary is `isohdpfx.bin`)
* The `mkisofs` command is used to create the bootable ISO.

#### PXELINUX

Used for booting from a network server that conforms to the Preboot Execution Environment (PXE) specification.

* Requires the installation of SYSLINUX on a TFTP server.
* On the TFTP server, create the directory `tftpboot`.
* Copy `pxelinux.0` (the bootloader) and `idlinux.c32` (the library) to the `tftpboot` directory.
* Create a directory called `pxelinux.cfg` within the `tftpboot` directory.
* Create a configuration file in the `pxelinux.cfg` for each system by using the client UUID, MAC address, IPv4 address, or lowercase `default` for default configuration.

### PXE Booting for BIOS/UEFI

PXE stands for Preboot Execution Environment, and it allows systems to boot over a local area network.

* Support for PXE must be present in the network card and the BIOS/UEFI must be configured to allow it.
* Once configured, the system will receive an IP address from a DHCP server and the necessary files for booting from a TFTP server.
* `shim.efi` is the initial UEFI bootloader used in a secure boot environment.
* `grubx64.efi` is the full grub bootloader for EFI systems using PXE booting.
* `shim.efi` (if using secure boot) and `grubx64.efi` should be copied to a directory on the TFTP server.

### Systemd-boot

A UEFI boot manager that executes configured EFI images.

* Operates on the EFI System Partition (ESP)
* Operates on the EFI System Partition (ESP)
* All configuration files and boot images must reside on the ESP (e.g. kernels, initrds, etc.)
* In order to execute a kernel as an EFI image, it myst be built with `CONFIG_EFI_STUB`
* Uses a main configuration file to set the timeout and the default boot entry
* Each boot entry requires a configuration file that accepts a few basic keywords: title, options, efi, linux, initrd, etc.

### U-boot

Das U-boot or the Universal Boot Loader is an open-source primary boot loader for embedded systems.

* A highly customizable bootloader, which makes it very popular with bot embedded systems and IoT devices
* Can be split into stages to accommodate size constraints
* Supports several different filesystems (e.g. ext, FAT, btrf, etc.)

## Sample Questions

**1.** Which special target disables logins and networking while attempting to mount local filesystems?

* [ ] emergency
* [ ] multi-user
* [ ] graphical
* [x] rescue

**2.** What option for fsck automatically attempts to repair a filesystem?

* [ ] \-A
* [ ] \-t
* [x] \-y
* [ ] \-r

**3.** What bootloader has become popular for embedded systems and IoT devices?

* [ ] Systemd-boot
* [ ] SYSLINUX
* [ ] PXELINUX
* [x] U-Boot

**4.** What directory has the greatest precedence for unit files in Systemd?

* [ ] /etc/run/systemd
* [ ] /usr/lib/systemd
* [ ] /run/systemd
* [x] /etc/systemd

**5.** What command shows overridden configuration files in Systemd?

* [x] systemd-delta
* [ ] systemd show-diff
* [ ] systemd-reload
* [ ] systemctl get-default

**6.** What processor mode does the BIOS run?

* [ ] 8-bit
* [ ] 64-bit
* [x] 16-bit
* [ ] 32-bit

**7.** What commands are used to modify services for runlevels in SysVinit? (Choose two answers.)

* [x] chkconfig
* [ ] runlevel
* [x] update-rc.d
* [ ] init

**8.** What is the main configuration file for the legacy GRUB bootloader?

* [ ] grub.cfg
* [ ] default.grub
* [ ] /etc/default/grub
* [x] grub.conf

**9.** What file defines the default runlevel in SysVinit systems?

* [ ] update-rc.d
* [ ] rc.sysvinit
* [ ] init.d
* [x] inittab

**10.** In what version of the Linux Kernel was NVMe first supported?

* [ ] Version 2.6
* [ ] Version 3.4
* [ ] Version 3.3
* [ ] Version 4.3

**11.** Which of the following is a partitioning system supported by UEFI?

* [ ] MPT
* [x] GPT
* [ ] MBR
* [ ] PGP

**12.** What directory and configuration file are used to build the grub.cfg for GRUB2? (Choose two.)

* [ ] /etc/rc.d
* [x] /etc/default/grub
* [x] /etc/grub.d/
* [ ] /boot/grub/grub.conf

**13.** Which of the following is the name of a joint project whose goal is to promote a set of standards to increase compatibility among Linux distributions?

* [ ] The Linux Foundation
* [x] The Linux Standard Base (LSB)
* [ ] The Portable Operating System Interface (POSIX) specification
* [ ] The Filesystem Hierarchy Standard (FHS)

**14.** What Linux service management and initialization system starts processes in parallel?

* [x] Systemd
* [ ] systemd-delta
* [ ] LinuxInit
* [ ] SysVinit

**15.** Which of the following are variants of SYSLINUX? (Choose all that apply.)

* [ ] NETLINUX
* [x] PXELINUX
* [x] ISOLINUX
* [x] EXTLINUX

**16.** What program does the workstation firmware start at boot time?

* [x] Bootloader
* [ ] The `init` program
* [ ] The Windows OS
* [ ] The `mount` command
* [ ] The `telinit` program

**17.** Where does the firmware first look for a Linux bootloader program?

* [ ] The `/boot/grub` folder
* [x] The Master Boot Record (MBR)
* [ ] The `/var/log` folder
* [ ] A boot partition
* [ ] The `/etc` folder

**18.** What Linux command lets you examine the most recent boot messages?

* [ ] `fsck`
* [ ] `init`
* [ ] `mount`
* [x] `dmesg`
* [ ] `chkconfig`

**19.** What folder do most Linux distributions use to store boot logs?

* [ ] /etc
* [ ] /var/message
* [x] /var/log
* [ ] /boot
* [ ] /proc

**20.** Where does the workstation BIOS attempt to find a bootloader program? (Select all that apply.)

* [x] An internal hard drive
* [x] An external hard drive
* [x] A DVD drive
* [x] A USB memory stick
* [x] A network server

**21.** Where is the Master Boot Record located?

* [x] The first sector of the first hard drive on the system
* [ ] The boot partition of any hard drive on the system
* [ ] The last sector of the first hard drive on the system
* [ ] Any sector on any hard drive on the system
* [ ] The first sector of the second hard drive on the system

**22.** Where is the EFI System Partition (ESP) stored on Linux systems?

* [ ] `/boot`
* [ ] `/etc`
* [ ] `/var`
* [x] `/boot/efi`
* [ ] `/boot/grub`

**23.** What file extension do UEFI bootloader files use?

* [ ] `.cfg`
* [ ] `.uefi`
* [ ] `.lst`
* [ ] `.conf`
* [x] `.efi`

**24.** Which was the first bootloader program used in Linux?

* [ ] GRUB Legacy
* [x] LILO
* [ ] GRUB2
* [ ] SYSLINUX
* [ ] ISOLINUX

**25.** Where are the GRUB Legacy configuration files stored?

* [x] `/boot/grub`
* [ ] `/boot/efi`
* [ ] `/etc`
* [ ] `/var`
* [ ] `/proc`

**26.** Where are GRUB2 configuration files stored? (Select all that apply.)

* [ ] `/proc`
* [x] `/etc/grub.d`
* [x] `/boot/grub`
* [ ] `/boot/efi`
* [ ] `/var`

**27.** What command must you run to generate the GRUB2 grub.cfg configuration file?

* [ ] `chkconfig`
* [ ] `update-rc.d`
* [x] `grub-mkconfig`
* [ ] `grub-install`
* [ ] `init`

**28.** What program does the kernel use to start other programs?

* [ ] GRUB2
* [ ] systemctl
* [ ] telinit
* [x] init
* [ ] BIOS

**29.** Which configuration file contains the SysV default runlevel?

* [ ] `/etc/init.d`
* [x] `/etc/inittab`
* [ ] `/etc/grub.d`
* [ ] `/etc/rc.d`
* [ ] `/boot/grub.cfg`

**30.** What runlevel is the default for Debian-based systems?

* [ ] 0
* [ ] 1
* [ ] 6
* [ ] 5
* [x] 2

**31.** What command would you use to change the current runlevel? (Select all that apply.)

* [x] `telinit`
* [ ] `chkconfig`
* [ ] `update-rc.d`
* [x] `init`&#x20;
* [ ] `dmesg`

**32.** What command displays the runlevels in which a program will be started?

* [x] `chkconfig`
* [ ] `init`
* [ ] `dmesg`
* [ ] `update-rc.d`
* [ ] `telinit`

**33.** What command do Debian systems use to set the runlevels for programs?

* [ ] `chkconfig`
* [x] `update-rc.d`
* [ ] `init`
* [ ] `telinit`
* [ ] `dmesg`

**34.** What program allows you to fix corrupt hard drive partitions?

* [ ] `mount`
* [ ] `umount`
* [x] `fsck`
* [ ] `init`
* [ ] `telinit`

**35.** Which command allows you to append a partition to the virtual directory on a running Linux system?

* [x] `mount`
* [ ] `umount`
* [ ] `fsck`
* [ ] `dmesg`
* [ ] `init`

**36.** Which of the following init systems comes along with an own UEFI boot loader?

* [x] systemd
* [ ] SysVinit
* [ ] Upstart
* [ ] OpenRC
* [ ] launchd

**37.** What effect does the `–f` option to the `update-rc.d` command have on files in the `/etc/rcX.d/` and `/etc/init.d/` directories?

* [ ] It will force the removal of the symlinks in `/etc/rcX.d/` even when the links are read only
* [ ] It will remove both the symlinks in `/etc/rcX.d/` and the init script in `/etc/init.d/`
* [ ] It will remove the init script in `/etc/init.d/` and the symlinks in `/etc/rcX.d/` and update the package information of installed files
* [x] It will force the removal of symlinks in `/etc/rcX.d/` even if the corresponding init script still exists in `/etc/init.d/`

**38.** Which command is used to install a GRUB boot loader into the master boot record?

* [x] grub-install
* [ ] grub-mkconfig
* [ ] grub-install-mbr
* [ ] grub-glue-mbr
* [ ] grub-mbr-setup

**39.** Which directory contains system-specific systemd unit files? (Specify the full path to the directory.)

**Answer:** `/lib/systemd/system`

**40.** A Linux server running systemd booted to `rescue.target` for maintenance. Which commands are used to restore the server to its usual target? (Choose two.)

* [ ] `telinit 0`
* [x] `systemctl default`
* [ ] `sync`
* [ ] `systemctl emergency`
* [x] `systemctl reboot`
