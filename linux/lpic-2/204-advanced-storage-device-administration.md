# 204: Advanced Storage Device Administration

## **204.1 Configuring RAID (weight: 3)**

{% hint style="success" %}
Candidates should be able to configure and implement software RAID. This objective includes using and configuring RAID 0, 1 and 5.



**Key Knowledge Areas:**

* Software RAID configuration files and utilities

**The following is a partial list of the used files, terms and utilities:**

* mdadm.conf
* mdadm
* /proc/mdstat
* partition type 0xFD
{% endhint %}

### **Undrestanding RAID and RAID Levels**

**RAID** stands for R**edundant Array of Independent Disks** and is a storage solution that is used to **organize or combine a group of disks into one or more logical units**.

* Mirroring
* Striping
* Parity

**RAID 0:** Striping

<figure><img src="broken-reference" alt=""><figcaption></figcaption></figure>

**RAID 1:** Mirroring

<figure><img src="broken-reference" alt=""><figcaption></figcaption></figure>

**RAID 5:** Block-Level Stripping with Distributed Parity

<figure><img src="broken-reference" alt=""><figcaption></figcaption></figure>

### Files and Utilities for Interacting with RAID

* `mdadm.conf`: Main configuration file for the management of software raid with the `mdadm` utility
* `/proc/mdstat`: Display a snapshot of the kernel's RAID/md stat.
* `0xFD` partition type: Type ID number that signifies a RAID partition.
* `mdadm`: A utility for creating, managing, and monitoring RAID devices using the md driver in Linux.

<pre class="language-bash"><code class="lang-bash"><strong>mdadm [mode] &#x3C;raiddevice> [options] &#x3C;component-devices>
</strong></code></pre>

| Option | Description                             |
| ------ | --------------------------------------- |
| `-C`   | Create a new array                      |
| `-l`   | Set a RAID level                        |
| `-n`   | Specify number of devices               |
| `-x`   | Specify number of spare devices         |
| `-D`   | Print details of one or more md devices |
| `-a`   | Add one or more devices to the array    |
| `-f`   | Mark listed devices as faulty           |
| `-r`   | Remove listed devices                   |
| `-s`   | Scan the map file for arrays            |
| `-v`   | Increase verbosity                      |

### Creating a Software RAID

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>[root@linuxmaster ~]# lsblk
</strong>NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0 38.2G  0 disk 
└─sda1   8:1    0 38.2G  0 part /
sdb      8:16   0   10G  0 disk 
├─sdb1   8:17   0    1G  0 part 
├─sdb2   8:18   0    1G  0 part 
└─sdb3   8:19   0    1G  0 part 
sdc      8:32   0   10G  0 disk 
├─sdc1   8:33   0    1G  0 part 
└─sdc2   8:34   0    1G  0 part 
sr0     11:0    1 1024M  0 rom  

<strong>[root@linuxmaster ~]# mdadm -C /dev/md0 -l raid5 -n 3 /dev/sdb1 /dev/sdb2 /dev/sdb3 -x 2 /dev/sdc1 /dev/sdc2
</strong>mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.

<strong>[root@linuxmaster ~]# ll /dev/md0
</strong>brw-rw----. 1 root disk 9, 0 Sep 10 10:34 /dev/md0

<strong>[root@linuxmaster ~]# mdadm -D /dev/md0 
</strong>/dev/md0:
           Version : 1.2
     Creation Time : Sat Sep 10 10:34:50 2022
        Raid Level : raid5
        Array Size : 2093056 (2044.00 MiB 2143.29 MB)
     Used Dev Size : 1046528 (1022.00 MiB 1071.64 MB)
      Raid Devices : 3
     Total Devices : 5
       Persistence : Superblock is persistent

       Update Time : Sat Sep 10 10:35:29 2022
             State : clean 
    Active Devices : 3
   Working Devices : 5
    Failed Devices : 0
     Spare Devices : 2

            Layout : left-symmetric
        Chunk Size : 512K

Consistency Policy : resync

              Name : centos-2gb-hel1-1:0
              UUID : 04d4ef0e:16a91fb8:f82fec5f:59832a86
            Events : 18

    Number   Major   Minor   RaidDevice State
       0       8       17        0      active sync   /dev/sdb1
       1       8       18        1      active sync   /dev/sdb2
       5       8       19        2      active sync   /dev/sdb3

       3       8       33        -      spare   /dev/sdc1
       4       8       34        -      spare   /dev/sdc2

<strong>[root@linuxmaster ~]# cat /proc/mdstat
</strong>Personalities : [raid6] [raid5] [raid4] 
md0 : active raid5 sdb3[5] sdc2[4](S) sdc1[3](S) sdb2[1] sdb1[0]
      2093056 blocks super 1.2 level 5, 512k chunk, algorithm 2 [3/3] [UUU]
      
unused devices: &#x3C;none>

<strong>[root@linuxmaster ~]# mdadm -D -s -v
</strong>ARRAY /dev/md0 level=raid5 num-devices=3 metadata=1.2 spares=2 name=linuxmaster:0 UUID=04d4ef0e:16a91fb8:f82fec5f:59832a86
   devices=/dev/sdb1,/dev/sdb2,/dev/sdb3,/dev/sdc1,/dev/sdc2

<strong>[root@linuxmaster ~]# mdadm -D -s -v > /etc/mdadm.conf
</strong>
<strong>[root@linuxmaster ~]# cat /etc/mdadm.conf
</strong>ARRAY /dev/md0 level=raid5 num-devices=3 metadata=1.2 spares=2 name=linuxmaster:0 UUID=04d4ef0e:16a91fb8:f82fec5f:59832a86
   devices=/dev/sdb1,/dev/sdb2,/dev/sdb3,/dev/sdc1,/dev/sdc2
</code></pre>

### Failing Active RAID Devices

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>[root@linuxmaster ~]# mkfs.ext4 /dev/md0
</strong>mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=128 blocks, Stripe width=256 blocks
130816 inodes, 523264 blocks
26163 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=536870912
16 block groups
32768 blocks per group, 32768 fragments per group
8176 inodes per group
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done

<strong>[root@linuxmaster ~]# mkdir /mnt/raid
</strong>
<strong>[root@linuxmaster ~]# mount -t ext4 /dev/md0 /mnt/raid/
</strong>
<strong>[root@linuxmaster ~]# touch /mnt/raid/test{1..5}
</strong><strong>
</strong>[root@linuxmaster ~]# ll /mnt/raid/
total 16
drwx------. 2 root root 16384 Sep 10 11:12 lost+found
-rw-r--r--. 1 root root     0 Sep 10 11:13 test1
-rw-r--r--. 1 root root     0 Sep 10 11:13 test2
-rw-r--r--. 1 root root     0 Sep 10 11:13 test3
-rw-r--r--. 1 root root     0 Sep 10 11:13 test4
-rw-r--r--. 1 root root     0 Sep 10 11:13 test5

<strong>[root@linuxmaster ~]# df -h
</strong>Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        871M     0  871M   0% /dev
tmpfs           895M     0  895M   0% /dev/shm
tmpfs           895M  8.7M  887M   1% /run
tmpfs           895M     0  895M   0% /sys/fs/cgroup
/dev/sda1        38G  1.1G   35G   3% /
tmpfs           179M     0  179M   0% /run/user/0
/dev/md0        2.0G  6.0M  1.9G   1% /mnt/raid

<strong>[root@linuxmaster ~]# mdadm -f /dev/md0 /dev/sdb1
</strong>mdadm: set /dev/sdb1 faulty in /dev/md0

<strong>[root@linuxmaster ~]# mdadm -D /dev/md0 
</strong>/dev/md0:
           Version : 1.2
     Creation Time : Sat Sep 10 10:34:50 2022
        Raid Level : raid5
        Array Size : 2093056 (2044.00 MiB 2143.29 MB)
     Used Dev Size : 1046528 (1022.00 MiB 1071.64 MB)
      Raid Devices : 3
     Total Devices : 5
       Persistence : Superblock is persistent

       Update Time : Sat Sep 10 11:14:30 2022
             State : clean, degraded, recovering 
    Active Devices : 2
   Working Devices : 4
    Failed Devices : 1
     Spare Devices : 2

            Layout : left-symmetric
        Chunk Size : 512K

Consistency Policy : resync

    Rebuild Status : 30% complete

              Name : centos-2gb-hel1-1:0
              UUID : 04d4ef0e:16a91fb8:f82fec5f:59832a86
            Events : 24

    Number   Major   Minor   RaidDevice State
       4       8       34        0      spare rebuilding   /dev/sdc2
       1       8       18        1      active sync   /dev/sdb2
       5       8       19        2      active sync   /dev/sdb3

       0       8       17        -      faulty   /dev/sdb1
       3       8       33        -      spare   /dev/sdc1

<strong>[root@linuxmaster ~]# cat /proc/mdstat 
</strong>Personalities : [raid6] [raid5] [raid4] 
md0 : active raid5 sdb3[5] sdc2[4] sdc1[3](S) sdb2[1] sdb1[0](F)
      2093056 blocks super 1.2 level 5, 512k chunk, algorithm 2 [3/2] [_UU]
      [==============>......]  recovery = 73.2% (766720/1046528) finish=0.2min speed=19605K/sec
      
unused devices: &#x3C;none>

<strong>[root@linuxmaster ~]# mdadm -D /dev/md0 
</strong>/dev/md0:
           Version : 1.2
     Creation Time : Sat Sep 10 10:34:50 2022
        Raid Level : raid5
        Array Size : 2093056 (2044.00 MiB 2143.29 MB)
     Used Dev Size : 1046528 (1022.00 MiB 1071.64 MB)
      Raid Devices : 3
     Total Devices : 5
       Persistence : Superblock is persistent

       Update Time : Sat Sep 10 11:15:10 2022
             State : clean 
    Active Devices : 3
   Working Devices : 4
    Failed Devices : 1
     Spare Devices : 1

            Layout : left-symmetric
        Chunk Size : 512K

Consistency Policy : resync

              Name : centos-2gb-hel1-1:0
              UUID : 04d4ef0e:16a91fb8:f82fec5f:59832a86
            Events : 37

    Number   Major   Minor   RaidDevice State
       4       8       34        0      active sync   /dev/sdc2
       1       8       18        1      active sync   /dev/sdb2
       5       8       19        2      active sync   /dev/sdb3

       0       8       17        -      faulty   /dev/sdb1
       3       8       33        -      spare   /dev/sdc1

<strong>[root@linuxmaster ~]# cat /proc/mdstat 
</strong>Personalities : [raid6] [raid5] [raid4] 
md0 : active raid5 sdb3[5] sdc2[4] sdc1[3](S) sdb2[1] sdb1[0](F)
      2093056 blocks super 1.2 level 5, 512k chunk, algorithm 2 [3/3] [UUU]
      
unused devices: &#x3C;none>

<strong>[root@linuxmaster ~]# ll /mnt/raid/
</strong>total 16
drwx------. 2 root root 16384 Sep 10 11:12 lost+found
-rw-r--r--. 1 root root     0 Sep 10 11:13 test1
-rw-r--r--. 1 root root     0 Sep 10 11:13 test2
-rw-r--r--. 1 root root     0 Sep 10 11:13 test3
-rw-r--r--. 1 root root     0 Sep 10 11:13 test4
-rw-r--r--. 1 root root     0 Sep 10 11:13 test5

<strong>[root@linuxmaster ~]# mdadm -r /dev/sdb1
</strong>mdadm: /dev/sdb1 does not appear to be an md device

<strong>[root@linuxmaster ~]# mdadm -r /dev/sdc2
</strong>mdadm: /dev/sdc2 does not appear to be an md device

<strong>[root@linuxmaster ~]# mdadm -D /dev/md0 
</strong>/dev/md0:
           Version : 1.2
     Creation Time : Sat Sep 10 10:34:50 2022
        Raid Level : raid5
        Array Size : 2093056 (2044.00 MiB 2143.29 MB)
     Used Dev Size : 1046528 (1022.00 MiB 1071.64 MB)
      Raid Devices : 3
     Total Devices : 5
       Persistence : Superblock is persistent

       Update Time : Sat Sep 10 11:31:18 2022
             State : clean 
    Active Devices : 3
   Working Devices : 3
    Failed Devices : 2
     Spare Devices : 0

            Layout : left-symmetric
        Chunk Size : 512K

Consistency Policy : resync

              Name : centos-2gb-hel1-1:0
              UUID : 04d4ef0e:16a91fb8:f82fec5f:59832a86
            Events : 56

    Number   Major   Minor   RaidDevice State
       3       8       33        0      active sync   /dev/sdc1
       1       8       18        1      active sync   /dev/sdb2
       5       8       19        2      active sync   /dev/sdb3

       0       8       17        -      faulty   /dev/sdb1
       4       8       34        -      faulty   /dev/sdc2
</code></pre>

### Adding Devices to an Existing RAID

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>[root@linuxmaster ~]# mdadm -a /dev/md0 /dev/sdd1 /dev/sdd2
</strong>mdadm: added /dev/sdd1
mdadm: added /dev/sdd2

<strong>[root@linuxmaster ~]# cat /proc/mdstat 
</strong>Personalities : [raid6] [raid5] [raid4] 
md0 : active raid5 sdd2[7](S) sdd1[6](S) sdb3[5] sdc2[4](F) sdc1[3] sdb2[1] sdb1[0](F)
      2093056 blocks super 1.2 level 5, 512k chunk, algorithm 2 [3/3] [UUU]
      
unused devices: &#x3C;none>

<strong>[root@linuxmaster ~]# mdadm -D /dev/md0 
</strong>/dev/md0:
           Version : 1.2
     Creation Time : Sat Sep 10 10:34:50 2022
        Raid Level : raid5
        Array Size : 2093056 (2044.00 MiB 2143.29 MB)
     Used Dev Size : 1046528 (1022.00 MiB 1071.64 MB)
      Raid Devices : 3
     Total Devices : 7
       Persistence : Superblock is persistent

       Update Time : Sat Sep 10 11:35:36 2022
             State : clean 
    Active Devices : 3
   Working Devices : 5
    Failed Devices : 2
     Spare Devices : 2

            Layout : left-symmetric
        Chunk Size : 512K

Consistency Policy : resync

              Name : centos-2gb-hel1-1:0
              UUID : 04d4ef0e:16a91fb8:f82fec5f:59832a86
            Events : 58

    Number   Major   Minor   RaidDevice State
       3       8       33        0      active sync   /dev/sdc1
       1       8       18        1      active sync   /dev/sdb2
       5       8       19        2      active sync   /dev/sdb3

       0       8       17        -      faulty   /dev/sdb1
       4       8       34        -      faulty   /dev/sdc2
       6       8       49        -      spare   /dev/sdd1
       7       8       50        -      spare   /dev/sdd2
</code></pre>

### Updating the mdadm.conf File

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>[root@linuxmaster ~]# mdadm -D -s -v > /etc/mdadm.conf 
</strong>
<strong>[root@linuxmaster ~]# cat /etc/mdadm.conf 
</strong>ARRAY /dev/md0 level=raid5 num-devices=3 metadata=1.2 spares=2 name=linuxmaster:0 UUID=04d4ef0e:16a91fb8:f82fec5f:59832a86
   devices=/dev/sdb1,/dev/sdb2,/dev/sdb3,/dev/sdc1,/dev/sdc2,/dev/sdd1,/dev/sdd2
</code></pre>

## **204.2 Adjusting Storage Device Access (weight: 2)**

{% hint style="success" %}
Candidates should be able to configure kernel options to support various drives. This objective includes software tools to view & modify hard disk settings including iSCSI devices.



**Key Knowledge Areas:**

* Tools and utilities to configure DMA for IDE devices including ATAPI and SATA
* Tools and utilities to configure Solid State Drives including AHCI and NVMe
* Tools and utilities to manipulate or analyse system resources (e.g. interrupts)
* Awareness of sdparm command and its uses
* Tools and utilities for iSCSI
* Awareness of SAN, including relevant protocols (AoE, FCoE)

**The following is a partial list of the used files, terms and utilities:**

* hdparm, sdparm
* nvme
* tune2fs
* fstrim
* sysctl
* /dev/hd\*, /dev/sd\*, /dev/nvme\*
* iscsiadm, scsi\_id, iscsid and iscsid.conf
* WWID, WWN, LUN numbers
{% endhint %}

### Direct Memory Access (DMA) and Interrupts Handling

**Direct Memory Access** allows hardware devices (e.g. disk drivers, network cards, sound cards, etc.) to effectively bypass the CPU and send or recive data directly from the main system memory rather than taking up large quantities of proccessing time. This process is managed by a DMA controller (DMAC).

<pre class="language-bash"><code class="lang-bash"><strong>[root@linuxmaster ~]# cat /proc/interrupts 
</strong>           CPU0       CPU1       
  0:        178          0   IO-APIC-edge      timer
  1:         10          0   IO-APIC-edge      i8042
  4:       2382          0   IO-APIC-edge      serial
  8:          1          0   IO-APIC-edge      rtc0
  9:          0          0   IO-APIC-fasteoi   acpi
 12:         15          0   IO-APIC-edge      i8042
 16:          0          0   IO-APIC-fasteoi   i801_smbus
 22:          9          0   IO-APIC-fasteoi   virtio3, virtio4
 24:          1          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 25:          1          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 26:          1          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 27:          1          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 28:          1          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 29:          1          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 30:          1          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 31:          1          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 32:          1          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 33:         25          0   PCI-MSI-edge      xhci_hcd
 34:          0          0   PCI-MSI-edge      xhci_hcd
</code></pre>

A **hardware interrupt or interrupt request (IRQ)** is a signal that informs the processor that a hardware device (e.g. mouse, keyboard, etc.) needs attention. The interrupt suspends the current activity of the processor in order to handle the external event.

<pre class="language-bash"><code class="lang-bash"><strong>[root@linuxmaster ~]# ls -l /proc/irq/
</strong>total 0
dr-xr-xr-x. 2 root root 0 Sep 10 12:02 0
dr-xr-xr-x. 3 root root 0 Sep 10 12:02 1
dr-xr-xr-x. 2 root root 0 Sep 10 12:02 10
dr-xr-xr-x. 2 root root 0 Sep 10 12:02 11
dr-xr-xr-x. 3 root root 0 Sep 10 12:02 12
dr-xr-xr-x. 2 root root 0 Sep 10 12:02 13
dr-xr-xr-x. 2 root root 0 Sep 10 12:02 14
dr-xr-xr-x. 2 root root 0 Sep 10 12:02 15
</code></pre>

### The hdparm Utility

This utility is used to view information about or set SATA/IDE device parameters. Devices of this type will be /dev/hd\*.

| Command                  | Description                                                      |
| ------------------------ | ---------------------------------------------------------------- |
| `hdparm -I /dev/hda`     | Display information about the drive                              |
| `hdparm -d# /dev/hda`    | Get or set the `using_dma` flag for the drive                    |
| `hdparm -t(-T) /dev/hda` | Test timings of device reads (or cache with `-T`) for comparison |
| `hdparm --help`          | View commands and syntax                                         |

### The sdparm Utility

List or change SCSI/SATA device parameters and send simple SCSI commands. Devices of this type will be /dev/dd\*.

| Command                         | Description                                                       |
| ------------------------------- | ----------------------------------------------------------------- |
| `sdparm -I /dev/sda`            | List all known fields for a device                                |
| `sdparm --command=CMD /dev/sda` | Send a SCSI command to the device (e.g. eject, start, stop, etc.) |
| `sdparm --help`                 | View commands and syntax                                          |

```bash
[root@linuxmaster ~]# sdparm /dev/sda
    /dev/sda: QEMU      QEMU HARDDISK     2.5+
Read write error recovery mode page:
  AWRE        1  [cha: n, def:  1]
  ARRE        0  [cha: n, def:  0]
  PER         0  [cha: n, def:  0]
Caching (SBC) mode page:
  WCE         1  [cha: y, def:  1]
  RCD         0  [cha: n, def:  0]

```

### The nvme Utility

A command line utility for managing PCI Express-based devices. Devices of this type will be /dev/nvme\*.

| Command                                  | Description                          |
| ---------------------------------------- | ------------------------------------ |
| `nvme help`                              | Display a list of available commands |
| `nvme list`                              | List all NVMe controllers            |
| `nvme smart-log /dev/nvme0n1`            | Retrieve the smart log               |
| `nvme read/write /dev/nvme0n1 [options]` | Issue IO read or write command       |

{% code overflow="wrap" %}
```bash
[root@linuxmaster ~]# nvme list
Node                  Generic               SN                   Model                                    Namespace Usage                      Format           FW Rev  
--------------------- --------------------- -------------------- ---------------------------------------- --------- -------------------------- ---------------- --------
/dev/nvme0n1          /dev/ng0n1            S5EFNE0N501948       SAMSUNG MZVLB256HBHQ-00A00               1          54.53  GB / 256.06  GB    512   B +  0 B   EXH7201Q
```
{% endcode %}

### The fstrim Utility

A command line utiliy for discarding unwanted block on a mounted filesystem.

* Discards all unused blocks by default but provides options to modify this behavior based on range or size.
* Systemd provides a timer for `fstrim` called `fstrim.timer` that runs on a weekly basis when enabled.
* The `-a` option will run `fstrim` against all mounted file systems.
* The `discard` option can be set in `/etc/fstab` for automatic online TRIM (not recommended).

<pre class="language-bash"><code class="lang-bash"><strong>[root@linuxmaster ~]# fstrim -v /mnt/util
</strong>/: 615.5 MiB (173498368 bytes) trimmed

<strong>[root@linuxmaster ~]# cat /usr/lib/systemd/system/fstrim.timer
</strong>
[Unit]
Description=Discard unused blocks once a week
Documentation=man:fstrim
ConditionVirtualization=!container
ConditionPathExists=!/etc/initrd-release

[Timer]
OnCalendar=weekly
AccuracySec=1h
Persistent=true
RandomizedDelaySec=6000

[Install]
WantedBy=timers.target
</code></pre>

### iSCSI

* iSCSI (Internet Small Computer System Interface): A protocol that allows SCSI commands to be sent over a TCP/IP network. Communication takes place between an initiator (the cliant) and a target (a server).
* ATA over Ethernet (AoE): Allows the Advanced Technology Attachment (ATA) protocol over an ethernet network
* Fiber Channel over Ethernet (FCoE): Allows the Fiber Channel (FC) protocol over an ethernet network
* `targetcli`: A shell for viewing, editing and saving the configuration of the kernel's target subsystem.

**Creating an iSCSI Target:**

* Create a backstore using `nvme1n1`.
* Create a target with a unique iSCSI Qualified Name (IQN).
* Create an ACL, LUN, and portal within the target portal group (TPG)
  * **ACLs**: Access control lists
  * **LUN**: Logical unit number
  * **Portal**: The IP and TCP port pair for an iSCSI target

```bash
[root@linuxmaster ~]# targetcli ls
...
```

### Files and Utilities for the iSCSI Initiator

* `iscsid.conf`: The default configuration file for iSCSI. It contains settings used by `iscsid` and `iscsiadm`.
* `initaltorname.iscsi`: Contains the iSCSI initiator name. This should mirror the name from the ACL of the iSCSI target.
* `iscsid`: A system daemon that manages iSCSI connections.
* `scsi_id`: A utility used to retrieve and generate a unique SCSI identifier. This is primarity used by other utilities such as udev.
* `iscsiadm`: The open-iscsi administration utility.

```bash
iscsiadm -m <mode> [options]
```

| Option | Description                                |
| ------ | ------------------------------------------ |
| `-m`   | Specify the mode                           |
| `-t`   | Specify the discovery type                 |
| `-T`   | Specify the target name                    |
| `-l`   | Login to a specified record                |
| `-p`   | Use target portal with IP address and port |
| `-P`   | Print in tree format                       |

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>[root@linuxmaster ~]# cat /etc/iscsi/initiatorname.iscsi 
</strong>	&#x3C;&#x3C;Add custom initiator name

<strong>[root@linuxmaster ~]# iscsiadm -m discovery -t st -p ip_address
</strong>	&#x3C;&#x3C;Discover the iSCSI target

<strong>[root@linuxmaster ~]# iscsiadm -m node -T iqn.2022-09.com.name.host:t1 -l
</strong>	&#x3C;&#x3C;Login to the target with the discovered IQN

<strong>[root@linuxmaster ~]# grep "Attached SCSI" /var/log/messages
</strong>	&#x3C;&#x3C;View the iSCSI disk name

<strong>[root@linuxmaster ~]# iscsiadm -m node -T iqn.2022-09.com.name.host:t1 -u
</strong>	&#x3C;&#x3C;Log off the iSCSI target204.3 Logical Volume Manager (weight: 3)
</code></pre>

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>[root@linuxmaster ~]# yum install -y targetctl
</strong>...

<strong>[root@linuxmaster ~]# targetcli
</strong>targetcli shell version 2.1.53
Copyright 2011-2013 by Datera, Inc and others.
For help on commands, type 'help'.

<strong>/> backstores/block create block01 /dev/nvme1n1 
</strong>Created block storage object block01 using /dev/nvme1n1.

<strong>/> iscsi/ create iqn.2022-09.com.linuxmaster:linuxmaster1
</strong>Created target iqn.2022-09.com.linuxmaster:linuxmaster1.
Created TPG 1.
Global pref auto_add_default_portal=true
Created default portal listening on all IPs (0.0.0.0), port 3260.

<strong>/> ls
</strong>o- / ............................................................................................. [...]
  o- backstores .................................................................................. [...]
  | o- block ...................................................................... [Storage Objects: 1]
  | | o- block01 ........................................ [/dev/nvme1n1 (2.0GiB) write-thru deactivated]
  | |   o- alua ....................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................... [ALUA state: Active/optimized]
  | o- fileio ..................................................................... [Storage Objects: 0]
  | o- pscsi ...................................................................... [Storage Objects: 0]
  | o- ramdisk .................................................................... [Storage Objects: 0]
  o- iscsi ................................................................................ [Targets: 1]
  | o- iqn.2022-09.com.linuxmaster:linuxmaster1 .............................................. [TPGs: 1]
  |   o- tpg1 ................................................................... [no-gen-acls, no-auth]
  |     o- acls .............................................................................. [ACLs: 0]
  |     o- luns .............................................................................. [LUNs: 0]
  |     o- portals ........................................................................ [Portals: 1]
  |       o- 0.0.0.0:3260 ......................................................................... [OK]
  o- loopback ............................................................................. [Targets: 0]

<strong>/> cd iscsi/iqn.2022-09.com.linuxmaster:linuxmaster1/tpg1/
</strong>
/iscsi/iqn.20...xmaster1/tpg1> luns/ create /backstores/block/block01 
Created LUN 0.

/iscsi/iqn.20...xmaster1/tpg1> acls/ create iqn.2022-09.com.linuxmaster.acg02:master
Created Node ACL for iqn.2022-09.com.linuxmaster.acg02:master
Created mapped LUN 0.

<strong>/iscsi/iqn.20...xmaster1/tpg1> cd /
</strong>
<strong>/> ls
</strong>o- / ............................................................................................. [...]
  o- backstores .................................................................................. [...]
  | o- block ...................................................................... [Storage Objects: 1]
  | | o- block01 .......................................... [/dev/nvme1n1 (2.0GiB) write-thru activated]
  | |   o- alua ....................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................... [ALUA state: Active/optimized]
  | o- fileio ..................................................................... [Storage Objects: 0]
  | o- pscsi ...................................................................... [Storage Objects: 0]
  | o- ramdisk .................................................................... [Storage Objects: 0]
  o- iscsi ................................................................................ [Targets: 1]
  | o- iqn.2022-09.com.linuxmaster:linuxmaster1 .............................................. [TPGs: 1]
  |   o- tpg1 ................................................................... [no-gen-acls, no-auth]
  |     o- acls .............................................................................. [ACLs: 1]
  |     | o- iqn.2022-09.com.linuxmaster.acg02:master ................................. [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................... [lun0 block/block01 (rw)]
  |     o- luns .............................................................................. [LUNs: 1]
  |     | o- lun0 .................................... [block/block01 (/dev/nvme1n1) (default_tg_pt_gp)]
  |     o- portals ........................................................................ [Portals: 1]
  |       o- 0.0.0.0:3260 ......................................................................... [OK]
  o- loopback ............................................................................. [Targets: 0]
  
<strong>/> exit
</strong>Global pref auto_save_on_exit=true
Last 10 configs saved in /etc/target/backup/.
Configuration saved to /etc/target/saveconfig.json

<strong>[root@linuxmaster ~]# systemctl enable target.service --now
</strong>Created symlink from /etc/systemd/system/multi-user.target.wants/target.service to /usr/lib/systemd/system/target.service.

<strong>[root@linuxmaster ~]# yum install -y iscsi-initiator-utils
</strong>...

<strong>[root@linuxmaster ~]# vim /etc/iscsi/initiatorname.iscsi
</strong>InitiatorName=iqn.2022-09.com.linuxmaster.acg02:master

<strong>[root@linuxmaster ~]# systemctl enable iscsid --now
</strong>Created symlink from /etc/systemd/system/multi-user.target.wants/iscsid.service to /usr/lib/systemd/system/iscsid.service.

<strong>[root@linuxmaster ~]# systemctl status iscsid 
</strong>● iscsid.service - Open-iSCSI
   Loaded: loaded (/usr/lib/systemd/system/iscsid.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2022-09-13 12:06:34 UTC; 20s ago
     Docs: man:iscsid(8)
           man:iscsiuio(8)
           man:iscsiadm(8)
 Main PID: 4125 (iscsid)
   Status: "Ready to process requests"
   CGroup: /system.slice/iscsid.service
           └─4125 /sbin/iscsid -f

Sep 13 12:06:34 linuxmaster systemd[1]: Starting Open-iSCSI...
Sep 13 12:06:34 linuxmaster systemd[1]: Started Open-iSCSI.

<strong>[root@linuxmaster ~]# iscsiadm -m discovery -t st -p 172.31.28.253
</strong>172.31.28.253:3260,1 iqn.2022-09.com.linuxmaster:linuxmaster1

<strong>[root@linuxmaster ~]# iscsiadm -m node -T iqn.2022-09.com.linuxmaster:linuxmaster1 -l 
</strong>Logging in to [iface: default, target: iqn.2022-09.com.linuxmaster:linuxmaster1, portal: 172.31.28.253,3260] (multiple)
Login to [iface: default, target: iqn.2022-09.com.linuxmaster:linuxmaster1, portal: 172.31.28.253,3260] successful.

<strong>[root@linuxmaster ~]# grep "Attached SCSI" /var/log/messages
</strong>Sep 13 12:08:17 ip-172-31-28-253 kernel: sd 0:0:0:0: [sda] Attached SCSI disk

<strong>[root@linuxmaster ~]# ll /dev/sda 
</strong>brw-rw----. 1 root disk 8, 0 Sep 13 12:08 /dev/sda

<strong>[root@linuxmaster ~]# ll /dev/disk/by-id/
</strong>total 0
lrwxrwxrwx. 1 root root 13 Sep 13 11:50 nvme-Amazon_Elastic_Block_Store_vol09bb3f1a4dc9b3cbe -> ../../nvme0n1
lrwxrwxrwx. 1 root root 15 Sep 13 11:50 nvme-Amazon_Elastic_Block_Store_vol09bb3f1a4dc9b3cbe-part1 -> ../../nvme0n1p1
lrwxrwxrwx. 1 root root 13 Sep 13 12:01 nvme-Amazon_Elastic_Block_Store_vol0d17c9387510b69db -> ../../nvme1n1
lrwxrwxrwx. 1 root root 13 Sep 13 11:50 nvme-nvme.1d0f-766f6c3039626233663161346463396233636265-416d617a6f6e20456c617374696320426c6f636b2053746f7265-00000001 -> ../../nvme0n1
lrwxrwxrwx. 1 root root 15 Sep 13 11:50 nvme-nvme.1d0f-766f6c3039626233663161346463396233636265-416d617a6f6e20456c617374696320426c6f636b2053746f7265-00000001-part1 -> ../../nvme0n1p1
lrwxrwxrwx. 1 root root 13 Sep 13 12:01 nvme-nvme.1d0f-766f6c3064313763393338373531306236396462-416d617a6f6e20456c617374696320426c6f636b2053746f7265-00000001 -> ../../nvme1n1
lrwxrwxrwx. 1 root root  9 Sep 13 12:08 scsi-36001405b58213e8e6e647d4ad9139dea -> ../../sda
lrwxrwxrwx. 1 root root  9 Sep 13 12:08 wwn-0x6001405b58213e8e6e647d4ad9139dea -> ../../sda

<strong>[root@linuxmaster ~]# lsblk 
</strong>NAME        MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sda           8:0    0   2G  0 disk 
nvme0n1     259:0    0  20G  0 disk 
└─nvme0n1p1 259:1    0  20G  0 part /
nvme1n1     259:2    0   2G  0 disk

<strong>[root@linuxmaster ~]# iscsiadm -m session -P 1
</strong>Target: iqn.2022-09.com.linuxmaster:linuxmaster1 (non-flash)
	Current Portal: 172.31.28.253:3260,1
	Persistent Portal: 172.31.28.253:3260,1
		**********
		Interface:
		**********
		Iface Name: default
		Iface Transport: tcp
		Iface Initiatorname: iqn.2022-09.com.linuxmaster.acg02:master
		Iface IPaddress: 172.31.28.253
		Iface HWaddress: &#x3C;empty>
		Iface Netdev: &#x3C;empty>
		SID: 1
		iSCSI Connection State: LOGGED IN
		iSCSI Session State: LOGGED_IN
		Internal iscsid Session State: NO CHANGE

<strong>[root@linuxmaster ~]# iscsiadm -m node -P 1
</strong>Target: iqn.2022-09.com.linuxmaster:linuxmaster1
	Portal: 172.31.28.253:3260,1
		Iface Name: default
</code></pre>

<figure><img src="broken-reference" alt=""><figcaption></figcaption></figure>

<figure><img src="broken-reference" alt=""><figcaption></figcaption></figure>

## 204.3 Logical Volume Manager (weight: 3)

{% hint style="success" %}
Candidates should be able to create and remove logical volumes, volume groups, and physical volumes. This objective includes snapshots and resizing logical volumes.



**Key Knowledge Areas:**

* Tools in the LVM suite
* Resizing, renaming, creating, and removing logical volumes, volume groups, and physical volumes
* Creating and maintaining snapshots
* Activating volume groups

**The following is a partial list of the used files, terms and utilities:**

* /sbin/pv\*
* /sbin/lv\*
* /sbin/vg\*
* mount
* /dev/mapper/
* lvm.conf
{% endhint %}

### LVM Architecture

<figure><img src="broken-reference" alt=""><figcaption></figcaption></figure>

### Working with Physical Volumes

| Command     | Purpose                                                   |
| ----------- | --------------------------------------------------------- |
| `pvcreate`  | Create (initialize) a physical volume from a block device |
| `pvs`       | Display info about physical volumes                       |
| `pvdisplay` | Display verbose info about physical volumes               |
| `pvscan`    | Scan all supported LVM block devices                      |
| `pvresize`  | Update the size of a physical volume                      |
| `pvremove`  | Remove a physical volume                                  |

<pre class="language-bash"><code class="lang-bash"><strong>[root@linuxmaster ~]# lsblk
</strong>NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0 19.1G  0 disk 
└─sda1   8:1    0 19.1G  0 part /
sdb      8:16   0   10G  0 disk 
├─sdb1   8:17   0    1G  0 part 
└─sdb2   8:18   0    1G  0 part 
sdc      8:32   0   10G  0 disk 
├─sdc1   8:33   0    1G  0 part 
└─sdc2   8:34   0    1G  0 part 
sr0     11:0    1 1024M  0 rom

<strong>[root@linuxmaster ~]# pvcreate /dev/sdb{1,2} /dev/sdc{1,2}
</strong>  Physical volume "/dev/sdb1" successfully created.
  Physical volume "/dev/sdb2" successfully created.
  Physical volume "/dev/sdc1" successfully created.
  Physical volume "/dev/sdc2" successfully created.

<strong>[root@linuxmaster ~]# pvs
</strong>  PV         VG Fmt  Attr PSize PFree
  /dev/sdb1     lvm2 ---  1.00g 1.00g
  /dev/sdb2     lvm2 ---  1.00g 1.00g
  /dev/sdc1     lvm2 ---  1.00g 1.00g
  /dev/sdc2     lvm2 ---  1.00g 1.00g

<strong>[root@linuxmaster ~]# pvscan 
</strong>  PV /dev/sdc1                      lvm2 [1.00 GiB]
  PV /dev/sdb2                      lvm2 [1.00 GiB]
  PV /dev/sdc2                      lvm2 [1.00 GiB]
  PV /dev/sdb1                      lvm2 [1.00 GiB]
  Total: 4 [4.00 GiB] / in use: 0 [0   ] / in no VG: 4 [4.00 GiB]

<strong>[root@linuxmaster ~]# pvdisplay 
</strong>  "/dev/sdc1" is a new physical volume of "1.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdc1
  VG Name               
  PV Size               1.00 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               yayebB-OerJ-U7IE-Ww8h-M1N6-l0dl-ezcXrS
   
  "/dev/sdb2" is a new physical volume of "1.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb2
  VG Name               
  PV Size               1.00 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               O5PA7y-HqOx-q69t-Vret-V9UZ-RqH8-NJuHqB
   
  "/dev/sdc2" is a new physical volume of "1.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdc2
  VG Name               
  PV Size               1.00 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               bOKd2X-yHB3-w0xS-t8JU-uq95-VksQ-KZ0LM6
   
  "/dev/sdb1" is a new physical volume of "1.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb1
  VG Name               
  PV Size               1.00 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               hzCENy-Pic3-8Z9D-Legi-JacD-G6oj-ucfuTM
</code></pre>

### Working with Volume Group

| Command     | Purpose                                                           |
| ----------- | ----------------------------------------------------------------- |
| `vgcreate`  | Create a volume group from physical volumes                       |
| `vgextend`  | Add a physical volume to a volume group                           |
| `vgreduce`  | Remove a physical volume from a volume group                      |
| `vgs`       | Display information about volume groups                           |
| `vgdisplay` | Display verbose information about volume groups                   |
| `vgscan`    | Display information about volume groups and rebuild the LVM cache |
| `vgchange`  | Modify attributes of a volume group                               |
| `vgrename`  | Change the name of a volume group                                 |
| `vgremove`  | Remove a volume group                                             |

<pre class="language-bash"><code class="lang-bash"><strong>[root@linuxmaster ~]# vgcreate data_vg /dev/sdb{1,2}
</strong>  Volume group "data_vg" successfully created

<strong>root@linuxmaster ~]# vgs
</strong>  VG      #PV #LV #SN Attr   VSize VFree
  data_vg   2   0   0 wz--n- 1.99g 1.99g

<strong>[root@linuxmaster ~]# vgscan
</strong>  Reading volume groups from cache.
  Found volume group "data_vg" using metadata type lvm2

<strong>[root@linuxmaster ~]# vgdisplay 
</strong>  --- Volume group ---
  VG Name               data_vg
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               1.99 GiB
  PE Size               4.00 MiB
  Total PE              510
  Alloc PE / Size       0 / 0   
  Free  PE / Size       510 / 1.99 GiB
  VG UUID               z5kweM-C1aR-egdJ-IefJ-f1Bz-VGDC-EXcQzx

<strong>[root@linuxmaster ~]# vgextend data_vg /dev/sdc{1,2}
</strong>  Volume group "data_vg" successfully extended

<strong>[root@linuxmaster ~]# vgs
</strong>  VG      #PV #LV #SN Attr   VSize VFree
  data_vg   4   0   0 wz--n- 3.98g 3.98g
</code></pre>

### Working with Logical Volumes

| Command     | Purpose                                                                                                                                                                             |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `lvcreate`  | <p>Create a logical volume from a volume group</p><ul><li><code>-L</code> - Specify the size</li><li><code>-l</code> - Specify the number of extends or extent percentage</li></ul> |
| `lvs`       | Display info about logical volumes                                                                                                                                                  |
| `lvdisplay` | Display verbose info about logical volumes                                                                                                                                          |
| `lvscan`    | List all logical volumes in all volume groups                                                                                                                                       |
| `lvrename`  | Change the name of a logical volume                                                                                                                                                 |
| `lvchange`  | Adjust the parameters of a logical volume                                                                                                                                           |
| `lvremove`  | Remove a logical volume from a volume group                                                                                                                                         |

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>[root@linuxmaster ~]# lvcreate -L 1G -n backup data_vg
</strong>  Logical volume "backup" created.

<strong>[root@linuxmaster ~]# lvcreate -l 500 -n temp data_vg
</strong>  Logical volume "temp" created.

<strong>[root@linuxmaster ~]# lvcreate -l 50%FREE -n current data_vg
</strong>  Logical volume "current" created.

<strong>[root@linuxmaster ~]# lvs
</strong>  LV      VG      Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  backup  data_vg -wi-a-----   1.00g                                                    
  current data_vg -wi-a----- 528.00m                                                    
  temp    data_vg -wi-a-----   1.95g

<strong>[root@linuxmaster ~]# lvdisplay 
</strong>  --- Logical volume ---
  LV Path                /dev/data_vg/backup
  LV Name                backup
  VG Name                data_vg
  LV UUID                a2GDAc-WoFl-QVQ5-E4YD-ylhX-7RPT-w7B462
  LV Write Access        read/write
  LV Creation host, time linuxmaster, 2022-09-13 14:26:00 +0000
  LV Status              available
  # open                 0
  LV Size                1.00 GiB
  Current LE             256
  Segments               2
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:0
   
  --- Logical volume ---
  LV Path                /dev/data_vg/temp
  LV Name                temp
  VG Name                data_vg
  LV UUID                AAtwqm-CtbZ-9AdO-SLDJ-F61m-gIXc-rxK22j
  LV Write Access        read/write
  LV Creation host, time linuxmaster, 2022-09-13 14:26:53 +0000
  LV Status              available
  # open                 0
  LV Size                1.95 GiB
  Current LE             500
  Segments               2
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:1
   
  --- Logical volume ---
  LV Path                /dev/data_vg/current
  LV Name                current
  VG Name                data_vg
  LV UUID                yRgoQq-u5d2-FiqW-ijZ5-538q-aH1I-UVWOQ0
  LV Write Access        read/write
  LV Creation host, time linuxmaster, 2022-09-13 14:28:10 +0000
  LV Status              available
  # open                 0
  LV Size                528.00 MiB
  Current LE             132
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:2

<strong>[root@linuxmaster ~]# lvremove /dev/data_vg/temp 
</strong>Do you really want to remove active logical volume data_vg/temp? [y/n]: y
  Logical volume "temp" successfully removed
  
<strong>[root@linuxmaster ~]# lvscan 
</strong>  ACTIVE            '/dev/data_vg/backup' [1.00 GiB] inherit
  ACTIVE            '/dev/data_vg/current' [528.00 MiB] inherit

</code></pre>

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>[root@linuxmaster ~]# mkfs.ext4 /dev/data_vg/backup 
</strong>mke2fs 1.42.9 (28-Dec-2013)
Discarding device blocks: done                            
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
65536 inodes, 262144 blocks
13107 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=268435456
8 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done

<strong>[root@linuxmaster ~]# mkfs.ext4 /dev/data_vg/current 
</strong>mke2fs 1.42.9 (28-Dec-2013)
Discarding device blocks: done                            
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
33840 inodes, 135168 blocks
6758 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=138412032
5 block groups
32768 blocks per group, 32768 fragments per group
6768 inodes per group
Superblock backups stored on blocks: 
	32768, 98304

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done

<strong>[root@linuxmaster ~]# mkdir /mnt/backup
</strong>
<strong>[root@linuxmaster ~]# mkdir /mnt/current
</strong>
<strong>[root@linuxmaster ~]# mount -t ext4 /dev/data_vg/backup /mnt/backup/
</strong>
<strong>[root@linuxmaster ~]# mount -t ext4 /dev/data_vg/current /mnt/current/
</strong>
[root@linuxmaster ~]# df -h
Filesystem                   Size  Used Avail Use% Mounted on
devtmpfs                     871M     0  871M   0% /dev
tmpfs                        895M     0  895M   0% /dev/shm
tmpfs                        895M  8.6M  887M   1% /run
tmpfs                        895M     0  895M   0% /sys/fs/cgroup
/dev/sda1                     19G  1.1G   17G   6% /
tmpfs                        179M     0  179M   0% /run/user/0
/dev/mapper/data_vg-backup   976M  2.6M  907M   1% /mnt/backup
/dev/mapper/data_vg-current  504M  804K  466M   1% /mnt/current
</code></pre>

### Resizing Logical Volumes

| Command      | Purpose                                                                                                                                                                                                                              |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `lvextend`   | <p>Grow the size of a logical volume</p><ul><li><code>-L</code> - Specify the size</li><li><code>-l</code> - Specify the number of extens or extent percentage</li><li><code>-r</code> - Resize the underlying file system</li></ul> |
| `lvs`        | Display info about logical volumes                                                                                                                                                                                                   |
| `resize2fs`  | Resize an ext2/ext3/ext4 filesystem                                                                                                                                                                                                  |
| `xfs_growfs` | Expand an XFS filesystem                                                                                                                                                                                                             |
| `lvreduce`   | Reduce the size of a logical volume                                                                                                                                                                                                  |

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>[root@linuxmaster ~]# vgs
</strong>  VG      #PV #LV #SN Attr   VSize VFree 
  data_vg   4   2   0 wz--n- 3.98g &#x3C;2.47g

<strong>[root@linuxmaster ~]# lvextend -L +500M /dev/data_vg/backup 
</strong>  Size of logical volume data_vg/backup changed from 1.00 GiB (256 extents) to &#x3C;1.49 GiB (381 extents).
  Logical volume data_vg/backup successfully resized.

<strong>[root@linuxmaster ~]# df -h
</strong>Filesystem                   Size  Used Avail Use% Mounted on
devtmpfs                     871M     0  871M   0% /dev
tmpfs                        895M     0  895M   0% /dev/shm
tmpfs                        895M  8.6M  887M   1% /run
tmpfs                        895M     0  895M   0% /sys/fs/cgroup
/dev/sda1                     19G  1.1G   17G   6% /
tmpfs                        179M     0  179M   0% /run/user/0
/dev/mapper/data_vg-backup   976M  2.6M  907M   1% /mnt/backup
/dev/mapper/data_vg-current  504M  804K  466M   1% /mnt/current

<strong>[root@linuxmaster ~]# resize2fs /dev/data_vg/backup 
</strong>resize2fs 1.42.9 (28-Dec-2013)
Filesystem at /dev/data_vg/backup is mounted on /mnt/backup; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 1
The filesystem on /dev/data_vg/backup is now 390144 blocks long.

<strong>[root@linuxmaster ~]# df -h
</strong>Filesystem                   Size  Used Avail Use% Mounted on
devtmpfs                     871M     0  871M   0% /dev
tmpfs                        895M     0  895M   0% /dev/shm
tmpfs                        895M  8.6M  887M   1% /run
tmpfs                        895M     0  895M   0% /sys/fs/cgroup
/dev/sda1                     19G  1.1G   17G   6% /
tmpfs                        179M     0  179M   0% /run/user/0
/dev/mapper/data_vg-backup   1.5G  3.0M  1.4G   1% /mnt/backup
/dev/mapper/data_vg-current  504M  804K  466M   1% /mnt/current

<strong>[root@linuxmaster ~]# lvs
</strong>  LV      VG      Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  backup  data_vg -wi-ao----  &#x3C;1.49g                                                    
  current data_vg -wi-ao---- 528.00m

<strong>[root@linuxmaster ~]# lvreduce -l 100 -r data_vg/current
</strong>Do you want to unmount "/mnt/current" ? [Y|n] y
fsck from util-linux 2.23.2
/dev/mapper/data_vg-current: 11/33840 files (0.0% non-contiguous), 6428/135168 blocks
resize2fs 1.42.9 (28-Dec-2013)
Resizing the filesystem on /dev/mapper/data_vg-current to 102400 (4k) blocks.
The filesystem on /dev/mapper/data_vg-current is now 102400 blocks long.

  Size of logical volume data_vg/current changed from 528.00 MiB (132 extents) to 400.00 MiB (100 extents).

  Logical volume data_vg/current successfully resized.

<strong>[root@linuxmaster ~]# lvs
</strong>  LV      VG      Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  backup  data_vg -wi-ao----  &#x3C;1.49g                                                    
  current data_vg -wi-ao---- 400.00m
</code></pre>

### Creating and Managing Snapshots

| Command     | Purpose                                                                                                                                                                                                     |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `lvcreate`  | <p>Create a logical volume</p><ul><li><code>-L</code> - Specify the size</li><li><code>-l</code> - Specify the number of extents or extent percentage</li><li><code>-s</code> - Create a snapshot</li></ul> |
| `lvs`       | Display info about logical volumes                                                                                                                                                                          |
| `lvextend`  | Increate the size of a logical volume or snapshot (COW table)                                                                                                                                               |
| `lvreduce`  | Reduce the size of a logical volume or snapshot (COW table)                                                                                                                                                 |
| `lvremove`  | Remove a logical volume or a snapshot                                                                                                                                                                       |
| `lvchange`  | Adjust the parameters of a logical volume                                                                                                                                                                   |
| `lvconvert` | <p>Change logical volume layout</p><ul><li><code>--merge</code> - Merge a snapshot logical volume into its origin</li></ul>                                                                                 |

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>[root@linuxmaster ~]# df -h
</strong>Filesystem                   Size  Used Avail Use% Mounted on
devtmpfs                     871M     0  871M   0% /dev
tmpfs                        895M     0  895M   0% /dev/shm
tmpfs                        895M  8.6M  887M   1% /run
tmpfs                        895M     0  895M   0% /sys/fs/cgroup
/dev/sda1                     19G  1.1G   17G   6% /
tmpfs                        179M     0  179M   0% /run/user/0
/dev/mapper/data_vg-current  976M  2.6M  907M   1% /mnt/current
/dev/mapper/data_vg-backup   976M  2.6M  907M   1% /mnt/backup

<strong>[root@linuxmaster ~]# touch /mnt/current/test{1..5}.txt
</strong>
<strong>[root@linuxmaster ~]# ll /mnt/current/
</strong>total 16
drwx------. 2 root root 16384 Sep 13 15:02 lost+found
-rw-r--r--. 1 root root     0 Sep 13 15:03 test1.txt
-rw-r--r--. 1 root root     0 Sep 13 15:03 test2.txt
-rw-r--r--. 1 root root     0 Sep 13 15:03 test3.txt
-rw-r--r--. 1 root root     0 Sep 13 15:03 test4.txt
-rw-r--r--. 1 root root     0 Sep 13 15:03 test5.txt

<strong>[root@linuxmaster ~]# lvcreate -l 25%ORIGIN -s -n cur_snap1 /dev/mapper/data_vg-current 
</strong>  Logical volume "cur_snap1" created.

<strong>[root@linuxmaster ~]# lvcreate -L 100M -s -n cur_snap2 /dev/mapper/data_vg-current
</strong>  Logical volume "cur_snap2" created.

<strong>[root@linuxmaster ~]# lvs
</strong>  LV        VG      Attr       LSize   Pool Origin  Data%  Meta%  Move Log Cpy%Sync Convert
  backup    data_vg -wi-ao----   1.00g                                                     
  cur_snap1 data_vg swi-a-s--- 264.00m      current 0.01                                   
  cur_snap2 data_vg swi-a-s--- 100.00m      current 0.01                                   
  current   data_vg owi-aos---   1.00g                                                     
  temp      data_vg -wi-a-----   1.00g

<strong>[root@linuxmaster ~]# cp -r /etc/ /mnt/current/
</strong>
<strong>[root@linuxmaster ~]# lvs
</strong>  LV        VG      Attr       LSize   Pool Origin  Data%  Meta%  Move Log Cpy%Sync Convert
  backup    data_vg -wi-ao----   1.00g                                                     
  cur_snap1 data_vg swi-a-s--- 264.00m      current 1.14                                   
  cur_snap2 data_vg swi-a-s--- 100.00m      current 3.01                                   
  current   data_vg owi-aos---   1.00g                                                     

<strong>[root@linuxmaster ~]# lvdisplay /dev/data_vg/cur_snap1 
</strong>  --- Logical volume ---
  LV Path                /dev/data_vg/cur_snap1
  LV Name                cur_snap1
  VG Name                data_vg
  LV UUID                giAvbe-z4kn-CWDp-cvZx-Vn8U-N86W-bUwEze
  LV Write Access        read/write
  LV Creation host, time linuxmaster, 2022-09-13 15:05:37 +0000
  LV snapshot status     active destination for current
  LV Status              available
  # open                 0
  LV Size                1.00 GiB
  Current LE             256
  COW-table size         264.00 MiB
  COW-table LE           66
  Allocated to snapshot  14.26%
  Snapshot chunk size    4.00 KiB
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:5

<strong>[root@linuxmaster ~]# lvextend -l 50%ORIGIN /dev/mapper/data_vg-cur_snap1
</strong>  Size of logical volume data_vg/cur_snap1 changed from 264.00 MiB (66 extents) to 512.00 MiB (128 extents).
  Logical volume data_vg/cur_snap1 successfully resized.

<strong>[root@linuxmaster ~]# lvs
</strong>  LV        VG      Attr       LSize   Pool Origin  Data%  Meta%  Move Log Cpy%Sync Convert
  backup    data_vg -wi-ao----   1.00g                                                     
  cur_snap1 data_vg swi-a-s--- 512.00m      current 7.47                                   
  cur_snap2 data_vg swi-a-s--- 100.00m      current 38.23                                  
  current   data_vg owi-aos---   1.00g                                                     

<strong>[root@linuxmaster ~]# lvremove data_vg/cur_snap2
</strong>Do you really want to remove active logical volume data_vg/cur_snap2? [y/n]: y
  Logical volume "cur_snap2" successfully removed

<strong>[root@linuxmaster ~]# lvs
</strong>  LV        VG      Attr       LSize   Pool Origin  Data%  Meta%  Move Log Cpy%Sync Convert
  backup    data_vg -wi-ao----   1.00g                                                     
  cur_snap1 data_vg swi-a-s--- 512.00m      current 7.47                                   
  current   data_vg owi-aos---   1.00g                                                     

<strong>[root@linuxmaster ~]# mount /dev/mapper/data_vg-cur_snap1 /mnt/snap/
</strong>
<strong>[root@linuxmaster ~]# ll /mnt/snap/
</strong>total 16
drwx------. 2 root root 16384 Sep 13 15:02 lost+found
-rw-r--r--. 1 root root     0 Sep 13 15:03 test1.txt
-rw-r--r--. 1 root root     0 Sep 13 15:03 test2.txt
-rw-r--r--. 1 root root     0 Sep 13 15:03 test3.txt
-rw-r--r--. 1 root root     0 Sep 13 15:03 test4.txt
-rw-r--r--. 1 root root     0 Sep 13 15:03 test5.txt

<strong>[root@linuxmaster ~]# ll /mnt/current/
</strong>total 20
drwxr-xr-x. 75 root root  4096 Sep 13 15:07 etc
drwx------.  2 root root 16384 Sep 13 15:02 lost+found
-rw-r--r--.  1 root root     0 Sep 13 15:03 test1.txt
-rw-r--r--.  1 root root     0 Sep 13 15:03 test2.txt
-rw-r--r--.  1 root root     0 Sep 13 15:03 test3.txt
-rw-r--r--.  1 root root     0 Sep 13 15:03 test4.txt
-rw-r--r--.  1 root root     0 Sep 13 15:03 test5.txt

<strong>[root@linuxmaster ~]# umount /mnt/current/
</strong>
<strong>[root@linuxmaster ~]# umount /mnt/snap/
</strong>
<strong>[root@linuxmaster ~]# lvchange -an /dev/data_vg/current 
</strong>
<strong>[root@linuxmaster ~]# lvconvert --merge data_vg/cur_snap1 
</strong>  Merging of snapshot data_vg/cur_snap1 will occur on next activation of data_vg/current.

<strong>[root@linuxmaster ~]# lvchange -ay /dev/data_vg/current
</strong>
<strong>[root@linuxmaster ~]# mount /dev/data_vg/current /mnt/current/
</strong>
<strong>[root@linuxmaster ~]# ll /mnt/current/
</strong>total 16
drwx------. 2 root root 16384 Sep 13 15:02 lost+found
-rw-r--r--. 1 root root     0 Sep 13 15:03 test1.txt
-rw-r--r--. 1 root root     0 Sep 13 15:03 test2.txt
-rw-r--r--. 1 root root     0 Sep 13 15:03 test3.txt
-rw-r--r--. 1 root root     0 Sep 13 15:03 test4.txt
-rw-r--r--. 1 root root     0 Sep 13 15:03 test5.txt

<strong>[root@linuxmaster ~]# lvs
</strong>  LV      VG      Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  backup  data_vg -wi-ao---- 1.00g                                                    
  current data_vg -wi-ao---- 1.00g
</code></pre>

## Sample Questions

**1.** Which of the following are protocols used in a Storage Area Network (SAN)? (Choose three.)

* [ ] PaaS
* [x] AoE
* [x] FCoE
* [x] iSCSI

**2.** What command is used to initialize a physical volume?

* [ ] `pvresize`
* [x] `pvcreate`
* [ ] `pvs`
* [ ] `vgcreate`

**3.** What command is used to add a physical volume to a volume group?

* [ ] `pvresize`
* [ ] `vgchange`
* [ ] `vgrename`
* [x] `vgextend`

**4.** What option for the sdparm utility lists all known fields for a device?

* [ ] `--help`
* [ ] `-l`
* [ ] `-v`
* [x] `-a`

**5.** What is the default configuration file for iSCSI?

* [ ] `initiatorname.iscsi`
* [ ] `iscsid.conf`
* [ ] `iscsid`
* [ ] `iscsiadm`

**6.** What are two ways to gain information about a RAID? (Choose two.)

* [x] `cat /proc/mdstat`
* [x] `mdadm -D`
* [ ] `mdamd -f`
* [ ] `mdadm -C`

**7.** What subcommand for the nvme utility will display all NVMe controllers?

* [x] `list`
* [ ] `read`
* [ ] `smart-log`
* [ ] `view-controllers`

**8.** What option for the hdparm utility will retrieve or set parameters the using\_dma option?

* [ ] `-t`
* [ ] `-T`
* [x] `-d`
* [ ] `-l`

**9.** What mode is used to log in to an iSCSI target?

* [x] `node`
* [ ] `discovery`
* [ ] `session`
* [ ] `connect`

**10.** What component in a target portal group is added to initiatorname.iscsi for security?

* [ ] `portal`
* [x] `ACL`
* [ ] `LUN`
* [ ] `backstore`

**11.** What option is used to designate a logical volume as a snapshot?

* [x] `-s`
* [ ] `-n`
* [ ] `-r`
* [ ] `-L`

**12.** What option is used to resize the underlying filesystem of a logical volume?

* [x] `-r`
* [ ] `-l`
* [ ] `-s`
* [ ] `-L`

**13.** What option is used to specify the number of extents for a logical volume?

* [ ] `-L`
* [x] `-l`
* [ ] `-s`
* [ ] `-n`

**14.** What type of technology allows hardware devices to bypass the CPU and communicate with system memory?

* [x] Direct Memory Access
* [ ] Interrupt Requests
* [ ] Memory Access Request
* [ ] Internal Request

**15.** What partition type specifies a RAID partition?

* [ ] The 8E type
* [ ] The 83 type
* [ ] The 82 type
* [x] The FD type

**16.** What option is used to specify a RAID device as faulty?

* [ ] `-C`
* [ ] `-x`
* [ ] `-n`
* [x] `-f`

**17.** What RAID level uses mirroring to prevent data loss?

* [ ] RAID 5
* [x] RAID 1
* [ ] RAID 2
* [ ] RAID 0

**18.** What is the main configuration file for the management of software RAID with the mdadm utility?

* [ ] `mdstat`
* [ ] `md.conf`
* [ ] `mdadm`
* [x] `mdadm.conf`

**19.** What command is used to discard unwanted blocks on a mounted filesystem?

* [x] `fstrim`
* [ ] `dump`
* [ ] `mkfs`
* [ ] `fsck`

