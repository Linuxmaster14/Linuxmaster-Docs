# 205: Networking Configuration

## **205.1 Basic networking configuration (weight: 3)**

{% hint style="success" %}
Candidates should be able to configure a network device to be able to connect to a local, wired or wireless, and a wide-area network. This objective includes being able to communicate between various subnets within a single network including both IPv4 and IPv6 networks.



**Key Knowledge Areas:**

* Utilities to configure and manipulate ethernet network interfaces
* Configuring basic access to wireless networks

**The following is a partial list of the used files, terms and utilities:**

* ip
* ifconfig
* route
* arp
* iw
* iwconfig
* iwlist
{% endhint %}

### Managing Network Interfaces

* `ifconfig`: configure a network interface

<table><thead><tr><th width="413">Command</th><th>Purpose</th></tr></thead><tbody><tr><td><code>ifconfig</code></td><td>Display information for active network interfaces</td></tr><tr><td><code>ifconfig -a</code></td><td>Display information for all network interfaces</td></tr><tr><td><code>ifconfig eth0</code></td><td>Display information for a specific network interface</td></tr><tr><td><code>ifconfig eth0 up</code></td><td>Bring a device online</td></tr><tr><td><code>ifconfig eth0 down</code></td><td>Bring a device offline</td></tr><tr><td><code>ifconfig eth0 192.168.1.200</code></td><td>Assign an IP address to a network interface</td></tr><tr><td><code>ifconfig eth0 netmask 255.255.255.0</code></td><td>Assign a netmask to a network interface</td></tr><tr><td><code>ifconfig eth0 broadcast 192.168.1.255</code></td><td>Assign a broadcast address to a network interface</td></tr><tr><td><code>ifconfig eth0 192.168.1.200</code></td><td>Remove an IP address from a network interface</td></tr><tr><td><code>ifconfig eth0 mtu </code><em><code>number</code></em></td><td>Set maximum transmission unit for a network interface</td></tr><tr><td><code>ifconfig eth0 promisc</code></td><td>Set a network interface to promiscuous mode</td></tr></tbody></table>

* `ip`: Show/manipulate routing, devices, policy routing, and tunnels.

```bash
ip [ OPTIONS ] OBJECT { COMMAND | help }
```

OBJECT:

* `link`
* `addr`
* `addrlabel`
* `route`
* `rule`
* `neigh`
* `tunnel`
* `maddr`
* `mroute`
* `monitor`

<table><thead><tr><th width="414">Command</th><th>Purpose</th></tr></thead><tbody><tr><td><code>ip help</code></td><td>Display a list of commands and options for the <code>ip</code> command</td></tr><tr><td><code>ip addr help</code></td><td>Display a list of commands and options for the address subcommand</td></tr><tr><td><code>ip link help</code></td><td>Display a list of commands and options for the link subcommand</td></tr><tr><td><code>ip addr</code></td><td>Show information for all address</td></tr><tr><td><code>ip addr show dev eth0</code></td><td>Show information for a specific device</td></tr><tr><td><code>ip addr add 192.168.1.200/24 dev eth0</code></td><td>Add a address to device</td></tr><tr><td><code>ip addr del 192.168.1.200/24 dev eth0</code></td><td>Remove an address from a device</td></tr><tr><td><code>ip addr add 192.168.1.200/24 broadcast 192.168.1.255 dev eth0</code></td><td>Add an IP address specific broadcast address to a device</td></tr><tr><td><code>ip link</code></td><td>Show information for all interfaces</td></tr><tr><td><code>ip link show dev eth0</code></td><td>Show information for a single device</td></tr><tr><td><code>ip -s link</code></td><td>Show interface statistics</td></tr><tr><td><code>ip link set</code></td><td>Alter the status of an interface</td></tr><tr><td><code>ip link set mtu </code><em><code>number</code></em></td><td>Set maximum transmission unit for a network interface</td></tr><tr><td><code>ip link set eth0 promisc on</code></td><td>Set a network interface to promiscuous mode</td></tr><tr><td><code>ip link set eth0 up</code></td><td>Bring a device online</td></tr><tr><td><code>ip link set eth0 down</code></td><td>Bring a device offline</td></tr></tbody></table>

* `iwconfig`: configure a wireless network interface
* `iwlist`: Get more detailed wireless information from a wireless interface

<table><thead><tr><th width="391">Command</th><th>Purpose</th></tr></thead><tbody><tr><td><code>iwconfig</code></td><td>Display information about all available wireless interfaces</td></tr><tr><td><code>iwconfig wlan0</code></td><td>Display information about a wireless interface</td></tr><tr><td><code>iwconfig --help</code></td><td>Display a list of commands and options</td></tr><tr><td><code>iwconfig wlan0 essid "MyNetwork" key my_key</code></td><td>Connect to a wireless network by providing a key</td></tr><tr><td><code>iwconfig wlan0 rate 24M</code></td><td>Set the bitrate for an interface</td></tr><tr><td><code>iwlist wlan0 scan</code></td><td>Scan for available wireless networks</td></tr><tr><td><code>iwlist wlan0 freq</code></td><td>List available frequencies</td></tr><tr><td><code>iwlist wlan0 rate</code></td><td>List available bit rates</td></tr></tbody></table>

* `iw`: show / manipulate wireless devices and their configuration

```bash
iw [ OPTIONS ] { help | OBJECT COMMAND }
```

| Command                   | Purpose                                          |
| ------------------------- | ------------------------------------------------ |
| `iw help`                 | Print all supported commands                     |
| `iw help command`         | Print help information for specified command     |
| `iw dev`                  | View available wireless interfaces               |
| `iw list`                 | List all wireless devices and their capabilities |
| `iw dev wlan0 link`       | Display link information                         |
| `iw dev wlan0 info`       | Show information for an interface                |
| `iw phy phy0 info`        | Show capabilities for a device                   |
| `iw event`                | Monitor event from the kernel                    |
| `iw wlan0 scan`           | Scan for available SSIDs                         |
| `iw dev wlan0 connad`     | Connect to a wireless network                    |
| `iw dev wlan0 disconnect` | Disconnect from a wireless network               |

### Discovering Network Devices

* `arp`: manipulate the system ARP cache

<table><thead><tr><th width="421">Command</th><th>Purpose</th></tr></thead><tbody><tr><td><code>arp [-avn]</code></td><td>Display the contents of the ARP cache</td></tr><tr><td><code>arp -i eth1</code></td><td>Display entries for an interface</td></tr><tr><td><code>arp -a 192.168.1.9</code></td><td>Display entries for an IP address</td></tr><tr><td><code>arp -s 192.168.1.9 -i eth2 1:2:3:4:5:6</code></td><td>Add an enrty to the ARP cache</td></tr><tr><td><code>arp -i eth1 -d 192.168.1.9</code></td><td>Remove an entry from the ARP cache</td></tr></tbody></table>

* `ip neigh:` Display the neighbor objects or the ARP cache

|                                                            |                                                    |
| ---------------------------------------------------------- | -------------------------------------------------- |
| `ip neigh`                                                 | Display neighbor objects                           |
| `ip -s neigh`                                              | Display neighbor objects in verbos with statistics |
| `ip neigh show dev eth1`                                   | Show o `arp` cache for a device                    |
| `ip neigh add 192.168.1.9 lladdr 1:2:3:4:5:6 dev eth1`     | Add an entry into the ARP table                    |
| `ip neigh del 192.168.1.9 dev eth1`                        | Invalidate an entry in the ARP table               |
| `ip neigh replace 192.168.1.9 lladdr 1:2:3:4:5:6 dev eth1` | Replace an entry or add one if not defined         |

## **205.2 Advanced Network Configuration (weight: 4)**

{% hint style="success" %}
Candidates should be able to configure a network device to implement various network authentication schemes. This objective includes configuring a multi-homed network device and resolving communication problems.



**Key Knowledge Areas:**

* Utilities to manipulate routing tables
* Utilities to configure and manipulate ethernet network interfaces
* Utilities to analyse the status of the network devices
* Utilities to monitor and analyse the TCP/IP traffic

**The following is a partial list of the used files, terms and utilities:**

* ip
* ifconfig
* route
* arp
* ss
* netstat
* lsof
* ping, ping6
* nc
* tcpdump
* nmap
{% endhint %}

### Adjusting Network Routing

* `ip route` and `route`: show / manipulate the IP routing table

| Command                                           | Purpose                                              |
| ------------------------------------------------- | ---------------------------------------------------- |
| `ip route show`                                   | Display the routing table                            |
| `ip route add 10.0.2.0/24 via 10.0.2.10 dev eth1` | Add a route                                          |
| `ip route del 10.0.2.0/24 via 10.0.2.10 dev eth1` | Remove a route                                       |
| `ip route add default via 10.0.2.10`              | Add a default gateway                                |
| `ip route add prohibit 10.0.2.10/24`              | Blockl the destinication route and send ICMP message |
| `ip route add blackhole 10.0.2.0/24`              | Block the destination route and silently discard     |

| Command                                                | Purpose                                   |
| ------------------------------------------------------ | ----------------------------------------- |
| `route (n)`                                            | Display the routing table                 |
| `route add -net 10.0.2.0/24 gw 10.0.2.10 eth1`         | Add a route                               |
| `route del -net 10.0.2.0/24 gw 10.0.2.10 eth1`         | Remove a route                            |
| `route add default gw 10.0.2.10`                       | Add a default gateway                     |
| `route add -host 10.0.2.10 reject`                     | Block the destination route for a host    |
| `route add -net 10.0.2.0 netmask 255.255.255.0 reject` | Block the destination route for a network |

### Monitoring Network Sockets

* `ss`: A utility used to investigate network sockets and dump socket statistics.

| Option               | Description                              |
| -------------------- | ---------------------------------------- |
| `-l`, `--listening`  | Display listening server sockets         |
| `-a`, `--all`        | Display all sockets (default: connected) |
| `-i`, `--interfaces` | Display interfaces table                 |
| `-s`, `--summary`    | Show socket usage summary (like SNMP)    |
| `-e`, `--extended`   | Show detailed socket information         |
| `-n`, `--numeric`    | Don't resolve names                      |
| `-p`, `--programs`   | Display PID/Program name for sockets     |
| `-t`, `--tcp`        | Display only TCP sockets                 |
| `-u`, `--udp`        | Display only UDP sockets                 |

* `netstat`: Print network connections, routing tables, interface statistics, masquerade connections, and multicast memberships.

| Option               | Description                              |
| -------------------- | ---------------------------------------- |
| `-l`, `--listening`  | Display listening server sockets         |
| `-a`, `--all`        | Display all sockets (default: connected) |
| `-i`, `--interfaces` | Display interfaces table                 |
| `-s`, `--statistics` | Show network statistics                  |
| `-e`, `--extended`   | Show detailed socket information         |
| `-v`, `--verbose`    | Be verbose                               |
| `-n`, `--numeric`    | Don't resolve names                      |
| `-p`, `--programs`   | Display PID/Program name for sockets     |
| `-t`, `--tcp`        | Display only TCP sockets                 |
| `-u`, `--udp`        | Display only UDP sockets                 |
| `-r`, `-route`       | Display routing table                    |

* `lsof`: A utility that lists open files.

<table><thead><tr><th width="394">Option</th><th>Description</th></tr></thead><tbody><tr><td><code>-u username</code></td><td>List open files by user</td></tr><tr><td><code>-u ^username</code></td><td>List open files and exclude a user</td></tr><tr><td><code>-i [46][protocol][@hostname|hostaddr][:service|port]</code></td><td>List open files by network connections</td></tr><tr><td><code>-p PID</code></td><td>List open files by PID</td></tr><tr><td><code>-p ^PID</code></td><td>List open files and exculde a PID</td></tr><tr><td><code>/directory</code></td><td>List open files by directory</td></tr><tr><td><code>/dev/sda1</code></td><td>List open files by device</td></tr><tr><td><code>-c</code></td><td>List open files by process name</td></tr></tbody></table>

### Monitoring Network Traffics

* `tcpdump`: A network traffic monitoring tool. Can monitor protocols other than TCP. Lofical operators **and** and **or** can be used to combine filters.

| Option                     | Description                                                        |
| -------------------------- | ------------------------------------------------------------------ |
| `-D`                       | List interfaces available for capture                              |
| `-i eth0`                  | Capture packets on an interface or all interfaces (any)            |
| `-c`                       | Capture a specified count of packets                               |
| `-n`                       | Disable hostname resolution                                        |
| `-nn`                      | Disable protocol, port and hostname resolution                     |
| `-i any protocol`          | Capture packets by protocol on all interfaces                      |
| `-i any host 10.0.2.10`    | Capture packets by a host on all interfaces                        |
| `-i any src/dst 10.0.2.10` | Capture packets by source or destination address on all interfaces |
| `-A`                       | View packet content in ASCII                                       |
| `-X`                       | View packet content in hex and ASCII                               |
| `-w file_name.pcap`        | Save the output of `tcpdump` to a file                             |
| `-r file_name.pcap`        | Read packets from a file                                           |

* `nmap`: Network Mapper is a network exploration and security scanner. The network mapper services file is located at `/usr/share/nmpa/nmpa-services`.

<table><thead><tr><th width="360">Option</th><th>Description</th></tr></thead><tbody><tr><td><code>hostname</code></td><td>Scan using a hostname or multiple hostnames</td></tr><tr><td><code>10.0.2.10</code></td><td>Scan using IP address or multi IP addresses</td></tr><tr><td><code>-v 10.0.2.10</code></td><td>Increase verbosity</td></tr><tr><td><code>-iL hosts.txt</code></td><td>Scan a list of hosts from a file</td></tr><tr><td><code>-A 10.0.2.10</code></td><td>Enable OS detection, version detection, script scanning, and traceroute</td></tr><tr><td><code>-O 10.0.2.10</code></td><td>Enable OS detection</td></tr><tr><td><code>-sA 10.0.2.10</code></td><td>Detect firewall or packet filters</td></tr><tr><td><code>-Pn 10.0.2.10</code></td><td>Skip host discovery (formerly -PN)</td></tr><tr><td><code>-sn 10.0.2.10</code></td><td>Perform a "ping scan" - Dot not detect open ports (formerly -sP)</td></tr><tr><td><code>-F 10.0.2.10</code></td><td>Perform fast scan using less ports</td></tr><tr><td><code>-r 10.0.2.10</code></td><td>Scan ports consecutively - don't randomize</td></tr><tr><td><code>--iflist</code></td><td>View host interface and route information</td></tr><tr><td><code>-p 22, 443 10.0.2.10</code></td><td>Specify ports to scan</td></tr><tr><td><code>-sU 58 10.0.2.10</code></td><td>Scan for a UDP port</td></tr><tr><td><code>-sV 10.0.2.10</code></td><td>Determine service/version information</td></tr><tr><td><code>-sS 10.0.2.10</code></td><td>Perform TCP SYN scan (stealthy scan)</td></tr><tr><td><code>-sT 10.0.2.10</code></td><td>Perform TCP connect scan</td></tr></tbody></table>

### Interacting with Remote Hosts

* `ping` and `ping6`: Utilities used to send ICMP ECHO\_REQUEST to network hosts. Provided by the `iputils` packages. All options can be used by `ping` and `ping6` execpt for -F (not listed), which in used to allocated a 20-bit flow label on echo request packets.

| Option            | Description                                            |
| ----------------- | ------------------------------------------------------ |
| `hostname`        | Send a stream of ICMP packets to a hostname            |
| `10.0.2.10`       | Send a stream of ICMP packets to an IP address         |
| `-c 5 10.0.2.10`  | Send a specified amount of packets                     |
| `-s 10.0.2.10`    | Alter the size of the packets                          |
| `-i 3 10.0.2.10`  | Change the interval for sending packets                |
| `-q 10.0.2.10`    | Only show the summary information                      |
| `-w 5 10.0.2.10`  | Set a timeout of when to stop sending packets          |
| `-f 10.0.2.10`    | Flood ping. Send packets as soon as possible.          |
| `-p ff 10.0.2.10` | Fill a packet with data. ff fills the packet with ones |
| `-b 10.0.2.10`    | Send packets to a broadcast address                    |
| `-t 10 10.0.2.10` | Limit the number of network hops                       |
| `-v 10.0.2.10`    | Increase verbosity                                     |

* `ncat (nc):` A network utility that provides several options for interacting with hosts using TCP or UDP over IPv4 and IPv6. Provided by the `nmap-ncat` package.

| Option                  | Purpose                                     |
| ----------------------- | ------------------------------------------- |
| `-l port`               | Listen for inbound connections on a port    |
| `10.0.2.10 port`        | Connect to remote system on a specific port |
| `-u udp_port`           | Specify a UDP port (TCP is the default)     |
| `-w time_count`         | Terminate connection after specified time   |
| `-l -k port`            | Accept multiple connections in listen mode  |
| `-v`                    | Increase verbosity                          |
| `-z`                    | Report connection status only               |
| `-i`                    | Set an idle timeout                         |
| `-v -z 10.0.2.10 22 80` | Scan multiple ports                         |
| `-v -z 10.0.2.10 20-80` | Scan a range of ports                       |
| `-c command`            | Executes given command via /bin/sh          |
| `-e command`            | Execute the given command                   |

## **205.3 Troubleshooting network issues (weight: 4)**

{% hint style="success" %}
Candidates should be able to identify and correct common network setup issues, to include knowledge of locations for basic configuration files and commands.



**Key Knowledge Areas:**

* Location and content of access restriction files
* Utilities to configure and manipulate ethernet network interfaces
* Utilities to manage routing tables
* Utilities to list network states.
* Utilities to gain information about the network configuration
* Methods of information about the recognised and used hardware devices
* System initialisation files and their contents (Systemd and SysV init)
* Awareness of NetworkManager and its impact on network configuration

**The following is a partial list of the used files, terms and utilities:**

* ip
* ifconfig
* route
* ss
* netstat
* /etc/network/, /etc/sysconfig/network-scripts/
* ping, ping6
* traceroute, traceroute6
* mtr
* hostname
* System log files such as /var/log/syslog, /var/log/messages and the systemd journal
* dmesg
* /etc/resolv.conf
* /etc/hosts
* /etc/hostname, /etc/HOSTNAME
* /etc/hosts.allow, /etc/hosts.deny
{% endhint %}

### Undrestanding Network Configuration Files and Locations

<pre class="language-bash"><code class="lang-bash"><strong>cat ifcfg-eth0
</strong>
BOOTPROTO=dhcp
DEVICE=eth0
DHCPV6C=yes
HWADDR=02:ne:5a:69:69:0f
IPV6INIT=yes
ONBOOT=yes
TYPE=Ethernet
USERCTL=no
</code></pre>

<table><thead><tr><th width="328">Option</th><th>Description</th></tr></thead><tbody><tr><td><code>IPADDR=10.0.1.10</code></td><td>Specify the IPv4 address</td></tr><tr><td><code>PREFIX=24</code></td><td>Specify the network prefix</td></tr><tr><td><code>NETMASK=10.0.10.1</code></td><td>Specify the netmask</td></tr><tr><td><code>GATEWAY=10.0.10.1</code></td><td>Specify the gateway</td></tr><tr><td><code>DNS1=192.168.154.3</code></td><td>Specify a DNS server</td></tr><tr><td><code>DNS2=10.216.6.3</code></td><td>Specify another DNS server</td></tr><tr><td><code>PEERDNS=yes</code></td><td>Modify the <code>/etc/resolv.conf</code> file (yes|no)</td></tr></tbody></table>

| Option                     | Description                                             |
| -------------------------- | ------------------------------------------------------- |
| `TYPE=Ethernet`            | The type of network interface device                    |
| `BOOTPROTO=none`           | Specify boot protocol (none\|dhcp\|bootp)               |
| `DEFROUTE=yes`             | Specify default route for IPv4 traffic (yes\|no)        |
| `IPV6_DEFROUTE=yes`        | Specify default route for IPv6 traffic (yes\|no)        |
| `IPV4_FAILURE_FATAL=no`    | Disable the device if the configuration fails (yes\|no) |
| `IPV6_FAILURE_FATAL=no`    | Disable the device if the configuration fails (yes\|no) |
| `IPV6INIT=yes`             | Enable or disable IPv6 on the interface (yes\|no)       |
| `IPV6_AUTOCONF=yes`        | Enable or disable autoconf configuration (yes\|no)      |
| `NAME=eth0`                | Specify a name for the connection                       |
| `UUID=...`                 | Specify the unique identifier for the device            |
| `ONBOOT=yes`               | Activate interface on boot (yes\|no)                    |
| `HWADDR=0e:a5:1a:b9:fc:89` | Specify the MAC address for the interface               |

* `/etc/hosts`: The host configuration file associates hostnames with an IP address.

<pre class="language-bash"><code class="lang-bash"><strong>cat /etc/hosts
</strong>
127.0.0.1 localhost.localdomain localhost
10.0.1.10 linuxmaster.example.com linuxmaster
</code></pre>

* `/etc/resolv.conf`: The resolver configuration file specifies DNS servers and searches domains for the host.

<pre class="language-bash"><code class="lang-bash"><strong>cat /etc/resolv.conf
</strong>
search example.com
nameserver 192.168.20.4
nameserver 172.8.100.3
</code></pre>

* `/etc/sysconfig/network`: This configuration file is used to specify global network settings.

<pre class="language-bash"><code class="lang-bash"><strong>cat /etc/sysconfig/network
</strong>
NETWORKING=yes
HOSTNAME=linuxmaster.example.com
</code></pre>

* `/etc/nsswitch.conf`: The Name Service Switch (NSS) configuration file is used to determine which sources to obtain name-service information and in what order.

```bash
cat /etc/nsswitch.conf
...
hosts: files dns
...
```

* `/etc/network/interfaces`: (Debian Based-System):

```bash
cat /etc/network/interfaces

# An example ethernet card setup: (broadcast and gateway are optional)
# 
# auto eth0
# iface eth0 inet static
# address 192.168.0.42
# network 192.168.0.0
# netmask 255.255.255.0
# broadcast 192.168.0.255
# gateway 192.168.0.1
```

#### NetworkManager

* Attempts to automate and simplify network configuration
* Implements a dynamic network control and configuration daemon to ensure connections stay active
* Proactivity creats (temporary) connections for detected network devices
* Provides user-friendly administrative tools: GUI, `nmtui`, and `nmcli`

> Lines beginning with the word "auto" are used to identify to be brought up when `ifup` is run with the `-a` optio. (This option is used by the system boot scripts.)

### Analyzing Network Diagnostics and Troubleshooting Network Issues

* `traceroute`: Tracks the route packets take from an IP network on their way to a given host.
* `traceroute6`: is identical to `traceroute` with the `-6` option.

<pre class="language-bash"><code class="lang-bash"><strong>traceroute [option] hostname [packet_len]
</strong></code></pre>

| Option                | Description                                                     |
| --------------------- | --------------------------------------------------------------- |
| `-I`                  | Use ICMP ECHO for probes                                        |
| `-T`                  | Use TCP SYN for probes                                          |
| `-f first_ttl`        | Specifies what TTL to start (default is 1)                      |
| `-g gateway`          | Specify a gateway to route the packets                          |
| `-i interface`        | Specify an interface to send packets through                    |
| `-m max_ttl`          | Specify the maximum number of hopes (default is 30)             |
| `-n`                  | Do not attempt to resolve host names                            |
| `-q`                  | Set the number of probe packet per hop (default is 3)           |
| `-w`                  | Set the time to wait, in seconds, for a response (default is 5) |
| `-4` \| `-6`          | Use IPv4 or IPv6 only                                           |
| `hostname packet_len` | Set the size of the probing packet (default is 60 bytes)        |

* `mtr`: A network diagnostic utility that combines the funcionality of the `traceroute` and `ping` command

```bash
mtr [options] hostname [packet_size]
```

| Option          | Description                                                                  |
| --------------- | ---------------------------------------------------------------------------- |
| `-r -c 5`       | Run `mtr` report mode and print out statistics based on the number of cycles |
| `-w`            | Run mtr in wide report mode and print out statistics                         |
| `-c 5`          | Specify the number of pings                                                  |
| `-n`            | Do not resolve hostnames                                                     |
| `-b`            | Show hostnames and IP addresses                                              |
| `-o "LSD NBAW"` | Specify the fields and order of fields                                       |
| `-a 10.0.2.20`  | Send outgoing packes through a specific interface                            |
| `-i seconds`    | Specify the interval for sending packets (default is 1)                      |
| `-m NUM`        | Specify the maximum number of hops (default is 30)                           |
| `-f NUM`        | Specify the maximim number of hops (default is 1)                            |
| `-u`            | Use UDP datagrams instead of ICMP ECHO                                       |
| `-T`            | Use TCP SYN packets instead of ICMP ECHO                                     |
| `-4` \| `-6`    | Use IPv4 or IPv6 only                                                        |

* `journalctl`: A logging system introduce by Systemd. Implemented by the `journald` daemon, which stores logs in a binary format that can viewed by using the `journalctl` utility. Settings for the Systemd journal can be updated bt modifying `/etc/systemd/journald.conf` or by adding configuration files to `/etc/systemd/journald.conf.d/`.

| Option      | Description                                          |
| ----------- | ---------------------------------------------------- |
| `-u unit`   | View messages for a particular Systemd unit          |
| `-f`        | Follow the journal for the latest messages           |
| `-e`        | Jump to the end of the journal                       |
| `-o format` | Change the format of the messages displayed          |
| `-x`        | Add explanation texts from the message catalogue     |
| `-p`        | Filter messages based on priority specified          |
| `-S`, `-U`  | Show entries from a specified date (since and until) |

* `dmesg`: A utiliy used to examine or control the kernel ring buffer. By default, it reads all messages from the kernel ring buffer.

| Option    | Description                                           |
| --------- | ----------------------------------------------------- |
| `-C`      | Clear the ring buffer                                 |
| `-c`      | Clear the ring buffer contents after printing         |
| `-D`      | Disable printing message to the console               |
| `-E`      | Enable printing message to the console                |
| `-e`      | Display local time and delta in human-readable format |
| `-H`      | Enable human readable format                          |
| `-F file` | Read log from a file                                  |

* `/var/log/syslog`: The main system log for Debian-based hosts. Stores all global system activity and startup messages. Options are controlled by `/etc/syslog.conf` or `/etc/rsyslog.conf` in newer versions. Additional configuration files can be added to \`/etc/rsyslog.d/.

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>cat /var/log/syslog
</strong>
...
Aug 27 14:26:13 linuxmaster systemd[3616238]: Listening on GnuPG network certificate management daemon.
Aug 27 14:26:13 linuxmaster systemd[3616238]: Listening on GnuPG cryptographic agent and passphrase cache (access for web browsers).
Aug 27 14:26:13 linuxmaster systemd[3616238]: Listening on GnuPG cryptographic agent and passphrase cache (restricted).
Aug 27 14:26:13 linuxmaster systemd[3616238]: Listening on GnuPG cryptographic agent (ssh-agent emulation).
Aug 27 14:26:13 linuxmaster systemd[3616238]: Listening on GnuPG cryptographic agent and passphrase cache.
Aug 27 14:26:13 linuxmaster systemd[3616238]: Listening on debconf communication socket.
...
</code></pre>

* `/var/log/messages`: The main system log on RHEL-based hosts. Stores all global system activity and startup messages. Options are controlled by `/etc/rsyslog.conf`. Additional configurations can be added to \`/etc/rsyslog.d/.

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>/var/log/messages
</strong>
...
Aug 21 03:41:13 linuxmaster rsyslogd: [origin software="rsyslogd" swVersion="8.24.0-57.el7_9.3" x-pid="737" x-info="http://www.rsyslog.com"] rsyslogd was HUPed
Aug 21 03:41:13 linuxmaster pure-ftpd: (?@127.0.0.1) [INFO] New connection from 127.0.0.1
Aug 21 03:41:13 linuxmaster pure-ftpd: (?@127.0.0.1) [INFO] __cpanel__service__auth__ftpd__klgf3cDA7cymYCN1 is now logged in
Aug 21 03:41:13 linuxmaster pure-ftpd: (__cpanel__service__auth__ftpd__klgf3cDA7cymYCN1@127.0.0.1) [INFO] Logout.
Aug 21 03:41:41 linuxmaster PAM-hulk[9971]: Brute force detection active: 550 LOGIN DENIED -- TOO MANY FAILURES
...
</code></pre>

### Managing Hostnames and Restricting Host-Level Access

* `/etc/hostname` and `/etc/HOSTNAME`: The /etc/hostname file is used to store hostname of the system. On some distributions, the `/etc/HOSYNAME` file is used for this purpose but is ofen aliased to `/etc/hostname`.
* `hostname` and `hostnamectl`: The `hostname` command is used to show ro set the system's hostname (i.e., `hostname HOSTNAME`). On Systemd systems, the `hostnamectl` command has replaced the `hostname` command (i.e., \`hostnamectl set-hostname HOSTNAME)

<pre class="language-bash"><code class="lang-bash"><strong>cat /etc/hostname
</strong>linuxmaster.example.com
</code></pre>

<pre class="language-bash"><code class="lang-bash"><strong>hostname
</strong>linuxmaster.example.com
</code></pre>

<pre class="language-bash"><code class="lang-bash"><strong>hostnamectl status
</strong>
   Static hostname: linuxmaster.example.com
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 7d9f417ed8ed4e2393f3dce9f5a89ef4
           Boot ID: 3179595ad0cd4454a4b0c7a5f33f27cc
    Virtualization: kvm
  Operating System: CentOS Linux 7 (Core)
       CPE OS Name: cpe:/o:centos:centos:7
            Kernel: Linux 3.10.0-1160.42.2.el7.x86_64
      Architecture: x86-64
</code></pre>

* `/etc/hosts`: This file is used to map hostnames and aliases to IP addresses.
* `/etc/hosts.allow` and `/etc/hosts.deny`: These files are used to determine whether a client has permission to connect to a network service on a remote host. The format of both files is as follow: `daemon_list:client_list [:command]`. The daemon list is a comma-seprated list of service daemons, the client list is a comma-separated list of clients, and command is an optional command that is executed when a client tries to access a server daemon. The keyword `ALL` may be used for the daemon and client lists in order to allow or deny access to all clients.

```bash
cat /etc/hosts.deny

sshd : ALL
```

```bash
cat /etc/hosts.allow

sshd : 10.0.3.*
```

```bash
cat /etc/hosts.deny

vsfpd : .example.com
```

```bash
cat /etc/hosts.allow

vsftpd : linuxmaster.example.com
```

## Sample Questions
