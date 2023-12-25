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

**Desc**:

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

**45.** What

* [ ] lsusb
* [ ] lspci
* [ ] lsdev
* [ ] uname

**46.** What

* [ ] lsusb
* [ ] lspci
* [ ] lsdev
* [ ] uname

**47.** What

* [ ] lsusb
* [ ] lspci
* [ ] lsdev
* [ ] uname

**48.** What

* [ ] lsusb
* [ ] lspci
* [ ] lsdev
* [ ] uname

**49.** What

* [ ] lsusb
* [ ] lspci
* [ ] lsdev
* [ ] uname
