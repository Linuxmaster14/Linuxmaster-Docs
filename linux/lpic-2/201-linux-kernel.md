# 201: Linux Kernel

## 201.1 Kernel components (weight: 2)

{% hint style="success" %}
Candidates should be able to utilise kernel components that are necessary to specific hardware, hardware drivers, system resources and requirements. This objective includes implementing different types of kernel images, understanding stable and longterm kernels and patches, as well as using kernel modules.\
\
**Key Knowledge Areas:**

* Kernel 2.6.x, 3.x and 4.x documentation

**The following is a partial list of the used files, terms and utilities:**

* /usr/src/linux/
* /usr/src/linux/Documentation/
* zImage
* bzImage
* xz compression
{% endhint %}

### The Kernel Source Tree and Compressed Kernel Images

* The Kernel Source Tree: The default location for the kernel source tree is in `/usr/src` (for CentOS, this is `/usr/src/kernels`). The kernel documentation is in the Documentation directory within the source tree. The 00-INDEX in the documentation directory gives list of the various files and directories as well as a short description.

The two main types of kernel images are `zImage` and `bzImage`. Once compiled, the compressed image is copied to the boot directory and renamed to vmlinuz followed by the kernel version and architecture.

* **zImage** was used on older systems and designed to fit within the limited amount of memory that was available (the first 640 KB, AKA low memory). The image was compressed down to 512 KB using gzip compression.
* **bzImage** stands for big zImage and takes advantage for the higher memory available (around 1 MB, AKA high memory). This change happened around version 2 of the Linux kernel and by version 2.6 bzip2 was the standard compression used.

```bash
[root@linuxmaster ~]# ls /boot/
...
vmlinuz-3.10.0-1160.71.1.el7.x86_64
vmlinuz-3.10.0-1160.76.1.el7.x86_64
...
```

* `xz` Compression: As of 2013, kernel.org started providing kerel archives in xz compression rather than bzip2 (older archives will remain in bzip2). xz used the LZMA2 compression algorithm and has become a standard beacause of its efficiency and lower compression ratio.

## 201.2 Compiling a Linux kernel (weight: 3)

{% hint style="success" %}
Candidates should be able to properly configure a kernel to include or disable specific features of the Linux kernel as necessary. This objective includes compiling and recompiling the Linux kernel as needed, updating and noting changes in a new kernel, creating an initrd image and installing new kernels.

\
**Key Knowledge Areas:**

* /usr/src/linux/
* Kernel Makefiles
* Kernel 2.6.x, 3.x and 4.x make targets
* Customize the current kernel configuration.
* Build a new kernel and appropriate kernel modules.
* Install a new kernel and any modules.
* Ensure that the boot manager can locate the new kernel and associated files.
* Module configuration files
* Use DKMS to compile kernel modules.
* Awareness of dracut

**The following is a partial list of the used files, terms and utilities:**

* mkinitrd
* mkinitramfs
* make
* make targets (all, config, xconfig, menuconfig, gconfig, oldconfig, mrproper, zImage, bzImage, modules, modules\_install, rpm-pkg, binrpm-pkg, deb-pkg)
* gzip
* bzip2
* module tools
* /usr/src/linux/.config
* /lib/modules/kernel-version/
* depmod
* dkms
{% endhint %}

### Installing a Linux Kernel and Kernel Modules

```bash
# Compile the Linux Kernel
make menuconfig
make bzImage

# Building and Installing Kernel Modules
make modules
make modules_install

# Installing the Kernel and Generating the initramfs
cp /usr/src/kernel_tree/arch/x86/boot/bzImage /boot
mv bzImage kernel_image

mkinitrd initrd_image kernel_version #CentOS
mkinitramfs -o initrd_image kernel_version #Ubuntu
```

### Understanding Make Targets and Associated Utilities

`make` will execute the commands in the makefile to one or more target names. If the makefile isn't provided, then the make command will look for the makefiles starting with `gnumakefile`, `makefile`, and `Makefile` (`Makefile` is the suggested usage)

| Target            | Purpose                                                      |
| ----------------- | ------------------------------------------------------------ |
| `all`             | Builds all targets marked with an asterisk                   |
| `config`          | Update current config utilizing a line-oriented program      |
| `xconfig`         | Update current config utilizing a ncurses menu-based program |
| `menuconfig`      | Update current config utilizing a menu-based program         |
| `gconfig`         | Update current config utilizing a GTK+ based frontend        |
| `oldconfig`       | Update current config utilizing a provided `.config` as base |
| `mrproper`        | Remove all generated files + config + various backup files   |
| `zImage`          | Compressed kernel image (`arch/x86/boot/zImage`)             |
| `bzImage`         | Compressed kernel image (`arch/x86/boot/bzImage`)            |
| `modules`         | Build all modules                                            |
| `modules_install` | Install all modules to `INSTALL_MOD_PATH` (default: /)       |
| `rpm_pkg`         | Build both source and binary RPM kernel packages             |
| `binrpm_pkg`      | Build only the binary kernel RPM package                     |
| `dep_pkg`         | Build both source and library deb kernel packages            |

* `depmod`: `depmod` is short for dependency modules and is used to generate a dependency list file, `modules.dep`, and associated map files. `depmod` analyzes the `/lib/modules/kernel_version` directory and creates the dependency file `modules.dep`, as well as a binary hash version called `modules.dep.bin`. Another important file created by `depmod` is `modules.symbols` (and `modules.symbols.bin`). This file contains a dependency list of symbols - uniqe service provided by modules - that can be used by other modules.

```bash
[root@linuxmaster ~]# ls -n /path/to/your-kernel-module.ko /lib/modules/`uname -r`

[root@linuxmaster ~]# depmod -a
```

* `dkms`: Dynamic Kernel Module Support (DKMS) provides a framework for generating kernel modules whose sources generally reside outside of the kernel source tree.

Prepare a module for DKMS:

* Download and extract the kernel module
* Create or modify the `dkms.conf` file
* Copy the kernel module source code to `/usr/src`

Add, build, and install with DKMS:

```bash
dkms add -m <MODULE-NAME>

dkms build -m <MODUKLE-NAME> -v <MODULE-VERSION>

dkms install -m <MODULE-NAME> -v <MODULE-VERSION>
```

## 201.3 Kernel runtime management and troubleshooting (weight: 4)

{% hint style="success" %}
Candidates should be able to manage and/or query a 2.6.x, 3.x, or 4.x kernel and its loadable modules. Candidates should be able to identify and correct common boot and run time issues. Candidates should understand device detection and management using udev. This objective includes troubleshooting udev rules.

\
**Key Knowledge Areas:**

* Use command-line utilities to get information about the currently running kernel and kernel modules.
* Manually load and unload kernel modules.
* Determine when modules can be unloaded.
* Determine what parameters a module accepts.
* Configure the system to load modules by names other than their file name.
* /proc filesystem
* Content of /, /boot/ , and /lib/modules/
* Tools and utilities to analyze information about the available hardware
* udev rules

**The following is a partial list of the used files, terms, and utilities:**

* /lib/modules/kernel-version/modules.dep
* module configuration files in /etc/
* /proc/sys/kernel/
* /sbin/depmod
* /sbin/rmmod
* /sbin/modinfo
* /bin/dmesg
* /sbin/lspci
* /usr/bin/lsdev
* /sbin/lsmod
* /sbin/modprobe
* /sbin/insmod
* /bin/uname
* /usr/bin/lsusb
* /etc/sysctl.conf, /etc/sysctl.d/
* /sbin/sysctl
* udevmonitor
* udevadm monitor
* /etc/udev/
{% endhint %}

### Working with Kernel Modules

* `uname`: A command line utility that prints basic information about the system. The most common use is to determine the processor architecture, the system hostname, and the kernel version of the system.

Without any options, `uname` prints kernel name as if the `-s` option were used.

```bash
uname [OPTIONS]
```

<table><thead><tr><th width="232">Option</th><th>Description</th></tr></thead><tbody><tr><td><code>-a</code></td><td>Print all information</td></tr><tr><td><code>-s</code></td><td>Print the kernel name</td></tr><tr><td><code>-n</code></td><td>Print the network node hostname</td></tr><tr><td><code>-r</code></td><td>Print the kernel release</td></tr><tr><td><code>-v</code></td><td>Print the kernel version</td></tr><tr><td><code>-m</code></td><td>Print the machine hardware name</td></tr><tr><td><code>-p</code></td><td>Print the processor type or "unknown"</td></tr><tr><td><code>-i</code></td><td>Print the hardware platform or "unknown"</td></tr><tr><td><code>-o</code></td><td>Print the operation system</td></tr></tbody></table>

* `dmesg`: Print or control the kernel ring buffer. By default, it shows all the messages in the kernel ring buffer.

```bash
dmesg [OPTIONS]
```

<table><thead><tr><th width="232">Option</th><th>Description</th></tr></thead><tbody><tr><td><code>-C</code></td><td>Clear the ring buffer</td></tr><tr><td><code>-c</code></td><td>Clear the ring buffer after printing the contents</td></tr><tr><td><code>-f</code></td><td>Restrict the output to a give list of facilities</td></tr><tr><td><code>-H</code></td><td>Enable human-readable format</td></tr><tr><td><code>-k</code></td><td>Print kernel messages</td></tr><tr><td><code>-l</code></td><td>Restrict output to the given list of levels</td></tr><tr><td><code>-T</code></td><td>Print human-readable timestamps</td></tr><tr><td><code>-u</code></td><td>Print userspace messages</td></tr></tbody></table>

* `lsmod`: A trivial program that nicely formats the contents of the `/proc/modules` file, showing what kernel modules are currently loaded.
* `modinfo`: Shows information about a kernel module. By default, `modinfo` lists each attribute of the module in a `filename: value` format. If the module name is not a filename, then `/lib/modules/kernelversion` is searched.

```bash
modinfo -O -F field -k kernel modulename [filename]
```

<table><thead><tr><th width="145">Option</th><th>Description</th></tr></thead><tbody><tr><td><code>-O</code></td><td>Use ASCII zero characters to separate field values instead of new lines</td></tr><tr><td><code>-F</code></td><td>Only print field value specified</td></tr><tr><td><code>-k</code></td><td>Provide information about a kernel other than the running one</td></tr></tbody></table>

* `insmod`: A simple program to insert a kernel module into the Linux kernel. It is recommended to use `modprobe`, which is a more sophisticated command.

```bash
insmod [FILENAME] [MODULE_OPTIONS]
```

* `rmmod`: A simple program to remove a module from the Linux kernel. It is recommended to use `modprobe` with the `-r` option instead.

```bash
rmmod [OPTIONS] [MODULE_NAME]
```

<table><thead><tr><th width="145">Option</th><th>Description</th></tr></thead><tbody><tr><td><code>-v</code></td><td>Use verbose output</td></tr><tr><td><code>-f</code></td><td>Force the removal of a module</td></tr><tr><td><code>-w</code></td><td>Wait until the module is not in use</td></tr><tr><td><code>-s</code></td><td>Send errors to syslog instead of the standard error</td></tr></tbody></table>

* `modprobe`: A program used to add and remove modules from the Linux kernel. `modprobe` intelligently adds and removes modules by considering module dependencies. Configuration added to `/etc/modprobe.d/` or `/etc/modprobe.conf` are considered when the command is executed.

```bash
modprobe [OPTIONS] [MODULE_NAME] [MODULE_PARAMERERS]
```

<table><thead><tr><th width="145">Option</th><th>Description</th></tr></thead><tbody><tr><td><code>-v</code></td><td>Use verbose output</td></tr><tr><td><code>-C</code></td><td>Override the default configuration directory/file</td></tr><tr><td><code>-n</code></td><td>Perform a dry run</td></tr><tr><td><code>-r</code></td><td>Remove a module</td></tr><tr><td><code>-w</code></td><td>Block new requests to a module and wait until it is available</td></tr><tr><td><code>-f</code></td><td>Attempt to force an insert or a remove</td></tr><tr><td><code>-D</code></td><td>List the dependencies of a module</td></tr><tr><td><code>-o</code></td><td>Attempt to rename a module</td></tr></tbody></table>

#### Module Configuration Files

* `/lib/modules/kernel-version/module.dep`: List the dependencies for every module in the directories under `/lib/modules/kernel_version`. This file is used by `modprobe` to know the order in which to load modules.
* `/etc/modules` and `/etc/modules-load.d/modules.conf` and /`etc/modules.conf`: Used to configure kernel modules to load at boot. The configuration file(s) should contain a list of kernel module names to load, separated by newlines. Empty lines and lines beginning with `#` or `;` are ignored.
* `/etc/modprobe.d` and `/etc/modprobe.conf`: The configuration directory/file for `modprobe`. The format of this configuration is one command per line with ‘\’ at the end of the line. Blank lines and lines beginning with ‘#’ are ignored.

**Commands:**

* `alias`: Give alternate names for modules
* `options`: Add options to the module when it is inserted into the kernel
* `install`: run a shell command instead of inserting a module as normal
* `remove`: run a shell command when `modprobe -r` is invoked rather than removing a module
* `blacklist`: ignore a particular module’s internal aliases

### Understand the /proc Filesystem and the Device Filesystem

* The `proc` Filesystem: A virtual filesystem that gets created and mounted during boot time. It presents information about the process and other system-related information. Processes, for which the filesystem is named, are represented by numbered directories that correlate with their PID. System and kernel-level parameters can be changed by manually editing files within the `/proc` directory or by using the `sysctl` utility. These changes are only effective for the current runtime and will not persist through a reboot.

```bash
[root@linuxmaster ~]# ls -ld /proc/* | awk ‘{print $9}’
/proc/1
/proc/10
/proc/1719
…
/proc/cpuinfo
/proc/devices
/proc/driver
…
```

* `sysctl`: Used to configure kernel parameters at runtime and to display the current values. Available parameters can be found in `/proc/sys/`. `sysctl` settings added to `/etc/sysctl.conf` or `/etc/sysctl.d/` will be applied on system boot. Configuration files added to `/etc/sysctl.d/` should end with “.conf” and be prefixed with a number based on priority. The higher the prefix number the greater the priority.

```
sysctl [options] [variable[=value]]
```

<table><thead><tr><th width="185">Option</th><th>Description</th></tr></thead><tbody><tr><td><code>-a</code></td><td>Display all value currently available</td></tr><tr><td><code>-w</code></td><td>Change a sysctl settings</td></tr><tr><td><code>-p</code></td><td>Load a sysctl settings from a file</td></tr><tr><td><code>-n</code></td><td>Print names without names</td></tr><tr><td><code>-N</code></td><td>Print names without values</td></tr></tbody></table>

* The Device Filesystem: Contains files for every device of which the system is aware and special device files that are used by the system. Some examples of this are `urandom`, `zero`, and `null`. The kernel device manager, udev, continually probe the system to check for connected devices and creates a device file when one is detected. Udev rules end with `.rules` an extension and determine how to identify devices and assign names to them. These rules are located in `/etc/udev/rules.d/` and `/lib/udev/rules.d/`.
* `udevadm monitor`: Listen to the kernel `uevents` and events are sent out by a `udev` rule and prints the devpath of the event to the console. This command replaces the now deprecated `udevmonitor` utility

```bash
udevadm monitor [options]
```

<table><thead><tr><th width="195">Option</th><th>Description</th></tr></thead><tbody><tr><td><code>-k</code></td><td>Print kernel uevents</td></tr><tr><td><code>-u</code></td><td>Print udev event after rule processing</td></tr><tr><td><code>-p</code></td><td>Also print the properties of the event</td></tr></tbody></table>

### Analyzing Information about Available Hardware

* `lspci`: A utility for displaying information about Peripheral Component Interconnect (PCI) buses in the system and the devices connected to them. By default, `lspci` shows a brief list of devices. At more verbose levels, the output is only useful to those who are very familiar with PCI.

```bash
lspci [options]
```

<table><thead><tr><th width="195">Option</th><th>Description</th></tr></thead><tbody><tr><td><code>-m</code></td><td>Dump data in a backward-compatible, machine-readable format</td></tr><tr><td><code>-mm</code></td><td>Dump data in a machine-readable format</td></tr><tr><td><code>-t</code></td><td>Display data in a tree-like diagram</td></tr><tr><td><code>-v</code></td><td>Increase verbosity (<code>-vv</code> and <code>-vvv</code> for more)</td></tr><tr><td><code>-k</code></td><td>Show kernel drivers handling each device and modules that are capable of handling it</td></tr><tr><td><code>-n</code></td><td>Show PCI vendor and devices codes as numbers</td></tr></tbody></table>

* `lsusb`: A utility for displaying information about USB buses in the system and the devices connected to them.

```bash
lsusb [options]
```

<table><thead><tr><th width="195">Option</th><th>Description</th></tr></thead><tbody><tr><td><code>-v</code></td><td>Increase verbosity</td></tr><tr><td><code>-s</code></td><td>Show devices specified with bus and/or <code>devnum</code> provided</td></tr><tr><td><code>-d</code></td><td>Show devices specified with vendor and product ID provided</td></tr><tr><td><code>-t</code></td><td>Display USB device hierarchy as a tree</td></tr></tbody></table>

* `lsdev`: Displays information about installed hardware. `lsdev` gathers this information from the interrupts, `ioports`, and `dma` files in the `/proc` directory. There are no command line options for this command.

## Commands and Notes



## Sample Questions

**1.** What command prints information about and controls the kernel ring buffer?

* [ ] insmod
* [ ] uname
* [x] dmesg
* [ ] modprobe

**2.** What are the two main types of compressed kernel images? (Choose two.)

* [x] zImage
* [ ] vmlinuz
* [x] bZimage
* [ ] initramfs

**3.** What is the utility used to generate modules whose source resides outside of the main kernel source tree?

* [ ] make modules\_install
* [x] dkms
* [ ] depmod
* [ ] make modules

**4.** What command is used to generate a compressed kernel image?

* [ ] make menuconfig
* [ ] make image
* [x] make bzImage
* [ ] make kImage

**5.** What command is used to configure kernel parameters at runtime?

* [ ] modprobe
* [ ] proc
* [x] sysctl
* [ ] make modules

**6.** What is the default location for the kernel source tree?

* [ ] /etc/src
* [ ] /usr/lib/src
* [x] /usr/src
* [ ] /src

**7.** What are accepted names for the makefile? (Choose all that apply.)

* [x] makefile
* [ ] make\_file
* [x] gnumakefile
* [x] Makefile

**8.** What command intelligently adds and removes modules from the kernel?

* [x] modprobe
* [ ] modinfo
* [ ] modules.dep
* [ ] insmod

**9.** Which of the following commands are used to generate the initramfs image? (Choose two.)

* [x] mkinitramfs
* [x] mkinitrd
* [ ] make-initrd
* [ ] make initramfs

**10.** What command is used to monitor kernel uevents?

* [ ] modinfo
* [x] udevadm monitor
* [ ] dmesg
* [ ] udevadm-monitor

**11.** What option for lspci shows kernel drivers handling each device and modules that are capable of handling it?

* [ ] \-m
* [ ] \-t
* [ ] \-n
* [x] \-k

**12.** What command displays information about installed hardware?

* [ ] lsusb
* [ ] lspci
* [x] lsdev
* [ ] uname

**13.** Which part of a Linux system manages system memory?

* [ ] GNU utilities
* [x] kernel
* [ ] Graphical desktop
* [ ] Application software

**14.** What is the area on the hard disk called that is used as memory storage?

* [x] Swap Space
* [ ] Virtual memory
* [ ] Filesystem
* [ ] Physical Memory

**15.** What method does Linux use to load device drivers into the kernel?

* [ ] DLL Files
* [ ] Virtual memory
* [x] Modules
* [ ] Bootloader

**16.** Which kernel file type is used to boot the Linux system?

* [x] Binary file
* [ ] Modules
* [ ] Patch file
* [ ] Source code

**17.** What type of release can you use to upgrade an existing kernel to a newer version?

* [x] Patch release
* [ ] Modules
* [ ] Binary files
* [ ] Development release

**18.** Which make utility target uses text-based questions to configure the kernel?

* [ ] xconfig
* [ ] mrproper
* [ ] clean
* [x] config

**19.** Which make utility target uses a graphical interface to configure the kernel?

* [ ] config
* [ ] mrproper
* [x] gconfig
* [ ] clean

**20.** Which make utility target should you use to remove any old object files from a previous compile?

* [ ] gconfig
* [x] mrproper
* [ ] bzImage
* [ ] oldconfig

**21.** Which make utility target is commonly used to generate a compressed kernel binary file?

* [x] bzImage
* [ ] kernel
* [ ] config
* [ ] clean

**22.** Which program should you use to create an initial RAM disk for a Debian-based system?

* [ ] mkinitrd
* [ ] make bzImage
* [x] mkinitramfs
* [ ] make initrd

**23.** Which command should you use to list all of the installed modules?

* [ ] modinfo
* [ ] insmod
* [ ] rmmod
* [x] lsmod

**24.** Which command should you use to install a module if you don’t know its filename?

* [ ] insmod
* [ ] rmmod
* [x] modprobe
* [ ] lsmod

**26.** Which command should you use to display information about USB devices installed on the system?

* [x] lsusb
* [ ] lspci
* [ ] lsmod
* [ ] lsdev

**27.** Which option in the lsusb command displays detailed information about the USB devices?

* [ ] \-d
* [ ] \-s
* [ ] \-t
* [x] \-v

**28.** Which type of hardware devices can you connect to the Linux system as it’s running?

* [ ] Coldplug
* [x] Hotplug
* [ ] PCI
* [ ] PCIe

**29.** What program does Linux use to detect hotplug devices and load the appropriate modules?

* [ ] lsusb
* [x] udevd
* [ ] modprobe
* [ ] insmod

**30.** Where are the udevd configuration settings stored?

* [ ] /lib/modules
* [ ] /boot
* [ ] /etc/udev/rules.d
* [x] /etc/udev/udev.conf

**31.** Where are the udevd rules stored?

* [ ] /etc/udev/udevd.conf
* [x] /etc/udev/rules.d
* [ ] /boot
* [ ] /lib/modules

**32.** Where is the dynamic pseudo-directory the kernel creates to peek at kernel settings and performance statistics located?

* [ ] /etc/kernel
* [ ] /boot/kernel
* [x] /proc
* [ ] /usr/src/linux

**33.** What command allows you to change kernel settings during runtime?

* [x] sysctl
* [ ] lsmod
* [ ] lsdev
* [ ] modprobe

**34.** Which of the following terms are used to describe 3.x kernel releases? (Choose TWO correct answers.)

* [ ] beta
* [ ] final
* [x] longterm
* [ ] prereleased
* [x] stable

**35.** How can the kernel parameter for the maximum size of the shared memory segment (shmmax) be changed to 2GB (2147483648 Bytes) on a running system? (Choose TWO correct answers.)

* [ ] Edit /etc/shmmax and set the parameter to 2147483648.
* [ ] sysctl shmmax=2147483648
* [x] sysctl kernel.shmmax=2147483648
* [x] echo 2147483648 > /proc/sys/kernel/shmmax
* [ ] export kernel.shmmax=2147483648

**36.** What is the correct parameter to pass to the kernel at boot time to force it to use only one of the available processors?

* [x] maxcpus=1
* [ ] usecpus=1
* [ ] smpcpus=1
* [ ] vcpumx=1

**37.** Which commands are used to load modules into the Linux kernel? (Choose TWO correct answers.)

* [x] insmod
* [ ] loadmod
* [ ] kernload
* [x] modprobe
* [ ] probemod

**38.** What is a key difference between a zImage and bzImage kernel image?

* [ ] zImage is compressed using gzip, bzImage is compressed using bzip2.
* [ ] zImage is for 2.6 series kernels, bzImage is for 3.x series kernels.
* [ ] zImage is limited to 64k, bzImage has no such restriction.
* [x] zImage gets loaded completely into low memory, bzImage will load into high memory once low memory is full.

**39.** Which of the following command sequences can be used to extract files contained in a initramfs file (/boot/initramfs) which is used by the kernel at boot time?

* [ ] cp /boot/initramfs /tmp/initramfs.gz; gzip -c /tmp/initramfs.gz; mkdir /tmp/initramfs.dir ; cd\
  /tmp/initramfs.dir ; cpio -i < /tmp/initramfs
* [x] cp /boot/initramfs /tmp/initramfs.gz; gunzip /tmp/initramfs.gz; mkdir /tmp/initramfs.dir ; cd\
  /tmp/initramfs.dir ; cpio -i < /tmp/initramfs
* [ ] cp /boot/initramfs /tmp/initramfs.gz; gunzip /tmp/initramfs.gz; mount /tmp/initramfs /mnt/ -o loop -t initramfs
* [ ] cp /boot/initramfs /tmp/initramfs.gz; gunzip /tmp/initramfs.gz; mkdir /tmp/initramfs.dir ; cd\
  /tmp/initramfs.dir ; cpio -e /tmp/initramfs
* [ ] cp /boot/initramfs /tmp/initramfs.gz; gunzip /tmp/initramfs.gz; mount /tmp/initramfs /mnt/ -o loop -t initrd

**Description**:

1. `cp /boot/initramfs /tmp/initramfs.gz` - This command copies the initramfs file from the /boot directory to `/tmp` and renames it to `initramfs.gz`. Note that this step assumes the initramfs file is compressed with gzip. If it's not a gzipped file, the `.gz` extension shouldn't be added.

2. `gunzip /tmp/initramfs.gz` - This command decompresses the file initramfs.gz. After decompression, the file will be named `initramfs` in the `/tmp` directory.

3. `mkdir /tmp/initramfs.dir` - This creates a new directory where the contents of the initramfs file will be extracted.

4. `cd /tmp/initramfs.dir` - This changes the current working directory to the newly created directory.

5. `cpio -i < /tmp/initramfs` - This command uses `cpio` to extract the contents of the initramfs file into the current directory (`/tmp/initramfs.dir`). The `-i` option stands for "extract".

**40.** On a server running the 3.4.50-11 Linux kernel, which file in the /boot directory contains the kernel configuration parameters?

* [ ] config-linux-3.4.50-11
* [x] config-3.4.50-11
* [ ] system-3.4.50-11
* [ ] vmlinuz-3.4.50-11
* [ ] rc.config-3.4.50-11

**Description**: In a Linux system, the file that contains the kernel configuration parameters is typically named with the `config-` prefix followed by the kernel version. This file is located in the `/boot` directory. Based on the options you've provided and the kernel version you mentioned (3.4.50-11), the correct file would be: `config-3.4.50-11`

**41.** Which archive format is used to create an initramfs image?

* [ ] gzip
* [ ] tar
* [ ] RAR
* [x] cpio
* [ ] bzip2

**42.** What information does the file modules.dep provide?

* [ ] A list of all modules, compiled or not, that are available to the kernel.
* [ ] A list of modules trusted by the installed kernel.
* [ ] A list of devices and their module name.
* [x] A list of all compiled modules and their dependencies.
* [ ] A list of modules the kernel needs to run.

**43.** What is the purpose of the command udevadm monitor?

* [x] It listens to kernel events produced by a udev rule and print information to the console.
* [ ] It monitors the /dev directory for new devices.
* [ ] It monitors the udev process and prints performance statistics to the console.
* [ ] It communicates with D-Bus to setup new devices.

**44.** How is the source code for the main Linux kernel modules distributed?

* [x] It is inculded with the Linux kernel source code.
* [ ] The kernel modules and downloaded on demand as they are used during compilation.
* [ ] The kernel modules have their own release cycle and can be maintained seprately from the Linux kernel source.
* [ ] It is provided as a seprate download alongside the Linux kernel source code of the same version.

**45.** Which option to the update-rc.d command will cause the removal of all symlinks to /etc/rcX.d/*test2 even when the script /etc/init.d/test2 still exists?

* [ ] -n
* [x] -f
* [ ] -r
* [ ] -d

**Description**: The `-f` option stands for "force". When you use `update-rc.d -f test2` remove, it forces the removal of all symbolic links to the `/etc/init.d/test2` script from the `/etc/rcX.d` directories regardless of whether the `/etc/init.d/test2` script still exists.

**46.** Which of the following files will be looked for and used by GNU make, if one of them exists, unless a different file is specified on the command line when trying to compile software from source code? (Choose TWO correct answers.)

* [x] makefile
* [ ] configure
* [ ] makefile.in
* [ ] config.h.in
* [x] Makefile

**47.** Before compiling a new kernel , what needs to be done?

* [ ] Compile kernel modules.
* [x] Configure the kernel options.
* [ ] Delete olf kernel sources.
* [ ] Change to runlevel 1.

**Description**: Configuring the kernel options is a critical step in the kernel compilation process. This involves selecting the necessary modules, features, and settings that the new kernel will support. This configuration can be done using tools like `make menuconfig`, make `xconfig`, or `make gconfig` in the Linux kernel source directory. The configuration process creates a `.config` file, which is used by the `make` command during the compilation process.

**48.** That script is included with the source of the kernel to fix a kernel ?

* [x] patch
* [ ] patch-kernel
* [ ] apply-patch
* [ ] update-kernel

**Description**: In the context of Linux kernel development and maintenance, the `patch` utility is used to apply changes to the kernel source code. These changes are often distributed in the form of "patch files," which contain the differences between one set of files and another. When developers fix bugs or add features, they often do so by creating a patch file that can be applied to the kernel source using the `patch` command.

**49.** What command would you use to apply a diff file to an original?

* [ ] cat diff -file
* [ ] cat diff -file >> kernel
* [ ] patch
* [x] patch < diff -file
* [ ] patch > diff -file

**Description**: This command uses the `patch` utility, which is specifically designed for applying diff files. The diff file contains the differences between files and is applied to the original files to update them. The syntax `patch < diff-file` means that the `patch` command takes the contents of the diff file as input.

**50.** After configuring a new 2.4 series kernel , all dependencies, such as included files, need to be created. How can this be achieved?

* [ ] make dependencies
* [ ] make clean
* [ ] make mrproper
* [x] make dep
* [ ] make test

**Description**: The `make dep` command was used specifically with older Linux kernel series like 2.4 to create dependencies. This command ensures that all the necessary headers and files are properly linked based on the configuration choices made.

**51.** What two archiving formats are used to create an initramfs image?

* [x] gzip
* [ ] tar
* [ ] rar
* [x] cpio
* [ ] bzip2

**52.** After unpacking the source code for a Linux kernel, what is the first make command that should be executed, which will delete any current configuration and all generated files? This command will ensure that the maintainer does not leave inappropriate files in the kernel file.

* [ ] make depend
* [ ] make distclean
* [ ] make config
* [ ] make clean
* [x] make mrproper

**Description:** The `make mrproper` command is used to thoroughly clean the kernel source tree. It removes not only the compiled binaries and object files (which `make clean` does) but also the configuration files and other generated files that might not be needed for a fresh start. This command ensures that you start with a clean state, which is particularly important when compiling a new kernel or switching between different kernel versions.

**53.** What script, in the linux / scripts directory , can be used to add upgrades or updates to the 2.6.xx kernel source code?

* [ ] patch
* [x] patch-kernel
* [ ] kernel-update
* [ ] upgrade-kernel
* [ ] upgrade

**Description:** The `patch-kernel` script is designed to apply a series of patch files to the Linux kernel source. It automates the process of updating or upgrading the kernel source code with new patches, which are often released for bug fixes, security updates, and new features.

**54.** You have finished updating and resolving dependencies on some source code. What command should you run before recompiling the code in binary format?

* [x] make clean
* [ ] make all
* [ ] make dep
* [ ] make install

**Description:** The purpose of `make clean` is to remove all files that were generated during previous builds. This ensures that you start the compilation with a clean slate, which is particularly important after updating source code or dependencies, as old object files or binaries might interfere with the new build process.

**55.** What tool can be used to control the behavior of runtime of udev?

* [ ] udev
* [ ] udevctl
* [x] udevadm
* [ ] udevconfig
* [ ] udevclient

**Description**: `udevadm` is the command-line utility provided for interacting with the udev Linux device manager. It allows for managing the runtime behavior of the udev system, including triggering events, querying udev database, monitoring udev events, and controlling the udev daemon.

**56.** Which command can be used to view the kernel messages created from the boot time so far?

* [ ] initm
* [ ] initmessage
* [ ] initmsg
* [ ] inittab
* [x] dmesg

**57.** Why mkinitrd was deprecated in favor of mkinitramfs from the series Kermel 2.6? (Select 2 correct answers).

* [ ] It is not loaded until later in the boot process
* [ ] An easy to use GUI is available in mkinitramfs
* [x] mkinitrd depends on devfs
* [x] SATA hard drives are not supported

**58.** A precompiled module has been moved to `/lib/modules/<kernel-version>`, but `modprobe -a <module-name>` fails to load it. What needs to be done to use this module?

* [ ] make modules_install must be run
* [x] depmod must be run
* [ ] The kernel must be re-compiled
* [ ] Modules.conf or modprobe.conf must be edited
* [ ] The system must be restarted

**Description**: The `depmod` command creates a module dependency file (`modules.dep` and other map files) in the `/lib/modules/<kernel-version>` directory. This file is used by `modprobe` to automatically load the necessary modules and their dependencies. Running `depmod` ensures that the system is aware of the new module and its dependencies, making it possible for `modprobe` to load the module successfully.

**59.** To restore a kernel source to its previous, unpatched version, which of the following commands could be used?

* [ ] patch - restore
* [ ] patch --remove
* [ ] patch -U
* [ ] patch -undo
* [x] patch -R

**Description:** The `-R` option with the `patch` command reverses the patching process. It essentially "undoes" the changes made by a patch. When you apply a patch using `patch`, it makes changes to the source code based on the contents of the patch file. Using `patch -R` with the same patch file will revert those changes, restoring the source code to its previous state.

**60.** If the current directory is `/root` and the kernel source is located in `/usr/src/linux`, which of the following commands should be used to apply the `/tmp/foopatch `patch?

* [ ] cat /tmp/foopatch | patch -p0
* [ ] cd /usr/src/linux; cat /tmp/foopatch | patch -p0
* [ ] cd /usr/src/linux; cat /tmp/foopatch | patch
* [x] cd /usr/src/linux; patch -p1 < /tmp/foopatch
* [ ] cd /usr/src/linux; patch -p1 > /tmp/foopatch

**61.** A 2.6.9-ac1 kernel would be:

* [ ] An alpha kernel.
* [x] Some patch for a stable kernel.
* [ ] A stable kernel.
* [ ] An unstable kernel.
* [ ] Some patch for an unstable kernel.

**62.** What command should be used to find out which PCI devices can be seen, without consulting the kernel?

* [x] lspci -v
* [ ] lspci -s
* [ ] lspci -x
* [ ] lspci -b
* [ ] lspci -p

**Description**: The `lspci` command lists all PCI devices. The `-v` flag (for "verbose") provides detailed information about each device. This command reads the device information directly from the PCI configuration space, so it does not rely on the kernel's view of the devices.

**63.** User Joseph successfully extracted and compiled a program from source code. Installing the Binaries produces errors. What is the most likely reason?

* [ ] The source code was compiled for a different CPU.
* [ ] The permissions set on the `/usr/bin` directory are wrong.
* [x] Binaries requier root privileges to be installed.
* [ ] An incorrect prefix was used when configuration the source code.

**64.** The structure of the official Linux kernel can be classified as:

* [x] Monilithic
* [ ] Micro-kernel
* [ ] Stable
* [ ] Multitasking

**Description**: The Linux kernel is known as a monolithic kernel. This means it operates in a single large process running entirely in a single address space. It's distinguished from micro-kernel systems where the kernel is broken down into separate processes known as servers. The terms "stable" and "multitasking" are characteristics or features of an operating system, but they do not describe its structural classification.

**65.** What is the correct Linux kernel version numbering format?

* [ ] 09.04
* [ ] 2008
* [x] 2.6.31
* [ ] 2008 Server

**66.** What do the suffixes added to the kernel version number represent?

* [ ] The initials of the name of the author of the kernel.
* [x] A specific kernel, changed from the corresponding official kernel
* [ ] An unstable version of kernel
* [ ] A kernel that is not provided by the official website kernel.org

**67.** Where can the official kernel version specific documentation be found?

* [ ] In linux newsgroups
* [ ] In the source code of the module
* [x] In the Documentation directory where the kernel source code was placed.
* [ ] In the comments left by the developers.

**68.** What commands can be used to generate an Initial Ramdisk?

* [ ] mkisofs
* [ ] fdisk
* [x] mkinitramfs
* [x] mkinitrd

**69.** Which command can be used to configure the Linux kernel?

* [x] make config
* [x] make xconfig
* [x] make menuconfig
* [x] make gconfig

**70.** The uname -r command reports:

* [ ] The resources available in the running kernel.
* [ ] The modules loaded into memory for the running kernel.
* [ ] The modules not loaded into memory for the running kernel.
* [x] The version of the kernel source code that is running

**71.** The correct way to load the ehci_hcd module and its dependencies is:

* [ ] make modules_install
* [ ] make modules_install ehci_hcd
* [x] modprobe ehci_hcd
* [ ] insmode ehci_hcd

**72.** What type of compression is used in a zImage kernel?

* [x] gzip
* [ ] bzip2
* [ ] rar
* [ ] cpio

**73.** What information is not normally provided by the modinfo command? Select everything that applies.

* [ ] license
* [ ] author
* [x] in use
* [ ] description
* [x] size

**74.** After compiling a new kernel, which of the following is a valid filename for the compressed kernel?

* [ ] kernel
* [ ] image
* [x] zimage
* [ ] vmlinux

**Description**: `vmlinux` is the uncompressed kernel image.

**75.** Which of the following commands will show the status of the running kernel?

* [x] cat /proc/sys/kernel/threads-max
* [ ] cat /dev/kernel/threads-max
* [ ] cat /etc/sys/kernel/threads-max
* [ ] cat /etc/sys/kernel/threads-max
* [ ] cat /proc/sys/kernel/threads/max

**76.** What is the conventional name for a Linux kernel initially compiled and bootable on an x86 system?

* [ ] /boot/vmlinux-version
* [x] /boot/vmlinuz
* [ ] /boot/bzImage-version
* [ ] /usr/src/linux-version

**77.** In which directory can you look for information about the options that can be applied to your computer's Ethernet driver module?

* [x] /usr/src/linux/Documentation
* [ ] /lib/modules/options
* [ ] /usr/share/doc/modules
* [ ] /etc/modules.conf

**77.** What kind of kernel image was largely abandoned on x86 computers in favor of the bzImage format?

* [ ] vmlinux
* [x] zImage
* [ ] initramfs
* [ ] initrd

**78.** Which of the following commands, when typed in `/usr/src/linux` after configuring the kernel, compiles the main Linux kernel file and its modules?

* [x] make bzImage
* [ ] make modules_install ehci_hcd
* [ ] make xconfig
* [ ] make

**79.** Which of the following kernel features should you compile in the main kernel file for an x86-64 disk-based installation to simplify system startup? (Select 2 responses).

* [x] Drivers for the ATA boot disk controller or SCSI host adapter.
* [x] Support for your root file system.
* [ ] Drivers for the USB port.
* [ ] Framebuffer controllers for the vide card.

**80.** You have compiled and installed a new kerne , version 2.6.35.4. You now want to prepare an initial RAM disk. Which of the following commands will do this, depending on your distribution? (Select 2 options).

* [x] mkinitrd -o /boot/initrd-2.6.35.4 2.6.35.4
* [ ] mkinitrd /boot/initrd-2.6.35.4
* [x] mkinitramfs -o 2.6.35.4 /boot/initrd-2.6.35.4
* [ ] mkinitramfs /boot/initrd-2.6.35.4

**81.** You downloaded the patch file - 2.6.35.4.bz2. What program will you use as part of the patching operation to handle the compression of this file?

* [x] bunzip2
* [ ] gunzip2
* [ ] tar
* [ ] cpio

**82.** Which of the following commands must you enter to configure a Linux kernel using an interactive text mode tool?

* [ ] make xconfig
* [x] make menuconfig
* [ ] make config
* [ ] make textconfig

**83.** You have configured and compiled your new kernel, version 2.6.35.4. Now you type make modules_install. Where can you expect to find the module files?

* [ ] /lib/modules/modules-2.6.35.4
* [ ] /usr/src/linux/2.6.35.4
* [x] /lib/modules/2.6.35.4
* [ ] /usr/lib/2.6.35.4

**84.** In which file in the Linux kernel source tree are configuration options stored?

* [ ] kernel.conf
* [ ] configure
* [x] .config
* [ ] linux.conf

**Description:** The `.config` file in the root of the Linux kernel source tree holds the configuration options for the kernel. This file is generated when you run a kernel configuration tool like `make menuconfig`, `make xconfig`, or `make config`, and it reflects the choices made for various kernel features and modules. The other file names listed are not standard for storing kernel configuration options.

**85.** You found a kernel module for your network card, but you don't know what parameters to use. Which command can provide you with this information?

* [ ] modeprobe
* [ ] insmode
* [ ] depmode
* [x] modinfo
* [ ] cat /proc/modeinfo

**86.** Which of the following commands will load a kernel module, along with all necessary dependency modules?

* [ ] depmode
* [ ] insmode
* [x] modeprobe ehci_hcd
* [ ] loadmode

**87.** Which command should load the msdos.o module and all its dependencies?

* [ ] modeinfo -a msdos
* [ ] lsmode -a msdos
* [x] modeprobe msdos
* [ ] issmode -d msdos

**88.** Which command will remove a kernel module?

* [x] rmmod
* [ ] unmode
* [ ] delmod
* [ ] modeprobe
* [ ] unloadmod

**89.** You have just added new modules to the system. Which command should you run to rebuild the modules.dep file?

* [ ] depmode -rebuild
* [ ] update-dependencies
* [x] depmode -a
* [ ] insmode -dependencies

**90.** Which of the following correctly describes the relationship between depmod and modprobe?

* [ ] modproble creats a dependency file for use by depmode.
* [x] depmode creats a dependency file for use by modproble.
* [ ] They have no relationship.
* [ ] They can replace each other.

**Description:** `depmod` is used to create a dependency file (typically named `modules.dep`) which lists the dependencies for each module. This file is then used by `modprobe` to understand which modules need to be loaded when a particular module is requested, ensuring that all necessary dependencies are loaded automatically. They have a complementary relationship, but they do not replace each other, as each serves a distinct purpose in the management of kernel modules.

**91.** Which option of the depmod command allows you to print a list of all unresolved symbols?

* [ ] -l
* [ ] -i
* [ ] -a
* [x] -e

**92.** Which of the following commands loads the module file into the kernel and changes any symbols that are defined on the command line?

* [ ] depmode
* [x] insmode
* [ ] modprobe
* [ ] setmod

**93.** What option of the insmod command can be used to force the module to load even if problems are encountered?

* [ ] -F
* [x] -f
* [ ] -u
* [ ] -x

**94.** Which option in the rmmod command specifies that all output should be sent to the syslog?

* [ ] -a
* [ ] -i
* [x] -s
* [ ] -d

**95.** Which utility examines the object module_file file associated with a kernel module and displays all the information it can collect?

* [ ] depmode
* [x] modinfo
* [ ] modprobe
* [ ] insmod

**96.** Which modprobe option specifies the loading of all corresponding modules instead of stopping after the first successfully loaded module is loaded?

* [ ] -f
* [ ] -c
* [ ] -e
* [x] -a

**96.** Which modprobe option allows you to define "autoclean" on loaded modules?

* [ ] -e
* [ ] -c
* [x] -k
* [ ] -d

**97.** Which parameter of the uname command can be used to display the kernel version?

* [ ] -m
* [ ] -n
* [ ] -s
* [ ] -v
* [x] -r

**98.** Which parameter of the uname command can be used to display the machine type (Hardware / Architecture)?

* [ ] -n
* [ ] -s
* [x] -m
* [ ] -r