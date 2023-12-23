# 200: Capacity Planning

## 200.1 Measure and Troubleshoot Resource Usage (weight: 6)

{% hint style="success" %}
Candidates should be able to measure hardware resource and network bandwidth, identify and troubleshoot resource problems.



**Key Knowledge Areas:**

* Measure CPU usage.
* Measure memory usage.
* Measure disk I/O.
* Measure network I/O.
* Measure firewalling and routing throughput.
* Map client bandwidth usage.
* Match / correlate system symptoms with likely problems.
* Estimate throughput and identify bottlenecks in a system including networking.

**The following is a partial list of the used files, terms and utilities:**

* iostat
* iotop
* vmstat
* netstat
* ss
* iptraf
* pstree, ps
* w
* lsof
* top
* htop
* uptime
* sar
* swap
* processes blocked on I/O
* blocks in
* blocks out
{% endhint %}

### Measuring System Resource Usage

* `iostat`: Report on CPU statistics and input/output statistics for devices and partitions. Provided by the `sysstat` package. By default, the command generates two reports: CPU and device utilization.

```bash
iostat [OPTIONS] [DEVICE] [INTERVAL [COUNT]]
```

| Option | Purpose                                   |
| ------ | ----------------------------------------- |
| `-c`   | Display CPU utilization                   |
| `-d`   | Display device utilization report         |
| `-h`   | Display in human readable format          |
| `-m`   | Display stats in megabytes per second     |
| `-N`   | Display the registered device mapper name |
| `-t`   | Print the time for each report            |
| `-x`   | Display extended statistics               |
| `-y`   | Omit the first report                     |

{% code overflow="wrap" %}
```bash
[root@linuxmaster ~]# iostat
Linux 3.10.0-1160.76.1.el7.x86_64 (linuxmaster) 	09/15/2022 	_x86_64_(1 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           9.47    0.00    7.00    0.98    0.00   82.55

Device:            tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn
sda              60.79      1802.02     23505.63     210728    2748748

[root@linuxmaster ~]# iostat -c
Linux 3.10.0-1160.76.1.el7.x86_64 (linuxmaster) 	09/15/2022 	_x86_64_(1 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           2.92    0.00    2.29    0.30    0.00   94.49

[root@linuxmaster ~]# iostat -c -y 1 4
Linux 3.10.0-1160.76.1.el7.x86_64 (linuxmaster) 	09/15/2022 	_x86_64_(1 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           1.00    0.00    0.00    0.00    0.00   99.00

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.00    0.00    0.00    0.00    0.00  100.00

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.00    0.00    0.00    0.00    0.00  100.00

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.00    0.00    0.00    0.00    0.00  100.00
```
{% endcode %}

* `sar`: Collect, report, or save system activity information. Provided by the sysstat command. sar creates a binary file for each day of the month with all the information collected (e.g., sa01), as well as a daily summary (e.g.,sar01). These files are overwritten the following month. The sysstat service must be started for sar to start collecting data and the duration of collection can be changed by modifying `/etc/cron.d/sysstat`.

```bash
sar [OPTIONS] [INTERVAL [COUNT ]]
```

| Option | Purpose                                      |
| ------ | -------------------------------------------- |
| `-B`   | Report paging statistics                     |
| `-b`   | Report I/O and transfer rate statistics      |
| `-d`   | Report activity for each block device        |
| `-f`   | Extract records from a filename              |
| `-n`   | Report network statistics based on a keyword |
| `-o`   | Save statistics to a binary file             |
| `-r`   | Report memory utilization statistics         |
| `-u`   | Report CPU utilization                       |

{% code overflow="wrap" %}
```bash
[root@linuxmaster ~]# systemctl status sysstat
● sysstat.service - Resets System Activity Logs
   Loaded: loaded (/usr/lib/systemd/system/sysstat.service; enabled; vendor preset: enabled)
   Active: active (exited) since Thu 2022-09-15 11:34:53 UTC; 4min 16s ago
  Process: 1524 ExecStart=/usr/lib64/sa/sa1 --boot (code=exited, status=0/SUCCESS)
 Main PID: 1524 (code=exited, status=0/SUCCESS)

Sep 15 11:34:53 linuxmaster systemd[1]: Starting Resets System Activity Logs...
Sep 15 11:34:53 linuxmaster systemd[1]: Started Resets System Activity Logs.

[root@linuxmaster ~]# sar
Linux 3.10.0-1160.76.1.el7.x86_64 (linuxmaster) 	09/15/2022 	_x86_64_	(1 CPU)

11:34:53 AM       LINUX RESTART

11:40:01 AM     CPU     %user     %nice   %system   %iowait    %steal     %idle
11:50:01 AM     all      0.21      0.00      0.20      0.01      0.00     99.58
12:00:01 PM     all      0.43      0.00      0.32      0.03      0.00     99.23
12:10:01 PM     all      0.20      0.00      0.17      0.02      0.00     99.62
12:20:01 PM     all      0.37      0.00      0.24      0.02      0.00     99.37
Average:        all      0.30      0.00      0.23      0.02      0.00     99.45

[root@linuxmaster ~]# sar -y 1 5
Linux 3.10.0-1160.76.1.el7.x86_64 (linuxmaster) 	09/15/2022 	_x86_64_	(1 CPU)

11:39:27 AM       TTY   rcvin/s   xmtin/s framerr/s prtyerr/s     brk/s   ovrun/s
11:39:28 AM         0      0.00      0.00      0.00      0.00      0.00      0.00

11:39:28 AM       TTY   rcvin/s   xmtin/s framerr/s prtyerr/s     brk/s   ovrun/s
11:39:29 AM         0      0.00      0.00      0.00      0.00      0.00      0.00

11:39:29 AM       TTY   rcvin/s   xmtin/s framerr/s prtyerr/s     brk/s   ovrun/s
11:39:30 AM         0      0.00      0.00      0.00      0.00      0.00      0.00

11:39:30 AM       TTY   rcvin/s   xmtin/s framerr/s prtyerr/s     brk/s   ovrun/s
11:39:31 AM         0      0.00      0.00      0.00      0.00      0.00      0.00

11:39:31 AM       TTY   rcvin/s   xmtin/s framerr/s prtyerr/s     brk/s   ovrun/s
11:39:32 AM         0      0.00      0.00      0.00      0.00      0.00      0.00

Average:          TTY   rcvin/s   xmtin/s framerr/s prtyerr/s     brk/s   ovrun/s
Average:            0      0.00      0.00      0.00      0.00      0.00      0.00

[root@linuxmaster ~]# sar -d
Linux 3.10.0-1160.76.1.el7.x86_64 (linuxmaster) 	09/15/2022 	_x86_64_	(1 CPU)

11:34:53 AM       LINUX RESTART

11:40:01 AM       DEV       tps  rd_sec/s  wr_sec/s  avgrq-sz  avgqu-sz     await     svctm     %util
11:50:01 AM    dev8-0      0.61      0.08     10.49     17.36      0.00      0.57      0.28      0.02
12:00:01 PM    dev8-0      1.01      2.30     24.28     26.24      0.00      0.85      0.30      0.03
12:10:01 PM    dev8-0      0.59      0.29     10.25     17.95      0.00      0.44      0.31      0.02
12:20:01 PM    dev8-0      1.04      1.41     21.38     21.97      0.00      2.10      0.30      0.03
Average:       dev8-0      0.81      1.02     16.60     21.71      0.00      1.12      0.30      0.02
```
{% endcode %}

```bash
[root@linuxmaster sa]# cat /etc/cron.d/sysstat 
# Run system activity accounting tool every 10 minutes
*/10 * * * * root /usr/lib64/sa/sa1 1 1
# 0 * * * * root /usr/lib64/sa/sa1 600 6 &
# Generate a daily summary of process accounting at 23:53
53 23 * * * root /usr/lib64/sa/sa2 -A
```

* `free`: Display the amount of free and used memory in the system (default is Kb). The information displayed by free is pulled from the /proc/meminfo file. Swap is a substitute for physical memory that uses the hard disk. The /proc/sys/vm/swappiness file determines how aggressively a system will swap.

```bash
free [OPTIONS]
```

| Option | Purpose                                         |
| ------ | ----------------------------------------------- |
| `-m`   | Display memory in megabytes                     |
| `-g`   | Display memory in gigabytes                     |
| `-h`   | Display output in human-readable format         |
| `-w`   | Switch to wide mode                             |
| `-s`   | Continually display output in seconds specified |
| `-c`   | Set a count for the -s option                   |

```bash
[root@linuxmaster ~]# free
              total        used        free      shared  buff/cache   available
Mem:        1832812       96900     1379484        8684      356428     1585340
Swap:             0           0           0

[root@linuxmaster ~]# free -h
              total        used        free      shared  buff/cache   available
Mem:           1.7G         94M        1.3G        8.5M        348M        1.5G
Swap:            0B          0B          0B

[root@linuxmaster sa]# free -h -s 1 -c 3
              total        used        free      shared  buff/cache   available
Mem:           1.7G         96M        1.3G        8.5M        348M        1.5G
Swap:            0B          0B          0B

              total        used        free      shared  buff/cache   available
Mem:           1.7G         96M        1.3G        8.5M        348M        1.5G
Swap:            0B          0B          0B

              total        used        free      shared  buff/cache   available
Mem:           1.7G         96M        1.3G        8.5M        348M        1.5G
Swap:            0B          0B          0B
```

* `vmstat`: Reports virtual memory statistics as well as statistics on paging and block I/O. By default, vmstat prints a summary of usage statistics since the last reboot. vmstat can run at an interval with a count or continuously. The first report in the interval is the summary report.

```bash
vmstat [OPTIONS] [DELAY [COUNT]]
```

| Option | Purpose                              |
| ------ | ------------------------------------ |
| `-a`   | Display active and inactive memory   |
| `-s`   | Display a table of memory statistics |
| `-d`   | Report disk statistics               |
| `-D`   | Report summary statistics on disks   |
| `-t`   | Append a timestamp to each line      |
| `-w`   | Wide output mode                     |

```bash
[root@linuxmaster ~]# vmstat
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 1379508  13376 343080    0    0   735   467  132  200  4  3 93  0  0

[root@linuxmaster ~]# vmstat 1 4
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 1378676  13728 343292    0    0   226   146   54   82  1  1 98  0  0
 0  0      0 1378676  13728 343292    0    0     0     0   17   28  0  0 100  0  0
 0  0      0 1378676  13728 343292    0    0     0     0   17   29  0  0 100  0  0
 0  0      0 1378676  13728 343292    0    0     0     0   11   21  0  0 100  0  0
 
 [root@linuxmaster ~]# vmstat -d 1 4
disk- ------------reads------------ ------------writes----------- -----IO------
       total merged sectors      ms  total merged sectors      ms    cur    sec
sr0        0      0       0       0      0      0       0       0      0      0
sda     5820      8  422073    3096   1989   2803 5547568    9038      0      2
sr0        0      0       0       0      0      0       0       0      0      0
sda     5820      8  422073    3096   1991   2806 5547608    9040      0      2
sr0        0      0       0       0      0      0       0       0      0      0
sda     5820      8  422073    3096   1991   2806 5547608    9040      0      2
sr0        0      0       0       0      0      0       0       0      0      0
sda     5820      8  422073    3096   2003   2808 5547720    9052      0      2

[root@linuxmaster ~]# vmstat -s
      1832812 K total memory
        99164 K used memory
       268444 K active memory
       110020 K inactive memory
      1376568 K free memory
        13768 K buffer memory
       343312 K swap cache
            0 K total swap
            0 K used swap
            0 K free swap
         1239 non-nice user cpu ticks
            0 nice user cpu ticks
          986 system cpu ticks
        96054 idle cpu ticks
          125 IO-wait cpu ticks
            0 IRQ cpu ticks
           13 softirq cpu ticks
            0 stolen cpu ticks
       211036 pages paged in
       136768 pages paged out
            0 pages swapped in
            0 pages swapped out
        51272 interrupts
        78430 CPU context switches
   1663241496 boot time
         1637 forks
```

### Viewing Open Files and Running Processes

* `lsof`: Lists open files on the system. By default, the command will display all open files belonging to all active processes. When multiple options are used, it will display open files that meet any of the criteria (ORing), but the -a option can be used to AND the selections (ANDing).

```bash
lsof [OPTIONS] [NAMES]
```

| Option | Purpos                                                            |
| ------ | ----------------------------------------------------------------- |
| `-a`   | Causes selections to be ANDed                                     |
| `-c`   | List files based on command entered                               |
| `-i`   | List files based on a network connection                          |
| `+d`   | List files associated with a directory                            |
| `+D`   | List files associated with a directory (includes sub directories) |
| `-p`   | List files based on PID                                           |
| `-t`   | Display terse output (PIDs only)                                  |
| `-u`   | List files based on User                                          |
| `^`    | Used to negate a selection entered                                |

{% code overflow="wrap" %}
```bash
[root@linuxmaster ~]# lsof | head
COMMAND    PID  TID    USER   FD      TYPE             DEVICE  SIZE/OFF       NODE NAME
systemd      1         root  cwd       DIR                8,1      4096          2 /
systemd      1         root  rtd       DIR                8,1      4096          2 /
systemd      1         root  txt       REG                8,1   1632960      12858 /usr/lib/systemd/systemd
systemd      1         root  mem       REG                8,1     20064       5240 /usr/lib64/libuuid.so.1.3.0
systemd      1         root  mem       REG                8,1    265576      10126 /usr/lib64/libblkid.so.1.1.0
systemd      1         root  mem       REG                8,1     90160       5211 /usr/lib64/libz.so.1.2.7
systemd      1         root  mem       REG                8,1    157440       5229 /usr/lib64/liblzma.so.5.2.2
systemd      1         root  mem       REG                8,1     23968       5585 /usr/lib64/libcap-ng.so.0.0.0
systemd      1         root  mem       REG                8,1     19896       5553 /usr/lib64/libattr.so.1.1.0

[root@linuxmaster ~]# lsof -u ^root | head
COMMAND   PID TID    USER   FD      TYPE             DEVICE SIZE/OFF  NODE NAME
polkitd   540     polkitd  cwd       DIR                8,1     4096     2 /
polkitd   540     polkitd  rtd       DIR                8,1     4096     2 /
polkitd   540     polkitd  txt       REG                8,1   120432 13264 /usr/lib/polkit-1/polkitd
polkitd   540     polkitd  mem       REG                8,1    61560  4728 /usr/lib64/libnss_files-2.17.so
polkitd   540     polkitd  mem       REG                8,1    68192  5318 /usr/lib64/libbz2.so.1.0.6
polkitd   540     polkitd  mem       REG                8,1    99944  5540 /usr/lib64/libelf-0.176.so
polkitd   540     polkitd  mem       REG                8,1    19896  5553 /usr/lib64/libattr.so.1.1.0
polkitd   540     polkitd  mem       REG                8,1    20064  5240 /usr/lib64/libuuid.so.1.3.0
polkitd   540     polkitd  mem       REG                8,1   265576 10126 /usr/lib64/libblkid.so.1.1.0

[root@linuxmaster ~]# lsof -u root -t
1
2
4
5
6
7
8
9
10
11
...

[root@linuxmaster ~]# lsof -u root -t | wc -l
70

[root@linuxmaster ~]# lsof -i
COMMAND   PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
chronyd   546 chrony    5u  IPv4  13335      0t0  UDP linuxmaster:323 
chronyd   546 chrony    6u  IPv6  13336      0t0  UDP linuxmaster:323 
dhclient  643   root    6u  IPv4  15146      0t0  UDP *:bootpc 
sshd     1118   root    3u  IPv4  16844      0t0  TCP *:ssh (LISTEN)
sshd     1118   root    4u  IPv6  16846      0t0  TCP *:ssh (LISTEN)
sshd     1145   root    3u  IPv4  16984      0t0  TCP static.120.221.201.195.clients.your-server.de:ssh->88.232.191.134.dynamic.ttnet.com.tr:58976 (ESTABLISHED)
sshd     2029   root    3u  IPv4  31059      0t0  TCP static.120.221.201.195.clients.your-server.de:ssh->157.230.183.86:52592 (ESTABLISHED)
sshd     2030   sshd    3u  IPv4  31059      0t0  TCP static.120.221.201.195.clients.your-server.de:ssh->157.230.183.86:52592 (ESTABLISHED)

[root@linuxmaster ~]# lsof -i tcp
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd    1118 root    3u  IPv4  16844      0t0  TCP *:ssh (LISTEN)
sshd    1118 root    4u  IPv6  16846      0t0  TCP *:ssh (LISTEN)
sshd    1145 root    3u  IPv4  16984      0t0  TCP static.120.221.201.195.clients.your-server.de:ssh->88.232.191.134.dynamic.ttnet.com.tr:58976 (ESTABLISHED)

[root@linuxmaster ~]# lsof -c sshd | head
COMMAND  PID USER   FD   TYPE             DEVICE SIZE/OFF  NODE NAME
sshd    1118 root  cwd    DIR                8,1     4096     2 /
sshd    1118 root  rtd    DIR                8,1     4096     2 /
sshd    1118 root  txt    REG                8,1   852888 14974 /usr/sbin/sshd
sshd    1118 root  mem    REG                8,1    61560  4728 /usr/lib64/libnss_files-2.17.so
sshd    1118 root  mem    REG                8,1    68192  5318 /usr/lib64/libbz2.so.1.0.6
sshd    1118 root  mem    REG                8,1    99944  5540 /usr/lib64/libelf-0.176.so
sshd    1118 root  mem    REG                8,1    19896  5553 /usr/lib64/libattr.so.1.1.0
sshd    1118 root  mem    REG                8,1    15688  6404 /usr/lib64/libkeyutils.so.1.5
sshd    1118 root  mem    REG                8,1    67104  9009 /usr/lib64/libkrb5support.so.0.1

[root@linuxmaster ~]# lsof +d /var/log
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF  NODE NAME
rsyslogd 1122 root    6w   REG    8,1    73546 17793 /var/log/messages
rsyslogd 1122 root    7w   REG    8,1   111655 17794 /var/log/secure
rsyslogd 1122 root    8w   REG    8,1     1305  6991 /var/log/cron

[root@linuxmaster ~]# lsof +D /var/log
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF  NODE NAME
auditd    514 root    5w   REG    8,1   708911  6714 /var/log/audit/audit.log
tuned     855 root    3w   REG    8,1     1527  6992 /var/log/tuned/tuned.log
rsyslogd 1122 root    6w   REG    8,1    73546 17793 /var/log/messages
rsyslogd 1122 root    7w   REG    8,1   111655 17794 /var/log/secure
rsyslogd 1122 root    8w   REG    8,1     1305  6991 /var/log/cron

[root@linuxmaster ~]# lsof -a -c rsyslogd +d /var/log
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF  NODE NAME
rsyslogd 1122 root    6w   REG    8,1    73546 17793 /var/log/messages
rsyslogd 1122 root    7w   REG    8,1   113779 17794 /var/log/secure
rsyslogd 1122 root    8w   REG    8,1     1305  6991 /var/log/cron

[root@linuxmaster ~]# lsof -c rsyslogd +d /var/log | head
COMMAND   PID USER   FD      TYPE             DEVICE SIZE/OFF  NODE NAME
rsyslogd 1122 root  cwd       DIR                8,1     4096     2 /
rsyslogd 1122 root  rtd       DIR                8,1     4096     2 /
rsyslogd 1122 root  txt       REG                8,1   664008 17789 /usr/sbin/rsyslogd
rsyslogd 1122 root  mem       REG               0,20  8388608  8320 /run/log/journal/ffc3a14c22e24c419f4abca29738ab08/system.journal
rsyslogd 1122 root  mem       REG                8,1    68192  5318 /usr/lib64/libbz2.so.1.0.6
rsyslogd 1122 root  mem       REG                8,1    99944  5540 /usr/lib64/libelf-0.176.so
rsyslogd 1122 root  mem       REG                8,1   402384  5189 /usr/lib64/libpcre.so.1.2.0
rsyslogd 1122 root  mem       REG                8,1    19896  5553 /usr/lib64/libattr.so.1.1.0
rsyslogd 1122 root  mem       REG                8,1   338672 12539 /usr/lib64/libdw-0.176.so
```
{% endcode %}

* `ps`: Report a snapshot of the current processes. By default, ps selects all process with the same effective user ID as the current user and the associated terminal.

```bash
ps [OPTIONS]
```

`ps` accept several different kinds of options:

* UNIX - May be grouped and must be preceded by a dash.
* BSD - May be grouped but must not use a dash.
* GNU long - Preceded by two dashes.

| Option | Purpose                        |
| ------ | ------------------------------ |
| `-e`   | Select all processes           |
| `-C`   | Select by command name         |
| `-p`   | Select by PID                  |
| `-t`   | Select by tty                  |
| `-u`   | Select by effective UID        |
| `-f`   | Display in full-format listing |

{% code overflow="wrap" %}
```bash
[root@linuxmaster ~]# ps
  PID TTY          TIME CMD
 1332 pts/0    00:00:00 bash
 1921 pts/0    00:00:00 ps
 
[root@linuxmaster ~]# ps -ef | head
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 11:31 ?        00:00:02 /usr/lib/systemd/systemd --switched-root --system --deserialize 21
root         2     0  0 11:31 ?        00:00:00 [kthreadd]
root         4     2  0 11:31 ?        00:00:00 [kworker/0:0H]
root         5     2  0 11:31 ?        00:00:00 [kworker/u2:0]
root         6     2  0 11:31 ?        00:00:00 [ksoftirqd/0]
root         7     2  0 11:31 ?        00:00:00 [migration/0]
root         8     2  0 11:31 ?        00:00:00 [rcu_bh]
root         9     2  0 11:31 ?        00:00:00 [rcu_sched]
root        10     2  0 11:31 ?        00:00:00 [lru-add-drain]

[root@linuxmaster ~]# ps -fC sshd
UID        PID  PPID  C STIME TTY          TIME CMD
root      1118     1  0 11:31 ?        00:00:00 /usr/sbin/sshd -D
root      1145  1118  0 11:31 ?        00:00:00 sshd: root@pts/0
root      2065  1118  0 12:28 ?        00:00:00 sshd: [accepted]
root      2087  1118  0 12:29 ?        00:00:00 sshd: unknown [priv]
sshd      2088  2087  0 12:29 ?        00:00:00 sshd: unknown [net]

[root@linuxmaster ~]# ps -fp 1
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 11:31 ?        00:00:02 /usr/lib/systemd/systemd --switched-root --system -
```
{% endcode %}

* `pstree`: Display running processes as a visual tree.

```bash
pstree [OPTIONS]
```

| Option | Purpose                               |
| ------ | ------------------------------------- |
| `-A`   | Use ASCII characters to draw the tree |
| `-G`   | Use VT100 line drawing characters     |
| `-a`   | Show command line arguments           |
| `-p`   | Show PIDs                             |
| `-n`   | Sort by PID instead of name           |
| `-Z`   | Show SELinux security contex          |

{% code overflow="wrap" %}
```bash
[root@linuxmaster ~]# pstree
systemd─┬─NetworkManager─┬─dhclient
        │                └─2*[{NetworkManager}]
        ├─2*[agetty]
        ├─anacron
        ├─auditd───{auditd}
        ├─chronyd
        ├─crond
        ├─dbus-daemon───{dbus-daemon}
        ├─lvmetad
        ├─polkitd───6*[{polkitd}]
        ├─qemu-ga
        ├─rsyslogd───2*[{rsyslogd}]
        ├─sshd───sshd───bash───pstree
        ├─systemd-journal
        ├─systemd-logind
        ├─systemd-udevd
        └─tuned───4*[{tuned}]

[root@linuxmaster ~]# pstree -p
systemd(1)─┬─NetworkManager(627)─┬─dhclient(643)
           │                     ├─{NetworkManager}(628)
           │                     └─{NetworkManager}(630)
           ├─agetty(1126)
           ├─agetty(1127)
           ├─anacron(1809)
           ├─auditd(514)───{auditd}(515)
           ├─chronyd(546)
           ├─crond(1125)
           ├─dbus-daemon(543)───{dbus-daemon}(549)
           ├─lvmetad(401)
           ├─polkitd(540)─┬─{polkitd}(548)
           │              ├─{polkitd}(550)
           │              ├─{polkitd}(553)
           │              ├─{polkitd}(554)
           │              ├─{polkitd}(555)
           │              └─{polkitd}(558)
           ├─qemu-ga(538)
           ├─rsyslogd(1122)─┬─{rsyslogd}(1132)
           │                └─{rsyslogd}(1136)
           ├─sshd(1118)───sshd(1145)───bash(1332)───pstree(10018)
           ├─systemd-journal(381)
           ├─systemd-logind(551)
           ├─systemd-udevd(417)
           └─tuned(855)─┬─{tuned}(1024)
                        ├─{tuned}(1025)
                        ├─{tuned}(1030)
                        └─{tuned}(1033)

[root@linuxmaster ~]# pstree -pa | head
systemd,1 --switched-root --system --deserialize 21
  |-NetworkManager,627 --no-daemon
  |   |-dhclient,643 -d -q -sf /usr/libexec/nm-dhcp-helper -pf /var/run/dhclient-eth0.pid -lf...
  |   |-{NetworkManager},628
  |   `-{NetworkManager},630
  |-agetty,1126 --keep-baud 115200,38400,9600 ttyS0 vt220
  |-agetty,1127 --noclear tty1 linux
  |-anacron,1809 -s
  |-auditd,514
  |   `-{auditd},515
```
{% endcode %}

* `top`: Display a dynamic, real-time view of processes running on the system. The program provides a limited interactive interface for process mainpulation, as well as a much more extensive interface for personal configuration.

```bash
top [OPTIONS]
```

| Option | Purpose                           |
| ------ | --------------------------------- |
| `d`    | Change output delay               |
| `h`    | Show list of options (help)       |
| `H`    | Show thread count                 |
| `m`    | Display visual output of memory   |
| `f`    | Open field management window      |
| `R`    | Sorf from least to most           |
| `W`    | Write configuration to a file     |
| `n`    | Set number of processes displayed |
| `k`    | Kill a process                    |

{% code overflow="wrap" %}
```bash
[root@linuxmaster ~]# top
top - 12:19:34 up 47 min,  1 user,  load average: 0.00, 0.01, 0.05
Tasks:  71 total,   1 running,  70 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1832812 total,  1370016 free,    98232 used,   364564 buff/cache
KiB Swap:        0 total,        0 free,        0 used.  1582868 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND                         
 1950 root      20   0  161968   2144   1548 R  0.3  0.1   0:00.02 top                             
    1 root      20   0  128128   6776   4220 S  0.0  0.4   0:02.60 systemd                         
    2 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kthreadd                        
    4 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kworker/0:0H                    
    5 root      20   0       0      0      0 S  0.0  0.0   0:00.06 kworker/u2:0                    
    6 root      20   0       0      0      0 S  0.0  0.0   0:00.07 ksoftirqd/0                     
    7 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 migration/0 
    ...
```
{% endcode %}

```bash
# load average: 0.47, 24.71, 35.31
# 1, 5 and 15 minutes
```

### Measuring Network Usage and System Availability

* `netstat`: Print information on network connections, routing tables, interface statistics, masquerade connections, and multicast memberships. By default, netstat displays a list of open sockets. The netstat command is obsolete and has been replaced by ss. other replacements include: `ip route` for `netstat -r`, `ip -s link` for `netstat -i`, and `ip maddr` for `netstat -g.`

```bash
netstat [OPTIONS] [DELAY]
```

| Option  | Purpose                                          |
| ------- | ------------------------------------------------ |
| `-r`    | Display kernel routing table                     |
| `-g`    | Display multicast group information              |
| `-i`    | Display a table for network interfaces           |
| `-s`    | Display summary statistics for each protocol     |
| `-n`    | Show numerical address (do not resolve hostname) |
| `-c`    | Display output continuously                      |
| `-p`    | Show PID and process name                        |
| `-l`    | Show only listening sockets                      |
| `-t`    | Display TCP connections                          |
| `-u`    | Display UDP connections                          |
| `delay` | Set DELAY in seconds                             |

{% code overflow="wrap" %}
```bash
[root@linuxmaster ~]# netstat | head
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp       53      0 static.120.221.201.:ssh 157.230.183.86:47510    CLOSE_WAIT 
tcp        0     36 static.120.221.201.:ssh 88.232.191.134.dy:58976 ESTABLISHED
Active UNIX domain sockets (w/o servers)
Proto RefCnt Flags       Type       State         I-Node   Path
unix  3      [ ]         DGRAM                    8213     /run/systemd/notify
unix  2      [ ]         DGRAM                    8215     /run/systemd/cgroups-agent
unix  2      [ ]         DGRAM                    13337    /var/run/chrony/chronyd.sock
unix  5      [ ]         DGRAM                    8235     /run/systemd/journal/socket

[root@linuxmaster ~]# netstat -n | head
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 195.201.221.120:22      61.177.172.91:38436     ESTABLISHED
tcp        0     36 195.201.221.120:22      88.232.191.134:58976    ESTABLISHED
Active UNIX domain sockets (w/o servers)
Proto RefCnt Flags       Type       State         I-Node   Path
unix  3      [ ]         DGRAM                    8213     /run/systemd/notify
unix  2      [ ]         DGRAM                    8215     /run/systemd/cgroups-agent
unix  2      [ ]         DGRAM                    13337    /var/run/chrony/chronyd.sock
unix  5      [ ]         DGRAM                    8235     /run/systemd/journal/socket

[root@linuxmaster ~]# netstat -r
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
default         gateway         0.0.0.0         UG        0 0          0 eth0
gateway         0.0.0.0         255.255.255.255 UH        0 0          0 eth0
static.120.221. 0.0.0.0         255.255.255.255 UH        0 0          0 eth0

[root@linuxmaster ~]# netstat -nl | head 
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp6       0      0 :::22                   :::*                    LISTEN     
udp        0      0 0.0.0.0:68              0.0.0.0:*                          
udp        0      0 127.0.0.1:323           0.0.0.0:*                          
udp6       0      0 ::1:323                 :::*                               
Active UNIX domain sockets (only servers)
Proto RefCnt Flags       Type       State         I-Node   Path
unix  2      [ ACC ]     SEQPACKET  LISTENING     11043    /run/udev/control

[root@linuxmaster ~]# netstat -nlp | head 
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1118/sshd           
tcp6       0      0 :::22                   :::*                    LISTEN      1118/sshd           
udp        0      0 0.0.0.0:68              0.0.0.0:*                           643/dhclient        
udp        0      0 127.0.0.1:323           0.0.0.0:*                           546/chronyd         
udp6       0      0 ::1:323                 :::*                                546/chronyd         
Active UNIX domain sockets (only servers)
Proto RefCnt Flags       Type       State         I-Node   PID/Program name     Path
unix  2      [ ACC ]     SEQPACKET  LISTENING     11043    1/systemd            /run/udev/control

root@linuxmaster ~]# netstat -tlnp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1118/sshd           
tcp6       0      0 :::22                   :::*                    LISTEN      1118/sshd 

[root@linuxmaster ~]# netstat -ulnp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
udp        0      0 0.0.0.0:68              0.0.0.0:*                           643/dhclient        
udp        0      0 127.0.0.1:323           0.0.0.0:*                           546/chronyd         
udp6       0      0 ::1:323                 :::*                                546/chronyd         

[root@linuxmaster ~]# netstat -tulnp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1118/sshd           
tcp6       0      0 :::22                   :::*                    LISTEN      1118/sshd           
udp        0      0 0.0.0.0:68              0.0.0.0:*                           643/dhclient        
udp        0      0 127.0.0.1:323           0.0.0.0:*                           546/chronyd         
udp6       0      0 ::1:323                 :::*                                546/chronyd   
```
{% endcode %}

* `w`: Display information about the users currently on the machine, and their processes. The header shows the current time, how long the system has been running, how many users are currently logged on, and the system load averages for the past 1, 5, and 15 minutes. The following entries are displayed for each user: login name, the tty name, the remote host, login time, idle time, JCPU, PCPU, and the command line of their current process.

```bash
w [OPTIONS] USER
```

| Option | Purpose                                    |
| ------ | ------------------------------------------ |
| `-h`   | Don't print the header                     |
| `-s`   | Use short format                           |
| `-i`   | Display IP addresses rather than hostnames |
| `user` | Display information for USER specified     |

```bash
[root@linuxmaster ~]# w
 12:39:35 up  1:07,  1 user,  load average: 0.00, 0.01, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
root     pts/0    88.232.191.134   11:31    7.00s  0.32s  0.00s w

[root@linuxmaster ~]# w -h
root     pts/0    88.232.191.134   11:31    4.00s  0.37s  0.01s w -h

[root@linuxmaster ~]# w -s
 12:51:12 up  1:19,  1 user,  load average: 0.00, 0.01, 0.05
USER     TTY      FROM              IDLE WHAT
root     pts/0    88.232.191.134    0.00s w -s
```

* `uptime`: Display how long the system has been running. By default, displays the same information found in the header of the `w` command. Shows the current time, how long the system has been running, how many users are currently logged on, and the system load averages for the past 1, 5, and 15 minutes. Both `w` and `uptime` get their information from `/var/run/utmp`.

```bash
uptime [OPTIONS]
```

| Option | Purpose                                      |
| ------ | -------------------------------------------- |
| `-p`   | Show uptime in pretty format                 |
| `-h`   | Display help text                            |
| `-s`   | Display uptime in yyyy-mm-dd HH:MM:SS format |
| `-V`   | Display version information                  |

```bash
[root@linuxmaster ~]# uptime
 12:39:56 up  1:08,  1 user,  load average: 0.00, 0.01, 0.05
 
 [root@linuxmaster ~]# uptime -p
up 1 hour, 20 minutes

[root@linuxmaster ~]# uptime -s
2022-09-15 11:31:36
```

## 200.2 Predict Future Resource Needs (weight: 2)

{% hint style="success" %}
Candidates should be able to monitor resource usage to predict future resource needs.\
\


**Key Knowledge Areas:**

* Use monitoring and measurement tools to monitor IT infrastructure usage.
* Predict capacity break point of a configuration.
* Observe growth rate of capacity usage.
* Graph the trend of capacity usage.
* Awareness of monitoring solutions such as Icinga2, Nagios, collectd, MRTG and Cacti

**The following is a partial list of the used files, terms and utilities:**

* diagnose
* predict growth
* resource exhaustion
{% endhint %}

### Monitoring Solutions

#### Nagios

Nagios is an industry-standard IT monitoring solution.

* Provides complete monitoring for Linux operating systems and distributions
* Provides two options for monitoring hosts
  * Using dedicated Nagios agents
  * Using SNMP for "agentless" monitoring
* Provides advanced graphs and visual aids
* Provides automated and integrated capacity planning

#### Icinga2

Icinga2 is a monitoring solution that reports on various usage statistics by using a primary node, satellites, and agents.

* Monitors network resources
* Notifies users of outages
* Generates performance data
* Runs on Linux/Unix and Windows

#### MRTG

The Multi Router Traffic Grapher is a tool for monitoring routers and other network devices.

* Monitors SNMP network devices
* Monitors traffic load on network links
* Generates HTML pages containing PNG images of live data
* Implements a data consolidation algorithm
* Provides built-in hooks for using RRDTool

#### Cacti

A network graphing solution designed to work with RRDTool.

* Provides a complete frontend to RRDTool
* Uses a MySQL database to store data
* Provides a built-in user-based management system
* Provides graphs for displaying data
* Allows data sources to be defined for data collection

#### Collectd

Collectd is a service daemon that collects system and applicatkin performance metrics periodically and provides mechanisms to store the values in a variety of ways (e.g. RRD files).

* Gathers metrics from various sources
* Stores metrics gathered or makes them available over the network
* Provides information for performance analysis and capacity planning
* Provides over 100 plugins for monitoring and integration with other solutions

Installing and Configuration Collectd

{% code overflow="wrap" %}
```bash
root@linuxmaster:~# apt install collectd apache2 librrd-perl libconfig-general-perl libhtml-parser-perl libregexp-common-perl libcgi-session-perl

root@linuxmaster:~# systemctl restart collectd

root@linuxmaster:~# cp -r /usr/share/doc/collectd/examples/collection3/ /var/www/html

root@linuxmaster:~# vim /etc/apache2/apache2.conf

## Add the following:
<Directory /var/www/html/collection3/>
AllowOverride None
Options +ExectCGI
AddHandler cgi-script .cgi .pl
Require all granted
</Directory>

root@linuxmaster:~# ln -s /etc/apache2/mod-available/cgi.load /etc/apache2/mods-enabled/

root@linuxmaster:~# systemctl restart apache2
```
{% endcode %}

## Commands and Notes

* `iostat`: Report on CPU statistics and input/output statistics for devices and partitions.
* `sar`: Collect, report, or save system activity information.
* sar creates a binary file for each day of the month with all the information collected (e.g., `sa01`), as well as a daily summary (e.g.,`sar01`). These files are overwritten the following month. The sysstat service must be started for sar to start collecting data and the duration of collection can be changed by modifying `/etc/cron.d/sysstat`.
* `free`: Display the amount of free and used memory in the system.
* `vmstat`: Reports virtual memory statistics as well as statistics on paging and block I/O.
* `lsof`: Lists open files on the system.
* `ps`: Report a snapshot of the current processes.
* `pstree`: Display running processes as a visual tree.
* `top`: Display a dynamic, real-time view of processes running on the system.
* `netstat`: Print information on network connections, routing tables, interface statistics, masquerade connections, and multicast memberships.
* `w`: Display information about the users currently on the machine, and their processes.
* `uptime`: Display how long the system has been running.
* **Nagios** using SNMP for "agentless" monitoring.
* **Icinga2** originally created as a fork of Nagios.
* **MRTG** provides built-in hooks for using RRDTool.
* **Cacti** a network graphing solution designed to work with RRDTool.
* **Collectd** is a service daemon that collects system and applicatkin performance metrics periodically and provides mechanisms to store the values in a variety of ways (e.g. RRD files).

## Sample Questions

**1.** Which of the following provides a complete frontend for RRDTool?

* [ ] MRTG
* [x] Cacti
* [ ] Collectd
* [ ] Nagios

**2.** What command displays a dynamic view of processes running on the system?

* [ ] lsof
* [x] top
* [ ] pstree
* [ ] ps

**3.** Which of the following was originally created as a fork of Nagios?

* [ ] Collectd
* [ ] MRTG
* [ ] Cacti
* [x] Icinga2

**4.** What file is used to change the duration of collection for sar?

* [ ] /var/log/sa
* [ ] /etc/sar.conf
* [x] /etc/cron.d/sysstat
* [ ] /etc/cron.d/sar

**5.** What option for lsof lists open files based on network connections?

* [ ] \-c
* [ ] \-u
* [x] \-i
* [ ] \-p

**6.** What commands show how long the system has been running?

* [x] w
* [ ] date
* [x] uptime
* [ ] stat

**7.** What option for netstat displays the kernel routing table?

* [ ] \-l
* [ ] \-n
* [ ] \-i
* [x] \-r

**8.** Which of the following are aspects of capacity planning?

* [x] Collecting long term resource usage data
* [x] Observing trends in resource usage
* [ ] Implementing competitive salaries based on what the market will bear
* [x] Consdiering business Initiatives

**9.** What option only generates the device report with the iostat command?

* [ ] \-c
* [ ] \-y
* [ ] \-x
* [x] \-d

**10.** What kinds of options are accepted by the `ps` command? (choose all that apply)

* [ ] LSB
* [x] UNIX
* [x] GNU long
* [x] BSD

**11.** Which command reports information on memory usage, paging and block input/output?

* [ ] free
* [ ] memshow
* [ ] lsof
* [ ] pidof
* [x] vmstat

**12.** Which program lists information about files and network connections opened by processes? (Specify ONLY the command without any path or parameters.)

**Answer:** lsof

**13.** Which of the following commands by default provides the PIDs of the processes sorted by which are using the most CPU cycles on the Linux system?

* [x] top
* [ ] uptime
* [ ] ps aux
* [ ] vmstat
* [ ] freemem

**14.** In the following output of the command uptime, the load averages represent the system load averages for what time frames?

`12:10:05 up 18 days, 19:00, 2 users, load average: 0.47, 24.71, 35.31`

* [x] 1, 5 and 15 minutes
* [ ] 1, 15 and 30 minutes
* [ ] 5, 20 and 25 seconds
* [ ] 15, 30 and 60 minutes
* [ ] 15, 45 and 90 seconds

**15.** In order to gather performance data with `sar` over a longer period of time, which command should be run periodically from cron?

* [x] sa1
* [ ] sarmon
* [ ] sarec
* [ ] sadf
* [ ] sarcron

**16.** In the output of `sar –b`, what does tps stand for?

* [ ] Terrabyte per second
* [ ] Traffic per second
* [x] Transfers per second
* [ ] Total data per second
* [ ] Terrabit per second

**17.** Which of the following monitoring commands allow you to view system memory information? (Choose all that apply.)

* [x] free
* [ ] mpstat
* [x] top
* [ ] mtr
* [x] vmstat

**18.** Which of the following will show CPU usage information 20 times in 2-second intervals?

* [ ] uptime 20 2
* [ ] uptime 2 20
* [ ] sar 20 2
* [x] sar 2 20
* [ ] None of the above

**19.** Which of the following are considered to be system resource-monitoring solutions? (Choose all that apply)

* [x] collectd
* [ ] Duplicity
* [x] MRTG
* [x] Nagios
* [ ] All of the above

**20.** Which of the following are true about Linux system memory? (Choose all that apply)

* [ ] Memory is divided into sheets.
* [x] Memory for an idle process can be swapped out to swap space.
* [x] Swap can also be called virtual memory.
* [ ] Swapping is the memory management scheme, where idle processes swap memory.
* [ ] Memory-swapping statistics can be viewed using the psmap utility.

**Answer:** B, C. Memory for idle processes can be swapped out from RAM to a special partition called swap space, swap, or virtual memory. Therefore, options B and C are correct. Memory is divided up into chunks called pages (option A), not sheets. Swapping is the memory management technique where idle processes’ memory is swapped out to swap. The processes do not trade memory (option D). Finally, the psmap tool is primarily for mapping processes (option E). The best tools for viewing memory statistics are tools like `free`, `sar`, and `vmstat`.

**21.** In the following output from top, which processes contribute to the percentage of time that the CPU spends in the state of wa?
```bash
Tasks: 193 total, 1 running, 190 sleeping, 2 stopped, 0 zombie
Cpu(s): 0.5%us, 0.3%sy, 0.0%ni, 98.2%id, 1.0%wa, 0.0%hi, 0.0%si, 0.0%st
```

* [ ] Processes waiting for user interaction.
* [ ] Processes that were already closed and are waiting to be launched again.
* [ ] Processes that have not been scheduled yet because they haven't been fully loaded into RAM or are in swap.
* [x] Processes waiting for IO operations to complete.

