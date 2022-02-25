
# 7 Linux networking commands

[7 Linux networking commands that every sysadmin should know](https://www.redhat.com/sysadmin/7-great-network-commands)

## ip

The ip command is one of the basic commands every administrator will need in daily work, from setting up new systems and assigning IPs to troubleshooting existing systems. The ip command can show address information, manipulate routing, plus display network various devices, interfaces, and tunnels.

The syntax is as follows:

```sh
ip <OPTIONS> <OBJECT> <COMMAND>
```

The OBJECT is the most important part of the synopsis, and the following are supported (some omitted for brevity):

address - protocol (IPv4 or IPv6) address on a device.

    tunnel - tunnel over IP
    route - routing table entry
    rule - rule in routing policy database
    vrf - manage virtual routing and forwarding devices
    xfrm - manage IPSec policies

Here are some common use cases for the ip command.

To show the IP addresses assigned to an interface on your server:

[root@server ~]# ip address show 

To assign an IP to an interface, for example, enps03:

[root@server ~]# ip address add 192.168.1.254/24 dev enps03

To delete an IP on an interface:

[root@server ~]# ip address del 192.168.1.254/24 dev enps03

Alter the status of the interface by bringing the interface eth0 online:

[root@server ~]# ip link set eth0 up

Alter the status of the interface by bringing the interface eth0 offline:

[root@server ~]# ip link set eth0 down

Alter the status of the interface by changing the MTU of eth0:

[root@server ~]# ip link set eth0 mtu 9000

Alter the status of the interface by enabling promiscuous mode for eth0:

[root@server ~]# ip link set eth0 promisc on

Add a default route (for all addresses) via the local gateway 192.168.1.254 that can be reached on device eth0:

[root@server ~]# ip route add default via 192.168.1.254 dev eth0

Add a route to 192.168.1.0/24 via the gateway at 192.168.1.254:

[root@server ~]# ip route add 192.168.1.0/24 via 192.168.1.254

Add a route to 192.168.1.0/24 that can be reached on device eth0:

[root@server ~]# ip route add 192.168.1.0/24 dev eth0

Delete the route for 192.168.1.0/24 via the gateway at 192.168.1.254:

[root@server ~]# ip route delete 192.168.1.0/24 via 192.168.1.254

Display the route taken for IP 10.10.1.4:

[root@server ~]# ip route get 10.10.1.4

ifconfig

The ifconfig command was/is a staple in many sysadmin's tool belt for configuring and troubleshooting networks. It has since been replaced by the ip command discussed above. However, if you would like more information on it, check out our previously published article here.
mtr

MTR (Matt's traceroute) is a program with a command-line interface that serves as a network diagnostic and troubleshooting tool. This command combines the functionality of the ping and traceroute commands. Just like a traceroute, the mtr command will show the route from a computer to a specified host. mtr provides a lot of statistics about each hop, such as response time and percentage. With the mtr command, you will get more information about the route and be able to see problematic devices along the way. If you see a sudden increase in response time or packet loss, then obviously, there is a bad link somewhere.

The syntax of the command is as follows:

mtr <options> hostname/IP

Let's look at some common use cases.

The basic mtr command shows you the statistics, including each hop (hostnames) with time and loss%:

[root@server ~]# mtr google.com

Show numeric IP addresses (if you use -g, you will get IP addresses (numbers) instead of hostnames):

[root@server ~]# mtr -g google.com

Show the numeric IP addresses and hostnames, too:

[root@server ~]# mtr -b google.com

Set the number of pings that you want to send:

[root@server ~]# mtr -c 10 google.com

Get a report of the mtr command result:

[root@server ~]# mtr -r -c 10 google.com > mtr-command-google-output

or:

[root@server ~]# mtr -rw -c 10 google.com > mtr-command-google-output

Force the use of the TCP instead of the ICMP:

[root@server ~]# mtr –tcp google.com

Force the use of the UDP instead of the ICMP:

[root@server ~]# mtr –udp google.com

Set the maximum amount of hops:

[root@server ~]# mtr -m 35 216.58.223.78

Define the packet size:

[root@server ~]# mtr -r -s 50 google.com

Print to CSV output:

[root@server ~]# mtr –csv google.com

Print to XML output:

[root@server ~]# mtr –xml google.com

tcpdump

 

The tcpdump command is designed for capturing and displaying packets.

You can install tcpdump with the command below:

[root@server ~]# dnf install -y tcpdump

Before starting any capture, you need to know which interfaces tcpdump can use. You will need to use sudo or have root access in this case.

[root@server ~]# tcpdump -D

  1 eth0
  2 nflog
  3 nfqueue
  4 usbmon1
  5 any
  6 lo (Loopback)

If you want to capture traffic on eth0, you can initiate that with tcpdump -i eth0 sample output:

[root@server ~]# tcpdump -i eth0
[root@server ~]# tcpdump -i eth0 -c 10

Capture traffic to and from one host

You can filter out traffic coming from a specific host. For example, to find traffic coming from and going to 8.8.8.8, use the command:

[root@server ~]# tcpdump -i eth0 -c 10 host 8.8.8.8

For traffic coming from 8.8.8.8, use:

[root@server ~]# tcpdump -i eth0 src host 8.8.8.8

For outbound traffic going to 8.8.8.8, use:

[root@server ~]# tcpdump -i eth0 dst host 8.8.8.8

Capture traffic to and from a network

You can also capture traffic to and from a specific network using the command below:

[root@server ~]# tcpdump -i eth0 net 10.1.0.0 mask 255.255.255.0

or:

[root@server ~]# tcpdump -i eth0 net 10.1.0.0/24

You can also filter based on either source or destination.

Based on the source (traffic coming from):

[root@server ~]# tcpdump -i eth0 src net 10.1.0.0/24

Based on the destination (traffic going to):

[root@server ~]# tcpdump -i eth0 dst net 10.1.0.0/24

Capture traffic to and from port numbers

Capture only DNS port 53 traffic:

[root@server ~]# tcpdump -i eth0 port 53

For a specific host,

[root@server ~]# tcpdump -i eth0 host 8.8.8.8 and port 53

To capture only HTTPS traffic,

[root@server ~]# tcpdump -i eth0 -c 10 host www.google.com and port 443

To capture all port except port 80 and 25,

[root@server ~]# tcpdump -i eth0 port not 53 and not 25

netstat

The netstat tool for printing network connections, routing tables, interface statistics, masquerade connections, and multicast memberships. This utility is part of the net-tool package, as is ifconfig. In the new iproute2 package, the ss tool is used to achieve the same objectives.

If netstat is not found on your system, install it with this command:

[root@server ~]# dnf install net-tools

The primary usage of netstat is without any parameters:

[root@server ~]# netstat

For advanced usage, expand the netstat command with options:

netstat <options>

Or list the options one by one:

netstat <option 1> <option 2> <option 3>

To list all ports and connections regardless of their state or protocol, use:

[root@server ~]# netstat -a

List all TCP ports by running:

[root@server ~]# netstat -at

List all UDP ports with:

[root@server ~]# netstat -au

To return a list of only listening ports for all protocols, use:

[root@server ~]# netstat -l

List all listening TCP ports with:

[root@server ~]# netstat -lt

Return only listening UDP ports by running:

[root@server ~]# netstat -lu

To list UNIX listening ports, use:

[root@server ~]# netstat -lx

Display statistics for all ports regardless of the protocol with:

[root@server ~]# netstat -s

List statistics for TCP ports only with:

[root@server ~]# netstat -st

To view the TCP connections with the PID/Program name listed, use:

[root@server ~]# netstat -tp

To find a process that is using a particular port number, run:

[root@server ~]# netstat -an | grep ‘:<port number>’

nslookup

Use the nslookup utility to query Internet name servers interactively. Use it to perform DNS queries and receive domain names or IP addresses, or any other specific DNS records.

Consider the following common examples.

To find the A record of а domain:

[root@server ~]# nslookup example.com

To check the NS records of a domain:

[root@server ~]# nslookup -type=ns example.com

To find the MX records responsible for the email exchange:

[root@server ~]# nslookup -query=mx example.com

To find all of the available DNS records of a domain:

[root@server ~]# nslookup -type=any example.com

To check the use of a specific DNS server (in this case, query using the specific nameserver ns1.nsexample.com):

[root@server ~]# nslookup example.com ns1.nsexample.com

Checking DNS A records to see the IPs of a domain is a common practice, but sometimes you need to verify if an IP address is related to a specific domain. For that purpose, you need a reverse DNS lookup. 

[root@server ~]# nslookup 10.20.30.40

ping

Ping is a tool that verifies IP-level connectivity to another TCP/IP computer by sending Internet Control Message Protocol (ICMP) Echo Request messages. The receipt of corresponding Echo Reply messages is displayed, along with round-trip times. Ping is the primary TCP/IP command used to troubleshoot connectivity, reachability, and name resolution.

Simple ping commands take only one parameter: The hostname or the host IP address that you want to verify. A simple ping example is just like below:

[root@server ~]# ping google.com
PING google.com (216.58.206.174) 56(84) bytes of data.
64 bytes from sof02s27-in-f14.1e100.net (216.58.206.174): icmp_seq=1 ttl=56 time=10.7 ms
64 bytes from sof02s27-in-f14.1e100.net (216.58.206.174): icmp_seq=2 ttl=56 time=10.2 ms
64 bytes from sof02s27-in-f14.1e100.net (216.58.206.174): icmp_seq=3 ttl=56 time=10.4 ms
64 bytes from sof02s27-in-f14.1e100.net (216.58.206.174): icmp_seq=4 ttl=56 time=10.4 ms
64 bytes from sof02s27-in-f14.1e100.net (216.58.206.174): icmp_seq=5 ttl=56 time=17.3 ms
^C
--- google.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4005ms
rtt min/avg/max/mdev = 10.219/11.844/17.381/2.773 ms

You need to stop the ping command by pressing CTRL+C. Otherwise, it will ping until you stop it. After every ping command, it will display a summary report with the following information:

    Min: Minimum time that it takes to get a response from the host that has been pinged from your end.
    Avg: Average time that it takes to get a response from the host that has been pinged from your end.
    Max: Maximum time that it takes to get a response from the host that has been pinged from your end.

Also, you will see TTL, which stands for Time To Live. Ping uses a numerical TTL value to attempt to reach a given host computer via the route path. This is also known as the hop limit.

Normally, when you run a simple ping command without passing any additional parameters, Linux will ping that host for an infinite amount of time. If you want to ping a host ten times, use the following command:

[root@server ~]# ping -c 10 google.com

Use option -q to view only the ping statistics summary:

[root@server ~]# ping -c 10 google.com

On systems with multiple interfaces, you can specify the interface for the ping command to use. If the system has both eth0 and eth1 and I want to ping using the eth0:

[root@server ~]# ping -I eth0 google.com

Or use the address on the interface, assuming the interface as 10.233.201.45 as the IP:

[root@server ~]# ping -I 10.233.201.45 google.com

You can also ping and specify the IP version as either v4 or v6:

[root@server ~]# ping -4 google.com
[root@server ~]# ping -6 google.com

While pinging a host, you'll find different output from the ping results, including the following three examples.
More Linux resources

Destination Host Unreachable

The possible best reason is there is no route from the local host system and to the destination desired destination host, or a remote router reports that it has no route to the destination host.
Request timed out

This result means that no Echo Reply messages were received within the default time of one second or the time that you set while you are pinging that host. This can be due to many different causes; the most common include network congestion, failure of the ARP request, packet filtering/firewall, etc.
Unknown host/Ping Request Could Not Find Host

Maybe you misspelled the hostname or the host does not exist at all in the network.

You must have 0% packet loss for every ping result with a good latency or lower response time. Depending on which transmission medium (UTP, fiber optics cable, Wi-Fi) you're using, your latency will differ.