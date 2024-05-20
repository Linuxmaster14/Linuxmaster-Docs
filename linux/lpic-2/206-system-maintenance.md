# 206: System Maintenance

## **206.1 Make and install programs from source (weight: 2)**

{% hint style="success" %}
Candidates should be able to build and install an executable program from source. This objective includes being able to unpack a file of sources.



**Key Knowledge Areas:**

* Unpack source code using common compression and archive utilities.
* Understand basics of invoking make to compile programs.
* Apply parameters to a configure script.
* Know where sources are stored by default.

**The following is a partial list of the used files, terms and utilities:**

* /usr/src/
* gunzip
* gzip
* bzip2
* xz
* tar
* configure
* make
* uname
* install
* patch
{% endhint %}

### Undrestanding Directories for **St**oring and Building Sources Files

* `/usr/src`: The main default for storing sources files. According to the FHS, this directory should only be used for reference. Generally, sources should not be built in this directory heirarchy.

```bash
[root@linuxmaster ~]# ls -l /usr/src/
drwxr-xr-x. 2 root root 4096 Apr 11  2018 linux-aws-5.3-headers-5.3.0-1035hbash
```

* `/usr/local`: For use by the system administrator when installing software locally (based on FHS). `/usr/local/src` can be used for storing source code.

```bash
[root@linuxmaster ~]# ls -l /usr/local/
drwxr-xr-x. 2 root root 4096 Apr 11  2018 bin
drwxr-xr-x. 2 root root 4096 Apr 11  2018 etc
...
drwxr-xr-x. 5 root root 4096 Jul 18 12:47 share
drwxr-xr-x. 2 root root 4096 Apr 11  2018 src
```

* `/opt`: Reserved for the installation of add-on application software packages (based on FHS). Files should be placed in a separate `/opt/<package>` or `/opt/<provider>` directory tree. `<package>` is a name that describes the software package, and `<provider>` is the provider's LANANA (Linux Assigned Names and Numbers Autority).

```bash
[root@linuxmaster ~]# ls -l /opt
drwxr-xr-x. 2 root root 4096 Apr 10  2018 websh
```

### Compression Utilities

* `gzip`: Compresses files using Lempel-Ziv coding (LZYY).`gzip` on only compress single files but may be used to compress tar archives as well. Compresses files should end in .gz, .z, or .tgz in the case of tar files.

```bash
gzip [OPTIONS] [FILENAME(s)]
```

| Option       | Description                                            |
| ------------ | ------------------------------------------------------ |
| `-k`         | Keep the original input file (not available in CentOS) |
| `-c`         | Write output to Standard out                           |
| `-r`         | Compress all files in a directory                      |
| `-1` -> `-9` | Specify the compression level (default is -6)          |
| `-d`         | Decompress a file                                      |
| `-l`         | List information about a compressed file               |
| `-t`         | Test the integrity of the compressed file              |
| `-v`         | Increase verbosity                                     |

* `bzip2 (bunzip2)`: Compresses files using the Burrows-Wheeler block sorting text compression algorithm and Huffman coding. bzip2 files should end in .bz2, .bz, .tbz2 (tar), or .tbz (tar).

```bash
bzip2 [OPTIONS] [FILENAME(s)]
```

| Option       | Description                                     |
| ------------ | ----------------------------------------------- |
| `-k`         | Keep the original input file                    |
| `-c`         | Write output to standard out                    |
| `-r`         | Compress all files in a directory               |
| `-1` -> `-9` | Specify the compression level (default is -9)   |
| `-d`         | Decompress a file                               |
| `-z`         | Force compression regardless of invocation name |
| `-s`         | Reduce memory usage                             |
| `-l`         | List information about a compressed file        |
| `-t`         | Test the integrity of the compressed file       |
| `-v`         | Increase verbosity                              |

* `xz (unzx)`: General-purpose compression tool with a similar command-line syntax to `gzip` and `bzip2`. The file extensions for `xz` is .xz or .txz (tar). The alias `unxz` can be used for decompression.

```bash
xz [OPTIONS] [FILENAME(s)]
```

| Option       | Description                                      |
| ------------ | ------------------------------------------------ |
| `-k`         | Keep the original file (not available in CentOS) |
| `-c`         | Write output to standard out                     |
| `-e`         | Use a slower variant of the compression level    |
| `-0` -> `-9` | Specify the compression level (default is -6)    |
| `-d`         | Decompress a file                                |
| `-l`         | List information about a compressed file         |
| `-t`         | Test the integrity of the compressed file        |
| `-v`         | Increase verbosity                               |

* `tar`: Creates a tar file by converting groups of files and directories into an archive. It was originally used to create archives on magnetic tapes, which is where the name comes from. Tar files end with .tar extension followed by the compression that was used, if any (e.g. .tar.gz).

```bash
tar [OPTIONS] [ARCHIVE_NAME] [FILENAME(s)]
```

| Option       | Description                                                                       |
| ------------ | --------------------------------------------------------------------------------- |
| `-c`         | Create an archive                                                                 |
| `-x`         | Extract an archive                                                                |
| `-t`         | List the content of an archive                                                    |
| `-f`         | Specifgy the archive file name                                                    |
| `-v`         | Show files being processed by the command                                         |
| `-z`         | Filter the archive through gzip                                                   |
| `-j`         | Filter the archive through bzip2                                                  |
| `-J`         | Filter the archive through xz                                                     |
| `-C`         | Choose a directory to extract the archive to                                      |
| `-A`         | Append an archive to the end of another archives (does not work with compression) |
| `-d`         | Find differences between the archive and the file system                          |
| `--delete`   | Delete a member from the archive (does not work with compression)                 |
| `-r`         | Append a file to the end of an archive                                            |
| `--wildcard` | Wildcards match                                                                   |

### Install a Program from Source

* `configure:` This script checks the system to ensure that the project can be built and create the Makefile. Allows for options that can alter the build (see `--help` to see a list of all the options)
* `Makefile`: Contain the instructions that are required in order to build the project.
* `make`: Reads the project specific Makefile and performs all the actions required in order to build (compile) the program.
* `make install`: Installs the compiled program to a default location or the one specified by the configuration script when using the `--prefix` option.

#### Patching a Program from Source

* `diff`: Compress files line by line. Output displayed can vary based on options passed to the `diff` command.

```bash
diff [OPTION]... FILES
```

| Option   | Description                                  |
| -------- | -------------------------------------------- |
| `--help` | List options and descriptions                |
| `-u`     | Output lines of unified text                 |
| `-r`     | Recursively compare any subdirectories found |
| `-N`     | Treat absent files as empty                  |
| `-y`     | Output in two columns                        |

* `patch`: `patch` takes a patch file containing a difference listing produced by the `diff` command and applies those differences to one or more original files, producing patched versions.

```bash
patch -pnum <patchfile>
# or
patch [options] [oroginalfile [patchfile]]
```

| Option      | Description                                       |
| ----------- | ------------------------------------------------- |
| `--help`    | List options and descriptions                     |
| `-d`        | Change to the directory specified                 |
| `-b`        | Create a backup of the original file              |
| `-i`        | Read the patch from the .patch file               |
| `-p[#]`     | Strip slashes from the file path to the file name |
| `-R`        | Revese the previous patch                         |
| `-u`        | Interpret patch file as unified context `diff`    |
| `-n`        | Interpret patch file as a normal `diff`           |
| `-s`        | Run the command silently                          |
| `--dry-run` | Print the result of a patch without applying them |

## **206.2 Backup operations (weight: 3)**

{% hint style="success" %}
Candidates should be able to use system tools to back up important system data.



**Key Knowledge Areas:**

* Knowledge about directories that have to be included in backups
* Awareness of network backup solutions such as Amanda, Bacula, Bareos and BackupPC
* Knowledge of the benefits and drawbacks of tapes, CDR, disk or other backup media
* Perform partial and manual backups.
* Verify the integrity of backup files.
* Partially or fully restore backups.

**The following is a partial list of the used files, terms and utilities:**

* /bin/sh
* dd
* tar
* /dev/st\* and /dev/nst\*
* mt
* rsync
{% endhint %}

### Undrestanding Directories to Consider for Backup

#### Reviewing Directories for Backup

* `/home`: User home directories. Contains user-specific configuration files and data.
* `/etc`: System-wide conf files are used to of a program.
* `/usr/local`: Contains locally installed software. The directory structure is purposefully similar to that of the root directory.
* `/opt`: Reserved for the installation of add-on application software packages.
* `/root`: Home directory of the root user. operation Contains configuration files and data for the root user.
* `/srv`: Data for services provided by the system.

```bash
[root@linuxmaster ~]# tree -L 1
.
├── bin -> usr/bin
├── boot
├── dev
├── etc
├── home
├── lib -> usr/lib
├── lib32 -> usr/lib32
├── lib64 -> usr/lib64
├── libx32 -> usr/libx32
├── lost+found
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin -> usr/sbin
├── srv
├── sys
├── tmp
├── usr
└── var
```

#### Reviewing Subdirectories in /var for Backup

* `/var/backups` (Debian): Contains apt and dpkg package status backups, along with other important system file backups (e.g., `/etc/passwd`).
* `/var/lib`: Contains variable state information for applications and the system.
* `/var/log`: Contains system and application logs.
* `/var/mail`: Contains local user mail.
* `/var/www`: A default location for the web root of a web server, which can contain web content.

```bash
[root@linuxmaster ~]# tree -L 1 /var
/var/
├── backups
├── cache
├── crash
├── lib
├── local
├── lock -> /run/lock
├── log
├── mail
├── opt
├── run -> /run
├── spool
└── tmp
```

### Comparing Backup Media

#### Magnetic Tape Storage

* **Pros:** Cost Efficient, Storage Capacity, Reliability, Security
* **Cons:** Higher Upfront Cost, Slower Data Access, Susceptibility on Natural Disasters
* The `mt` Utility: The `mt` utility is provided bt the `mt-st` package and is used to control magnetic tape drive operations on a Linux system. Tape devices appear as `/dev/st` for rewinding tape devices and `/dev/nst` for non-rewinding tape devices.

#### Optical Media Storage

* **Pros:** Compact Physical Size, Low Cost, Read/Write Speed
* **Cons:** Limited Storage Capacity, Questionable Durability, Variable Speed

#### Hard Disk Storage

* **Pros:** Cost Efficient, High Storage Capacity, Reliability, Wide Availability
* **Cons:** Slower Speed Compared to SSDs, Larger Physical Size, Higher Power Usage, Durability



### Comparing Network Backup Solutions

#### AMANDA

"AMANDA, the Advanced Maryland Automatic Network Disk Archiver, is a backup solution that allows the IT administrator to set up a single primary backup server to back up multiple hosts over network to tape drives/changers or disks or optical media. Amanda uses native utilities and formats (e.g., dump and/or GNU tar) and can back up a large number of servers and workstations running multiple versions of Linux or Unix. Amanda uses a native Windows client to back up Microsoft Windows desktops and servers." (www.amanda.org)

* Developed at the University of Maryland
* Used to back up multiple computers on a network
* Implements a client-server model
* Has a free community edition and a paid enterprise edition
* Has the ability to back up multiple versions of Linux and Unix
* Includes a native Windows client to backup Microsoft Windows desktops and servers
* Supports tape-, disk-, and cloud-based backups

#### Bacula

"Bacula is a set of computer programs that permits the system administrator to manage backup, recovery, and verification of computer data across a network of computers of different kinds. Bacula can also run entirely upon a single computer and can backup to various types of media, including tape and disk." (www.bacula.org)

* Implements a client-server model
* Supports Linux, Unix, Windows, and macOS
* Bacula Systems provides support contracts for Bacula Enterprise Edition
* Uses various storage media for backups (e.g., tape and disk)
* Has a modular design
* A robust backup solution intended for more advanced users
* Bacula components: Bacula Director, Bacula Console, Bacula File, Bacula Storage, Bacula Catalog, Bacula Monitor

#### **Bareos**

"Bareos (Backup Archiving Recovery Open Sourced) is a reliable, cross-network open source software for backup, archiving and recovery of data for all well-established operating systems. Emerged from the Bacula Project in 2010, Bareos was and is actively developed as a fork and enriched with lots of new features." (www.bareos.com)

* Stands for Backup Archiving Recovery Open Sourced (Bareos)
* Is a fork of Bacula that emerged in 2010
* Has an updated web interface as well as other features
* The Bareos project and documentation can be found at www.bareos.org - Professional services are provided at www.bareos.com
* Supports multiple storage media (e.g., disk, tape, and cloud)
* Implements the same core components as Bacula

#### **BackupPC**

"BackupPC is a high-performance, enterprise-grade system for backing up Linux, Windows and macOS PCs and laptops to a server's disk. BackupPC is highly configurable and easy to install and maintain." (https://backuppc.github.io/backuppcn/)

* Hosted on GitHub and distributed under the GPL license
* Provides a web interface for initiating/restoring backups and for viewing logs and configurations
* Uses data deduplication (or pooling), so identical files across multiple backups are only stored once
* Uses compression on new files (not already pooled)
* Does not require any client-side software
* The `smb` protocol is used to extract backup data on Windows clients
* `tar over ssh/ rsh/nfs` is used to back up data on Linux systems (`rsync` is also supported)

### Creating and Restoring Backups

* `rsync`: `rsync` stands for remote sync and is used for copying and synchronizing files and directories, both remotely and locally.

```bash
rsync [OPTIONS] SOURCE DESTINATION
```

| Option                    | Description                                                      |
| ------------------------- | ---------------------------------------------------------------- |
| `-a`                      | Use archive mode which preserves permissions, ownership, etc.    |
| `-b`                      | Performs a backup during data synchronization                    |
| `-e`                      | Specify a protocol                                               |
| `-h`                      | Output in a human-readable format                                |
| `-n`                      | Performs a dry run                                               |
| `-r`                      | Copy data recursively (do not keep ownership, permissions, etc.) |
| `-u`                      | Skip files that are newer on the destination                     |
| `-v`                      | Use verbose output                                               |
| `-z`                      | Use compression                                                  |
| `--progress`              | Show transfer progress                                           |
| `--include` / `--exclude` | Choose files/directories to include                              |
| `--delete`                | Delete any file or directory not located in the source           |
| `--remove-sources-files`  | Removes source files after a successful transfer                 |

* `dd`: The `dd` command is used to vonvert and copy files. This extends to entire disks/partitions, which cane be transferred to and from files or other disks/partitions.

```bash
dd [OPERAND]
# or
dd [OPTION]
```

| Option  | Description                                                                                                                                                                                                                                                                  |
| ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `if`    | The input file (file/disk/partition)                                                                                                                                                                                                                                         |
| `of`    | The output file (file/disk/partition)                                                                                                                                                                                                                                        |
| `bs`    | Read and write BYTES at a time (default: 512)                                                                                                                                                                                                                                |
| `count` | Number of blocks to transfer                                                                                                                                                                                                                                                 |
| `ibs`   | Read up to BYTES at a time (default: 512)                                                                                                                                                                                                                                    |
| `obs`   | write BYTES at a time (default: 512)                                                                                                                                                                                                                                         |
| `conv`  | <p>Applies data conversion (use command-seprated list):</p><p><code>noerror</code> - dd will continue running even if it encounters error</p><p><code>notrunc</code> - dd will not truncate the output file</p><p><code>sync</code> - dd will pad input blocks with NULs</p> |

```bash
# Examples ...

# Copy an HDD to another HDD usin noerror and sync
[root@linuxmaster ~]# dd if=/dev/sda of=/dev/sdb conv=noerror,sync

# Create an image of the HDD
[root@linuxmaster ~]# dd if=/dev/sda of=/home/sda.img

# Restore an image of the HDD
[root@linuxmaster ~]# dd if=/home/sda.img of=/dev/sda

# Create a backup of a CDROM using a block size of 2048
[root@linuxmaster ~]# dd if=/dev/cdrom of=/home/backup.iso bs=2048

# Overwrite a disk write zero using a block size of 4096
[root@linuxmaster ~]# dd if=/dev/zero of=/dev/sda bs=4096
```

## 206.3 Notify users on system-related issues (weight: 1)

{% hint style="success" %}
Candidates should be able to notify the users about current issues related to the system.



**Key Knowledge Areas:**

* Automate communication with users through logon messages.
* Inform active users of system maintenance

**The following is a partial list of the used files, terms and utilities:**

* /etc/issue
* /etc/issue.net
* /etc/motd
* wall
* shutdown
* systemctl
{% endhint %}

#### Using Automated Messages to Communicate System-Related Issues

* `issue`: `/etc/issue` is a text file that contains a message or system identification to be printed before the login prompt. It may contain various `@char` and `\char` sequences, if supported by the getty-type program employed on the system.
* `issue.net`: `/etc/issue.net` is the historical location for pre-login banners for network logins using protocols lik: `telnet` or `rlogin` but can be used for SSH (escaped characters are not processed).

**IMPORTANT:** To add a message for remote logins, the banner path in `/etc/ssh/sshd_config` must be updated and the `sshd` service must be restarted.

| Option | Description                                                                                    |
| ------ | ---------------------------------------------------------------------------------------------- |
| `\b`   | Insert the baud rate of the current line.                                                      |
| `\d`   | Insert the current date.                                                                       |
| `\s`   | Insert the system name, the name of the operating system.                                      |
| `\l`   | Insert the name of the current tty line.                                                       |
| `\m`   | Insert the architecture identifier of the machine.                                             |
| `\n`   | Insert the node name of the machine, also known as the hostname.                               |
| `\o`   | Insert the domain name of the machine.                                                         |
| `\r`   | Insert the release number of the OS. Insert the current time.                                  |
| `\u`   | Insert the number of current users logged in.                                                  |
| `\U`   | Insert the string "1 user" or" users", where "users" is the number of current users logged in. |
| \\`v`  | Insert the version of the OS, e.g., the build date, etc.                                       |

* `/etc/motd`: The contents of `/etc/motd` are displayed after a successful login before the login shell is executed. The abbreviation "motd" stands for "message of the day", and this file has been traditionally used for exactly that (it requires much less disk space than mail to all users).

```bash
[root@linuxmaster ~]# cat /etc/motd
Welcome to Ubuntu 18.04.05 LTS (GNU/Linux 5.4.0-1037 x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

 * What are your preferred Linux desktop apps?  Help us set the default
   desktop apps in Ubuntu 18.04 LTS:
   - https://ubu.one/apps1804

10 packages can be updated.
0 updates are security updates.

Last login: Fri Aug 28 18:41:49 2021 from 192.168.0.11
```

* `wall`: `wall` displays a message, the contents of a file, or otherwise its standard input on the terminals of all currently logged-in users.

```bash
wall [-n] [-t timeout] [-g group] [message | file]
```

The `/usr/bin/wall` command send simple messages to certain system users. Only users who meet the following conditions will receive these messages: Users who are currently into a terminal (tty#) or a terminal-emulator (pts/#), and Users who have their message status set to "yes"

```bash
[root@linuxmaster ~]# mesg
is n

[root@linuxmaster ~]# mesg y

[root@linuxmaster ~]# mesg
is y
```

| Option | Description                                                         |
| ------ | ------------------------------------------------------------------- |
| `-n`   | Suppress the banner (do not print banner, works only for root)      |
| `-t`   | Abandon the write attempt after the time specified (default is 300) |
| `-g`   | Limit message to members of a group (or GID)                        |
| `-V`   | Display version information                                         |
| `-h`   | Display help text                                                   |

* `shutdown`: `shutdown` is used to halt, power off, or reboot the machine.
  * A shutdown time may be specified in "hh:mm" format or with `+m` where `m` is the number of minutes from now (`now` is an alias for `+0`)
  * In order to send a wall message, you must specify a time argument.

```bash
shutdown [OPTIONS...] [TIME] [WALL...]
```

| Option                     | Description                                                |
| -------------------------- | ---------------------------------------------------------- |
| <p><code>-H</code><br></p> | Halt the machine                                           |
| `-P`                       | Power off the machine (this is the default)                |
| `-r`                       | Reboot the machine                                         |
| `-k`                       | Do not halt, power off, or reboot; just write wall message |
| `-c`                       | Cancel a pending shutdown                                  |
| `--no-wall`                | Do not send wall message before halt, power-off, reboot.   |

## Commands and Notes

* `/usr/src`: The main default for storing sources files.
* `/usr/local`: For use by the system administrator when installing software locally (based on FHS). `/usr/local/src` can be used for storing source code.
* `/opt`: Reserved for the installation of add-on application software packages (based on FHS).
* `gzip`: Compresses files using Lempel-Ziv coding (LZYY).
* `bzip2` (`bunzip2`): Compresses files using the Burrows-Wheeler block sorting text compression algorithm and Huffman coding.
* `xz` (`unzx`): General-purpose compression tool with a similar command-line syntax to gzip and `bzip2`.
* `tar`: Creates a tar file by converting groups of files and directories into an archive.
* `configure`: This script checks the system to ensure that the project can be built and create the Makefile.
* `Makefile`: Contain the instructions that are required in order to build the project.
* `make`: Reads the project specific `Makefile` and performs all the actions required in order to build (compile) the program.
* `make install`: Installs the compiled program to a default location or the one specified by the configuration script when using the `--prefix` option.
* `diff`: Compress files line by line. Output displayed can vary based on options passed to the `diff` command.
* `patch`: `patch` takes a patch file containing a difference listing produced by the `diff` command and applies those differences to one or more original files, producing patched versions.
* `rsync`: `rsync` stands for remote sync and is used for copying and synchronizing files and directories, both remotely and locally.
* `dd`: The `dd` command is used to vonvert and copy files.
* `issue`: `/etc/issue` is a text file that contains a message or system identification to be printed before the login prompt.
* `issue.net`: `/etc/issue.net` is the historical location for pre-login banners for network logins using protocols lik: `telnet` or `rlogin` but can be used for SSH (escaped characters are not processed).
* `/etc/motd`: The contents of `/etc/motd` are displayed after a successful login before the login shell is executed.
* `wall`: `wall` displays a message, the contents of a file, or otherwise its standard input on the terminals of all currently logged-in users.
* `shutdown`: `shutdown` is used to halt, power off, or reboot the machine.

## Sample Questions

**1.** What are the three steps in installing a program from source? (Choose three.)

* [ ] Makefile
* [x] make
* [x] configure
* [x] make install

**2.** What option of the `tar` command uses `xz` compression?

* [ ] `-j`
* [x] `-J`
* [ ] `-z`
* [ ] `-c`

**3.** What is the default directory for storing source code (as a reference location)?

* [ ] `/etc`
* [ ] `/opt`
* [ ] `/usr/local`
* [x] `/usr/src`

**4.** What network backup solution is a fork of Bacula?

* [ ] BackupPC
* [ ] Nagios
* [x] Bareos
* [ ] AMANDA

**5.** What command is used to create a patch file?

* [ ] `make`
* [ ] `patch`
* [ ] `make patch`
* [x] `diff`

**6.** What option for the rsync command applies compression?

* [x] `-z`
* [ ] `-r`
* [ ] `-a`
* [ ] `-u`

**7.** Which directory contains system-wide configuration files?

* [x] `/etc`
* [ ] `/srv`
* [ ] `/usr/local`
* [ ] `/home`

**8.** What option for the wall command will limit a message to members of a group?

* [ ] `-h`
* [x] `-g`
* [ ] `-n`
* [ ] `-t`

**9.** What file is presented to users who attempt to log in locally?

* [x] `/etc/issue`
* [ ] `/etc/ssh/sshd_config`
* [ ] `/etc/motd`
* [ ] `/etc/issue.net`

**10.** Which of the following commands will allow a message to be sent to any tty terminals with write access allowed? (Choose all that apply.)

* [ ] `mesg`
* [ ] `echo`
* [ ] `mail`
* [x] `wall`

**11.** To shut down your system without sending a message to users, use which of the following /sbin/shutdown options?

* [ ] `-c`
* [ ] `-k`
* [x] `--no-wall`
* [ ] `--wall`
* [ ] None of the above

**12.** Your company’s legal department has decided that a login notification is needed on a Linux system that you manage, warning that unauthorized system use is prohibited. Which method is the best one for deploying this legal notification?

* [x] Edit the `/etc/issue` file, and place the notification there.
* [ ] Edit the `/etc/motd` file, and place the notification there.
* [ ] Use the `notify-send` command to send the notification to users.
* [ ] Edit the `∼./bashrc` file, and place the notification there.
* [ ] None of the above.

**13.** Assume that money is plentiful, and you want the fastest media to use for your company’s backups. Based on that information, which is the best backup media choice?

* [ ] Magnetic tapes
* [ ] Optical discs
* [ ] HDD
* [x] SSD
* [ ] NAS

**14.** Your co-worker, also a system administrator, hands you an SSD and says, “This has all the files on it that were created and/or modified since the system’s last full backup.” Which of the following backup types can this backup be? (Choose all that apply.)

* [ ] Full
* [x] Incremental
* [x] Differential
* [ ] None of the above
* [ ] All of the above

**15.** Which of the following Linux directories should be included in a backup when you plan on using the backup data in full system restores? (Choose all that apply.)

* [x] `/bin`
* [x] `/home`
* [ ] `/run`
* [ ] `/tmp`
* [ ] All of the above

**16.** Which of the following software packages are GUI and/or web-based backup solutions? (Choose all that apply.)

* [x] Amanda
* [x] Duplicity
* [ ] Nagios
* [x] Bacula
* [ ] `connectd`

**17.** Which of the following `tar` command options would employ `gzip` compression for creating a backup tarball? (Choose all that apply.)

* [ ] `-g`
* [x] `--gzip`
* [x] `-z`
* [ ] `-g`
* [x] `z`

**18.** When using the tar command for an incremental backup, a snapshot file is created. This snapshot file should have which of the following file extensions as part of its name?

* [ ] `.tar`
* [ ] `.tgz`
* [ ] `.tar.snap`
* [x] `.snap`
* [ ] `.snar`

**19.** To verify a tar backup immediately after it is created, you use which of the following `tar` command options? (Choose all that apply.)

* [x] `--compare`
* [x] `--diff`
* [x] `-d`
* [x] `-W`
* [ ] `-J`

**Answer:** A, B, C, D. The --compare and --diff (options A and B) are long-style options of -d (option C). These three options compare a tar archive file’s members with external files and list the differences. Option D, the -W option (long style is --verify), automatically verifies a tar archive as soon as it is created. However, it cannot be used with tarballs, only tar archives. Option E, -J, is a tar option that causes the tar command to employ xz compression.

**20.** Which of the following indicates a tape device that automatically rewinds? (Choose all that apply.)

* [x] `/dev/st0`
* [ ] `/dev/nst0`
* [x] `/dev/ht1`
* [ ] `/dev/nht1`
* [ ] `/dev/nht0`

**Answer:** A, C. The /dev/st0 (SCSI) and /dev/ht1 (PATA) tape device files are for automatically rewinding tape devices. Options B, D, and E all point to non-rewinding tape devices as indicated by the leading n in their device filename, /dev/n\*.

**21.** To control a magnetic tape for backups, use the ...... program.

* [ ] `tar`
* [ ] `star`
* [x] `mt`
* [ ] `rsync`
* [ ] `dd`

**22.** Which of the following rsync commands is the proper way to send a backup over the network to the remote host, ServerA, while having the transfer encrypted using OpenSSH?

* [ ] `rsync -av Project /run/media/ServerA`
* [ ] `rsync -av Project ServerA`
* [ ] `rsync -av ServerA Project`
* [x] `rsync -av Project ServerA:∼/`
* [ ] `rsync -av Project rsync://ServerA:/`

**23.** Which of the following are true about the `dd` command? (Choose all that apply.)

* [x] The `dd` command can be used to create a low-level copy of a hard drive.
* [ ] The `dd` command is popular for daily incremental backups.
* [ ] The `dd` command can be safely used to back up a disk mounted at /.
* [ ] The `dd` command can be used for managing tapes.
* [x] The `dd` command can be used to zero-out a hard disk.

**24.** When installing a program from source code, which of the following installation files typically can provide you with additional installation information? (Choose all that apply.)

* [x] `README`
* [x] `INSTALL`
* [ ] `COPYING`
* [ ] `RELEASE-NOTES`
* [ ] `NEWS`

**25.** During the process of installing a program from source, which of the following creates or updates the Makefile with the Makefile.in file’s contents and what it finds on your system?

* [ ] `INSTALL`
* [x] `configure`
* [ ] `make`
* [ ] `make install`
* [ ] `cp`

**26.** to build and install a program. However, the installation fails. What could be done to install the program? (Choose two.)

* [ ] Install the binaries manually with `suinstall`
* [x] Run `make install` with root privileges
* [ ] Do not run `./configure` in order to maintain the default configuration for correct installation
* [x] Rerun `./configure` with a `--prefix` option where the user has permissions to write
* [ ] Run make install\_local to install into `/usr/local/`

**27.** The following command has just been run successfully:\
`cd /opt; tar xvf /dev/nst0;`

What will happen if the command sequence is run again?

* [ ] An error saying that there is no tape present is generated because the tape has been ejected after being used
* [ ] The contents of `/opt` will be restored again
* [ ] The entire contents of `/opt` will be replaced with the contents of the next file on the tape
* [x] The contents of `/opt` will have additional content added from the next file on the tape

**28.** A system has one hard disk and one CD writer which are both connected to SATA controllers. Which device represents the CD writer?

* [ ] `/dev/hdb`
* [ ] `/dev/sdd`
* [ ] `/dev/scd1`
* [x] `/dev/sr0`
* [ ] `/dev/sr1`

**29.** What command ensures that the file systems are written to disk after a lot of write operations? (Specify ONLY the command without any path or parameters.)

**Answer:** `sync`

**30.** Which of the following is a CD-ROM filesystem standard?

* [ ] OSI 9660
* [x] ISO 9660
* [ ] SR0FS
* [ ] ISO 8859
* [ ] ROM-EO

**31.** Which of the following commands restores only those files containing `lpi` in their name from the archive `lpifiles.tar.gz`?

* [x] `tar xvzf lpifiles.tar.gz --wildcards '*lpi*'`
* [ ] `tar xvzwf lpifiles.tar.gz '*lpi*'`
* [ ] `tar -xvfz lpifiles.tar.gz --deep '*lpi*'`
* [ ] `tar -xvzf lpifiles.tar.gz --subdirs '*lpi*'`
* [ ] `tar xvzf lpifiles.tar.gz --globbing '*lpi*'`

**32.** Which of the following commands erases the contents of the `/dev/sdb3` partition?

* [ ] `rm /dev/sdb3`
* [x] `dd if=/dev/zero of=/dev/sdb3`
* [ ] `dd of=/dev/zero if=/dev/sdb3`
* [ ] `umount /dev/sdb3`

**33.** Which of the following files will be looked for and used by GNU make, if one of them exists, unless a different file is specified on the command line when trying to compile software from source code? (Choose two.)

* [ ] `configure`
* [ ] `config.h.in`
* [x] `makefile`
* [x] `Makefile`
* [ ] `Makefile.in`
