# 203: Filesystem and Devices

## 203.1 Operating the Linux filesystem (weight: 4)

{% hint style="success" %}
Candidates should be able to properly configure and navigate the standard Linux filesystem. This objective includes configuring and mounting various filesystem types.



**Key Knowledge Areas:**

* The concept of the fstab configuration
* Tools and utilities for handling swap partitions and files
* Use of UUIDs for identifying and mounting file systems
* Understanding of systemd mount units

**The following is a partial list of the used files, terms and utilities:**

* /etc/fstab
* /etc/mtab
* /proc/mounts
* mount and umount
* blkid
* sync
* swapon
* swapoff
{% endhint %}

### Mounting Filesystems

* `mount`: Mount a filesystem

<pre class="language-bash"><code class="lang-bash"><strong>mount [option] device mountpoint
</strong></code></pre>

| Option | Description                           |
| ------ | ------------------------------------- |
| `-a`   | Mount all filesystems in `/etc/fstab` |
| `-F`   | With the -a option, mount in parallel |
| `-n`   | Mount without writing in `/etc/mtab`  |
| `-o`   | Use the specified mount options       |
| `-t`   | Specify the filesystem type           |
| `-U`   | Mount a partition by UUID             |
| `-v`   | Increase verbosity                    |

* `umount`: Unmount a filesystem

<pre class="language-bash"><code class="lang-bash"><strong>umount [option] directory|device
</strong></code></pre>

| Option | Description                                  |
| ------ | -------------------------------------------- |
| `-a`   | Unmount filesystems described in `/etc/mtab` |
| `-t`   | Act only on filesystem type specified        |
| `-f`   | Force an unmount                             |
| `-n`   | Unmount without writing to `/etc/mtab`       |
| `-v`   | Increase verbosity                           |

* `sync`: Synchronize cached writes to persistent storage

<pre class="language-bash"><code class="lang-bash"><strong>sync [option] [file] ...
</strong></code></pre>

* `blkid`: Command line utility to locate and print block device attributes. It has two main forms of operation: either searching for a device with a specific NAME=value pair or displaying NAME=value pairs for one or more devices.&#x20;

<pre class="language-bash"><code class="lang-bash"><strong>blkid -L lable | - U uuid or blkid [option] device
</strong></code></pre>

* `/etc/fstab`: Static information about the filesystem

{% code overflow="wrap" %}
```bash
# A B C D E F

UUID=47C4-076C /home ext4 defaults 0 0

# A: Describe a block special device, remote filesystem, filesystem image, or swap file.
# B: Describe the mount point or target for the filesystem. 
# C: Describe the type of filesystem (e.g. ext4, xfs, nfs, etc.)
# D: Describe the mount options associated with the filesystem. Basic filesystem independent options include the following: defaults, noauto, user, owner, comment, no fail.
# E: Used by dump to determine which filesystems need to be dumped (0 disable).
# F: Used by fsck to determine the order in which filesystem checks are done at boot time. the root filesystem should be set to 1 and all other filesystem should be set to 2. The default of 0 disablesthe check.
```
{% endcode %}

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>cat /etc/fstab
</strong>
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a device; this may
# be used with UUID= as a more robust way to name devices that works even if
# disks are added and removed. See fstab(5).
#
# &#x3C;file system>             &#x3C;mount point>  &#x3C;type>  &#x3C;options>  &#x3C;dump>  &#x3C;pass>
UUID=47C4-076C                            /boot/efi      vfat    umask=0077 0 2
UUID=6a6b5a66-f7c0-44c1-8c81-be91ca97b0c9 /              ext4    defaults,noatime 0 1
</code></pre>

* `/etc/mtab`: The programs mount and umount traditionally maintained a list of currently mounted filesystems in `/etc/mtba`. For modern systems, `/etc/mtab` is a symlink to `/proc/mounts` (or `/proc/self/mounts`).
* `/proc/mounts`: Part of the proc virtual filesystem that presents data from the kernel about the mounted filesystems on the host. Can be a symlink to `proc/self/mounts`.

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>cat /proc/mount
</strong>
proc /proc proc rw,nosuid,nodev,noexec,relatime 0 0
sys /sys sysfs rw,nosuid,nodev,noexec,relatime 0 0
devpts /dev/pts devpts rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000 0 0
/dev/nvme0n1p5 / ext4 rw,noatime 0 0
...
binfmt_misc /proc/sys/fs/binfmt_misc binfmt_misc rw,nosuid,nodev,noexec,relatime 0 0

</code></pre>

### Linux Swap Space

Swap space is used as a substitute for physical memory (RAM) when the physical memory is full. This is helpful for systems with small amounts of memory but is not a substitute for adding additional RAM (because of inefficiency is speed). Swap space can be a dedicated partition or file (or both). The `/proc/swaps` file can be viewed to determine the amount of swap in use.

* `swapon/swapoff`: Enable or disable devices and files for paging and swapping.

<pre class="language-bash"><code class="lang-bash"><strong>swapon [options] [specialfile...]
</strong></code></pre>

| Option   | Description                                    |
| -------- | ---------------------------------------------- |
| `-a`     | Enable all devices marked swap in `/etc/fstab` |
| `-o`     | Specify swap options                           |
| `-p`     | Specify the priority of the device             |
| `-s`     | Display swap usage summary by device           |
| `--show` | Display a definable table of swap areas        |
| `-v`     | Increase verbosity                             |

* `mkswap`: Set up a Linux swap area.

<pre class="language-bash"><code class="lang-bash"><strong>mkswap [options] device [size]
</strong></code></pre>

<pre class="language-bash"><code class="lang-bash"><strong>cat /proc/swaps
</strong>
Filename				Type		Size	Used	Priority
/root/swap01                            file		2047996	0	-2
</code></pre>

### Systemd Mount Units

A systemd mount unit is a unit configuration file that ends in ".mount" and encodes information about a file system mount point controlled and supervised by Systemd.

Systemd automatically creates unit files for all the entries in `/etc/fstab`. These are in `/run/systemd/generator/`. Mount units can be manually created, but it is generally advised to add entries to `etc/fstab`.

* `cat /run/systemd/generator/mnt-my.mount`

```bash
# Automatically generated by systemd-fstab-generator

[Unit]
SourcePath=/etc/fstab
Documentation=man:fstab(5) man:systemd-fstab-generator(8)

[Mount]
What=/dev/disk/by-uuid/baabdf1f-5449-4f48-a8cd-7e188dbefb5d
Where=/mnt/my
Type=ext4
Options=discard,nofail,defaults
```

## 203.2 Maintaining a Linux filesystem (weight: 3)

{% hint style="success" %}
Candidates should be able to properly maintain a Linux filesystem using system utilities. This objective includes manipulating standard filesystems and monitoring SMART devices.



**Key Knowledge Areas:**

* Tools and utilities to manipulate and ext2, ext3 and ext4
* Tools and utilities to perform basic Btrfs operations, including subvolumes and snapshots
* Tools and utilities to manipulate XFS
* Awareness of ZFS

**The following is a partial list of the used files, terms and utilities:**

* mkfs (mkfs.\*)
* mkswap
* fsck (fsck.\*)
* tune2fs, dumpe2fs and debugfs
* btrfs, btrfs-convert
* xfs\_info, xfs\_check, xfs\_repair, xfsdump and xfsrestore
* smartd, smartctl
{% endhint %}

### Ext2/3/4 and XFS Filesystems

#### ext2

* Developed to overcome some of the limitation of the original ext filesystem.
* Max file size ranges from 16GB to 2 TB.
* Max filesystem size ranges from 2 TB to 32 TB.
* Supports 32,000 subdirectories.
* Recommended for flash-based storage becase of the absence of overhead caused by journaling.
* Can be directly converted to ext3.

#### ext3

* Many of the base features remain the same as ext2.
* Introduces a dedicated space for journaling.
* Journaling levels:
  * Journal - Metadata and content are written to the journal.
  * Ordered (default) - Only metadata is written to the journal. Content is written before metadata is committed.
  * Writeback - Only metadata is written to the journal. Content may be written before or after metadata is commited.
* Supports online filesystem growth.
* Introduces HTree indexing for larger directories.

#### ext4

* Max file size increase to 16 TB.
* Max filesystem increase to 1 Exabyte.
* Support a virtually unlimited number of subdirectories.
* Introduces journal checksums as well as several other imporovements to reliability and performance.
* Support backwards compatibility for ext2 and ext3.

#### Utilities for the EXT Filesystem

* `mkfs (mkfs.*)`: Build a Linux filesystem. The mkfsfroundend utility deprecated in favor of the filesystem-specific mkfs.utilities (e.g. `mkfs.ext4`, `mkfs.xfs`, etc.)

<pre class="language-bash"><code class="lang-bash"><strong>mkfs [options] [-t type] [fs-options] device [size]
</strong></code></pre>

* `fsck`: Checks and optionally repairs one or more Linux filesystems. A filesystem can be a device name, a mount point, an ext2 label, or a UUID specifier. The `fsck` command is a frontend for the various filesystem checkers (e.g. `fsck.fstype`)

<pre class="language-bash"><code class="lang-bash"><strong>fsck [options] [filesystem] -- [fs-specific-options]
</strong></code></pre>

| Option | Purpose                                             |
| ------ | --------------------------------------------------- |
| `-t`   | Specify the type of filesystem to be checked        |
| `-A`   | Check filesystem in `/etc/fstab`                    |
| `-C`   | Display progress bars for the check                 |
| `-N`   | Perform a dry run                                   |
| `-a`   | Automatically repair a filesystem                   |
| `-n`   | Print errors but do not repair                      |
| `-r`   | Interactively repair filesystems                    |
| `-y`   | Always attempt to repair a filesystem automatically |

* `tune2fs`: Adjust tunable filesystem parameters on ext2/ext3/ext4 filesystems.

<pre class="language-bash"><code class="lang-bash"><strong>tune2fs [options] device
</strong></code></pre>

| Option | Purpose                                                                                          |
| ------ | ------------------------------------------------------------------------------------------------ |
| `-l`   | List the contents of the filesystem superblock                                                   |
| `-L`   | Set the volume lable for the system                                                              |
| `-j`   | Add an ext3 journal to the filesystem                                                            |
| `-J`   | Override the default ex3 journal parameters                                                      |
| `-m`   | Set the percentage of the filesystem which may only be allocated by privileged processes.        |
| `-o`   | Set or clear the indicated default mount options ('^' is used to clear option)                   |
| `-O`   | Set or clear the indicated filesystem features in the filesystem ('^' is used to clear features) |

* `dumpe2fs`: Dump ext2/ext3/ext4 filesystem information. Prints the super block and blocks group information for the device specified. The `-h` option is used to only display superblock information.

<pre class="language-bash"><code class="lang-bash"><strong>dumpe2fs [options] device
</strong></code></pre>

* `debugfs`: ext2/ext3/ext4 filesystem debugger. Used to interactively examine and change the state of the filesystem. The `-w` option specifies that the filesystem should be opened in read-write mode.

<pre class="language-bash"><code class="lang-bash"><strong>debugfs [options] device
</strong></code></pre>

#### XFS

XFS stands for the extents filesystem, which is a reference to the way blocks are allocated and managed. Extents are one or more contiguous blocks that vary in length. It also implements the concept of allocation groups (AG), which are equally-sized linear regions on the filesystem.

Although files and directories can span across multiple AGs, each AG is responsible for managing its own inodes and free space.

Allocates blocks using extents, Manages storage through allocation groups (AGs) and Implements journaling Supports large filesystems and individual files (16 exbibvtes and 8 exbibytes respectivelv)

* `xfs_info`: Display XFS filesystem geometry information.
* `xfs_check`: Check the consistency of an XFS filesystem. This command is deprecated in favor of `xfs_repair -n`
* `xfs_repair`: Repair a corrupt or damaged XFS filesystem. Using the `-n` option will perform a dry run.
* `xfsdump`: Backs up files and their attributes to a storage media, regular file, or standard output. The `-f` option can be used to specify a device.
* `xfsrestore`: Restore a filesystem from dumps produced by the `xfsdump` utility. The `-f` option is used to specify a source of the dump to be restored.

### Btrfs and ZFS Filesystems

#### Btrfs Filesystem

* The b-tree filesystem, or Btrfs, is a modern copy on write (CoW) filesystem. It introduces advanced features while focusing on fault tolerance, repair, and easy administration. Some of these features include:
  * Space-efficient packing of small files and indexed directories
  * Snapshots (Writable and read-only)
  * Support for software-based RAID
  * Subvolumes (seprate internal filesystem roots)
  * Self-healing
  * Large file size and directory
* `btrfs`: The `btrfs` utility is a toobox for managing Btrfs filesystems.

<pre class="language-bash"><code class="lang-bash"><strong>btrfs &#x3C;command> [&#x3C;args>]
</strong></code></pre>

| Option     | Purpose                                                      |
| ---------- | ------------------------------------------------------------ |
| check      | Balance filesystem chunks across a single or several devices |
| device     | Perform an off-line check on a filesystem                    |
| filesystem | Manae a Btrfs filesystem                                     |
| replace    | Replace Btrfs devices                                        |
| rescue     | Try to rescue a damaged filesystem                           |
| restore    | Try to restore files from a damaged filesystem               |
| subvolume  | Create/Delete/List/Manage Btrfs subvolumes                   |

<pre class="language-bash"><code class="lang-bash"><strong>root@linuxmaster:~# fdisk -l /dev/sdb
</strong>Disk /dev/sdb: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: Volume          
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x56dcfbb5

Device     Boot   Start     End Sectors Size Id Type
/dev/sdb1          2048 2099199 2097152   1G 83 Linux
/dev/sdb2       2099200 4196351 2097152   1G 83 Linux

<strong>root@linuxmaster:~# mkfs.btrfs /dev/sdb1
</strong>btrfs-progs v5.4.1 
See http://btrfs.wiki.kernel.org for more information.

Label:              (null)
UUID:               0384a51c-30da-4c9a-a973-cccbace2210d
Node size:          16384
Sector size:        4096
Filesystem size:    1.00GiB
Block group profiles:
  Data:             single            8.00MiB
  Metadata:         DUP              51.19MiB
  System:           DUP               8.00MiB
SSD detected:       no
Incompat features:  extref, skinny-metadata
Checksum:           crc32c
Number of devices:  1
Devices:
   ID        SIZE  PATH
    1     1.00GiB  /dev/sdb1

<strong>root@linuxmaster:~# mkdir /mnt/btrfs
</strong>
<strong>root@linuxmaster:~# mount /dev/sdb1 /mnt/btrfs/
</strong>
<strong>root@linuxmaster:~# df -h
</strong>Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sdb1       1.0G  3.5M  905M   1% /mnt/btrfs

<strong>root@linuxmaster:~# btrfs device add /dev/sdb2 /mnt/btrfs/
</strong>
<strong>root@linuxmaster:~# df -h
</strong>Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sdb1       2.0G  3.5M  1.9G   1% /mnt/btrfs

<strong>root@linuxmaster:~# btrfs filesystem show /mnt/btrfs/
</strong>Label: none  uuid: 0384a51c-30da-4c9a-a973-cccbace2210d
	Total devices 2 FS bytes used 128.00KiB
	devid    1 size 1.00GiB used 126.38MiB path /dev/sdb1
	devid    2 size 1.00GiB used 0.00B path /dev/sdb2

<strong>root@linuxmaster:~# btrfs balance start --full-balance /mnt/btrfs/
</strong>Done, had to relocate 3 out of 3 chunks

<strong>root@linuxmaster:~# btrfs filesystem show /mnt/btrfs/
</strong>Label: none  uuid: 0384a51c-30da-4c9a-a973-cccbace2210d
	Total devices 2 FS bytes used 256.00KiB
	devid    1 size 1.00GiB used 240.00MiB path /dev/sdb1
	devid    2 size 1.00GiB used 448.00MiB path /dev/sdb2
</code></pre>

<pre class="language-bash"><code class="lang-bash"><strong>root@linuxmaster:~# btrfs subvolume create /mnt/btrfs/svol1
</strong>Create subvolume '/mnt/btrfs/svol1'

<strong>root@linuxmaster:~# btrfs subvolume list /mnt/btrfs/
</strong>ID 258 gen 25 top level 5 path svol1

<strong>root@linuxmaster:~# touch /mnt/btrfs/svol1/test{1..5}.txt
</strong>
<strong>root@linuxmaster:~# ll /mnt/btrfs/svol1/
</strong>total 16
drwxr-xr-x 1 root root 90 Sep  9 12:25 ./
drwxr-xr-x 1 root root 10 Sep  9 12:14 ../
-rw-r--r-- 1 root root  0 Sep  9 12:25 test1.txt
-rw-r--r-- 1 root root  0 Sep  9 12:25 test2.txt
-rw-r--r-- 1 root root  0 Sep  9 12:25 test3.txt
-rw-r--r-- 1 root root  0 Sep  9 12:25 test4.txt
-rw-r--r-- 1 root root  0 Sep  9 12:25 test5.txt

<strong>root@linuxmaster:~# mkdir /mnt/svol1
</strong>
<strong>root@linuxmaster:~# mount -t btrfs -o subvol=svol1 /dev/sdb2 /mnt/svol1/
</strong>
<strong>root@linuxmaster:~# df -h
</strong>Filesystem      Size  Used Avail Use% Mounted on
udev            949M     0  949M   0% /dev
tmpfs           194M  1.1M  193M   1% /run
/dev/sda1        19G  5.2G   13G  30% /
tmpfs           968M     0  968M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           968M     0  968M   0% /sys/fs/cgroup
/dev/sda15      253M  1.1M  252M   1% /boot/efi
tmpfs           194M     0  194M   0% /run/user/0
/dev/sdb1       2.0G  3.7M  1.6G   1% /mnt/btrfs
/dev/sdb1       2.0G  3.7M  1.6G   1% /mnt/svol1

<strong>root@linuxmaster:~# ll /mnt/svol1/
</strong>total 4
drwxr-xr-x 1 root root   90 Sep  9 12:25 ./
drwxr-xr-x 5 root root 4096 Sep  9 12:36 ../
-rw-r--r-- 1 root root    0 Sep  9 12:25 test1.txt
-rw-r--r-- 1 root root    0 Sep  9 12:25 test2.txt
-rw-r--r-- 1 root root    0 Sep  9 12:25 test3.txt
-rw-r--r-- 1 root root    0 Sep  9 12:25 test4.txt
-rw-r--r-- 1 root root    0 Sep  9 12:25 test5.txt

<strong>root@linuxmaster:~# ll /mnt/btrfs/svol1/
</strong>total 16
drwxr-xr-x 1 root root 90 Sep  9 12:25 ./
drwxr-xr-x 1 root root 10 Sep  9 12:14 ../
-rw-r--r-- 1 root root  0 Sep  9 12:25 test1.txt
-rw-r--r-- 1 root root  0 Sep  9 12:25 test2.txt
-rw-r--r-- 1 root root  0 Sep  9 12:25 test3.txt
-rw-r--r-- 1 root root  0 Sep  9 12:25 test4.txt
-rw-r--r-- 1 root root  0 Sep  9 12:25 test5.txt

<strong>root@linuxmaster:~# btrfs subvolume snapshot /mnt/btrfs/svol1/ /mnt/btrfs/snap1
</strong>Create a snapshot of '/mnt/btrfs/svol1/' in '/mnt/btrfs/snap1'

<strong>root@linuxmaster:~# btrfs subvolume list /mnt/btrfs 
</strong>ID 258 gen 27 top level 5 path svol1
ID 259 gen 27 top level 5 path snap1

<strong>root@linuxmaster:~# ll /mnt/btrfs/snap1/
</strong>total 16
drwxr-xr-x 1 root root 90 Sep  9 12:25 ./
drwxr-xr-x 1 root root 20 Sep  9 12:41 ../
-rw-r--r-- 1 root root  0 Sep  9 12:25 test1.txt
-rw-r--r-- 1 root root  0 Sep  9 12:25 test2.txt
-rw-r--r-- 1 root root  0 Sep  9 12:25 test3.txt
-rw-r--r-- 1 root root  0 Sep  9 12:25 test4.txt
-rw-r--r-- 1 root root  0 Sep  9 12:25 test5.txt
</code></pre>

* `btrfs-convert`: Perform an in-place conversion from ext2/3/4 filesystems to Btrfs.

<pre class="language-bash"><code class="lang-bash"><strong>btrfs-convert [options] &#x3C;device>
</strong></code></pre>

<pre class="language-bash"><code class="lang-bash"><strong>root@linuxmaster:~# lsblk -f | grep sdb3
</strong>└─sdb3  ext2         c067a099-27ce-4fb5-84d7-fbe8ec921055

<strong>root@linuxmaster:~# mkfs.ext2 -b 4096 /dev/sdb3
</strong>mke2fs 1.45.5 (07-Jan-2020)
/dev/sdb3 contains a ext2 file system
	created on Fri Sep  9 12:21:01 2022
Proceed anyway? (y,N) y
Discarding device blocks: done                            
Creating filesystem with 262144 4k blocks and 65536 inodes
Filesystem UUID: 6cc56630-86c9-4d54-ba65-43a72b3c5ec5
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376

Allocating group tables: done                            
Writing inode tables: done                            
Writing superblocks and filesystem accounting information: done

<strong>root@linuxmaster:~# btrfs-convert /dev/sdb3 
</strong>create btrfs filesystem:
	blocksize: 4096
	nodesize:  16384
	features:  extref, skinny-metadata (default)
	checksum:  crc32c
creating ext2 image file
creating btrfs metadata
copy inodes [o] [     23504/        11]
conversion complete

<strong>root@linuxmaster:~# btrfs filesystem show /dev/sdb3
</strong>Label: none  uuid: bf9c948e-3575-440c-82e2-65d6b8f2d4fa
	Total devices 1 FS bytes used 17.51MiB
	devid    1 size 1.00GiB used 291.00MiB path /dev/sdb3
</code></pre>

#### **ZFS Filesystem**

The ZFS filesystem, which originally stood for the zettabyte filesystem, combines the features of a filesystem and a volume manager. Like Btrfs, ZFS is a copy on write (CoW) filesystem. Some features of ZFS include:

* Pooled storage
* Snapshots (may be mounted as read-only)
* Data integrity verification
* Self-healing
* Support for RAID and RAID-Z
* Large file size and directory support

## 203.3 Creating and configuring filesystem options (weight: 2)

{% hint style="success" %}
Candidates should be able to configure automount filesystems using AutoFS. This objective includes configuring automount for network and device filesystems. Also included is creating filesystems for devices such as CD-ROMs and a basic feature knowledge of encrypted filesystems.



**Key Knowledge Areas:**

* autofs configuration files
* Understanding of automount units
* UDF and ISO9660 tools and utilities
* Awareness of other CD-ROM filesystems (HFS)
* Awareness of CD-ROM filesystem extensions (Joliet, Rock Ridge, El Torito)
* Basic feature knowledge of data encryption (dm-crypt / LUKS)

**The following is a partial list of the used files, terms and utilities:**

* /etc/auto.master
* /etc/auto.\[dir]
* mkisofs
* cryptsetup
{% endhint %}

### Using AutoFS to Automatically Mount Filesystems

* Indirect mounts: The `auto.master` contains a directory that point to a map file that contians one or more subdirectories (or keys).
* Direct mounts: The directory in the `auto.master` is represented as "/-" and points to a map file that contains the individual directory to be mounted.
* `/etc/sysconfig/autofs`: Configuration file that sets global options for `autofs` mount.
* `/etc/auto.master`: The main configuration file for `autofs`.
* `/etc/auto.map_name`: Individual map files for `autofs`.
* `cat /etc/automaster`: (Indirect Mount Example)

```bash
/mnt/data /etc/auto.data
```

* `cat /etc/auto.data`: (Indirect Mount Example)

```bash
backup -fstype=xfs, ro :/dev/nvme1n1p1
current -fstype=ext4 :/dev/nvme1n1p2
```

* `cat /etc/master`: (Direct Mount Example)

```bash
/- /etc/auto.design
```

* `cat /etc/auto.design`: (Direct Mount Example)

```bash
/mnt/design -fstype=ext3 :/dev/nvme1n1p3
```

* `cat /etc/auto.network`: (Network Mounts Example)

```bash
key -fstype=nfs ip_addr:/nfs/share
key -fstype=cifs,rw,credentials=/etc/creds.txt ://ip_addr/share
```

### CD-ROM Filesystems

* ISO 9660: A filesystem for optical disk media that was published by the International Organization for Standardization. It is a read-only filesystem and has been the de facto standard for CD-ROM disks.
* Universal Disk Format (UDF): A vendor neural filesystem that is used for a wide range of media. It was developed to overcome some of the shortcomings of ISO 9660 and is used often for DVDs and other rewritable media.
* Hierarchial File System (HFS): A filesystem developed by Apple and the native filesystem used on Macintosh computers. It is also used on read-only media like CD-ROMs.
* ISO 9660 Extensions:
  * Rock Ridge (Interchange Protocol) - Offers extensions for longer file names, POSIX permissions, groups, and user IDs, and support for device files and symbolic links.
  * Joliet - A Microsoft extension for the ISO 9660 file system format. It allows Unicode characters and supports long file names.
  * El Torito - Specifies a standard way to make bootable CD-ROMs and DVDs.
* `mkisofs` (genisoimage): Create ISO9660/Joliet/HFS filesystem with optional Rock Ridge attributes.

```bash
mkisofs [options] [-o filename] pathspec [pathspec...]
```

### Data Encryption

* Linux Unified Key Setup (LUKS): A disk encryption specification in Linux. It uses the device mapper crypt kernel module (`dm-crypt`) which provides transparent encryption for block devices.
* `cryptsetup`: Used to setup cryptographic volumes for `dm-crypt` (including LUKS extension).

```bash
cryptsetup <options> <action> <action args>
```

| Action       | Description                                        |
| ------------ | -------------------------------------------------- |
| `create`     | Create a mapping                                   |
| `remove`     | Remove an existing mapping                         |
| `status`     | Report the status for a mapping                    |
| `resize`     | Resize an active mapping                           |
| `luksFormat` | Initilize a LUKS partition and set the initial key |
| `luksOpen`   | Open the LUKS partition and set up a mapping       |
| `luksClose`  | Same as "remove"                                   |

* `smartd`: A daemon that monitors the Self-Monitoring, Analysis and Reporting Technology (SMART) system build into many ATA-3 and later ATA, IDE, and SCSI-3 hard drives.
  * `/etc/smartmontools/smartd.conf` - Define devices and attributes to monitor and mail addresses to notify in case of a failure.
  * `/etc/sysconfig/smartmontools` - Modify startup arguments for `smartd`
* `smartctl`: Control and monitor utility for SMART Disks.

```bash
smartctl [options] device
```

| Option | Description                                            |
| ------ | ------------------------------------------------------ |
| `-i`   | Print information about the device                     |
| `-a`   | Print SMART information about the device               |
| `-x`   | Print SMART and non-SMART information about the device |
| `-s`   | Enable or Disable SMART on a device                    |
| `-t`   | Execute a test of the device (delay with -C)           |

## Sample Questions

**1.** What are the two different types of autofs mounts?

* [ ] manual
* [x] direct
* [ ] auto
* [x] indirect

**2.** What command is used to convert an ext filesystem to Btrfs?

* [x] `btrfs-convert`
* [ ] `btrfs restore`
* [ ] `ext2btrfs`
* [ ] `btrfs replace`

**3.** What is the main configuration file for autofs?

* [x] `auto.master`
* [ ] `autofs`
* [ ] `auto.map`
* [ ] `autofs.conf`

**4.** What configuration file contains startup arguments for smartd to be modified?

* [ ] `smartd`
* [x] `smartmontools`
* [ ] `smartctl`
* [ ] `smartd.conf`

**5.** What are the commands used for enabling or disabling swap space? (Choose two.)

* [x] `swapoff`
* [x] `swapon`
* [ ] `mkswap on`
* [ ] `swap --enable`

**6.** What special type of RAID level was introduced by the ZFS filesystem?

* [ ] RAID-5
* [ ] RAID-3
* [x] RAID-Z
* [ ] RAID-F

**7.** What command is used to set up cryptographic volumes for dm-crypt?

* [ ] `luksFormat`
* [ ] `luksOpen`
* [ ] `cryptenable`
* [x] `cryptsetup`

**8.** What option is used to create a journal using tune2fs?

* [ ] `-l`
* [x] `-j`
* [ ] `-J`
* [ ] `-m`

**9.** What version of the ext filesystem introduced journaling?

* [ ] `ext2`
* [x] `ext3`
* [ ] `ext4j`
* [ ] `ext4`

**10.** What commands can list the contents of the filesystem super block? (Choose two.)

* [ ] `dumpe2fs`
* [x] `debug2fs`
* [ ] `fsck`
* [x] `tune2fs`

**11.** What is the mount command option to supply specific mount options?

* [ ] `-a`
* [x] `-o`
* [ ] `-t`
* [ ] `-O`

**12.** Which of the following is a command used to set up a Linux swap area?

* [ ] `swapon`
* [ ] `mswapctl`
* [x] `mkswap`
* [ ] `swapoff`

**13.** Which of the following are copy-on-write filesystems? (Choose two.)

* [x] btrfs
* [ ] udf
* [ ] ext2
* [x] zfs

**14.** What command writes cached data to persistent storage?

* [ ] `dump`
* [ ] `blkid`
* [ ] `save`
* [x] `sync`

**15.** What file is read to automatically create Systemd mount units?

* [ ] `/etc/systemd/mounts`
* [x] `/etc/fstab`
* [ ] `/run/systemd/generator`
* [ ] `/etc/mtab`

**16.** Which ISO 9660 extension is used to make bootable CD-ROMs and DVDs?

* [ ] Rock Bridge
* [ ] Joliet
* [x] El Torito
* [ ] HFS

**17.** What command is used to display filesystem geometry information for XFS?

* [ ] `xfs_restore`
* [ ] `xfs_repair`
* [ ] `xfs_check`
* [x] `xfs_info`

**18.** What file traditionally maintained a list of currently mounted filesystems?

* [ ] `/proc/mounts`
* [ ] `/etc/fstab`
* [x] `/etc/mtab`
* [ ] `/etc/mountab`

**19.** What is the purpose of a system mount unit?

* [x] It is used by the command `systemd-mount` and allows users to mount partitions to mount points of their choice
* [ ] It is used only to mount network file systems to local mount points. It cannot be used for local media
* [ ] It is created by the command `systemd-fstab-generator` to integrate entries from /`etc/fstab` into the system boot process
* [ ] It is used by the command mount when using system to mount and unmount file systems
