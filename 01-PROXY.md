# But, How to Set up your own Proxy server?

We are going to generate a cloud-based Debian machine, will be installing a Proxy Server called **Squid3** or handling personal traffic to demonstrate how you can be monitored and easily know your destination and packets details using **Wireshark** and **Tshark Network Analyzer**.

Let's start.

##### A cloud instance server with DigitalOcean (can be another cloud provider)

Log in into your provider and create a Droplet (Digital Ocean) machine instance, with a Debian 9 OS in it.

1. Select a 5$ plan
2. Select country for datacenter

##### Generating SSH Keys and Blocking Root Access (minimum security Controls) 

1. Generating SSH key for accessing the server, doing the following `ssh-keygen -t rsa`, once is done you will be a file named `id_rsa.pub` on the `.ssh` directory for the username logged.
2. Copying the generated key into **DigitalOcean** instance by doing: `cat id_rsa.pub`
3. Accessing via SSH like this: `ssh root@IP`  where the "IP" will be the IP address instance provided with **DigitalOcean**. This last one will send a email with the root password
4. Adding other user **(for security purposes)** by `adduser david` set the password using the `passwd` command
5. Changing the authentication methods on the sshd configuration file `/etc/ssh/sshd_config` setting the `PermitRootLogin` to `no`, this will let users access SSH only.
6. copy and generating the root authorized_keys ownership to the same user `david` this will disable root access to the server.

#####   Installing and configuring a Proxy Server

0. Installing squid3 (proxy server): `apt-get install squid3`

1. Back up the config file `cp /etc/squid3/squid.conf /etc/squid3/squid.conf.original`
2. Changing rights to the backup `chmod -w /etc/squid3/squid.conf.original` (everybody can read the file but can't edit it)
3. Inside the conf file: `/etc/squid3/squid.conf` you can change the TCP port if you want, by default is the 3128
4. Before starting the server, we need a firewall hole, you can check ports with the `nmap` tool by doing: `nmap [IP address]`
5. Checking the squid3 service by doing: `systemctl status squid3.service`, start if it is necessary.

6. Setting the **Squid3** proxy server on the `/etc/proxychains.conf` like `http [IP address] 3128`, where the **3128** is the default port provided by the **proxy service**

If you scan with nmap the 3128 port of the squid3 service won't be on the server

##### On the squid3 config file

1. Uncomment: `http_access deny !Safe_ports` this is for banning ports, if it isn't on port 80, the connections are gonna get drop
2. `http_access allow all` (the default is deny all), by setting this into allow, we have to be responsible for things done in the proxy. This is for honeypot proxy servers
3. restarting the squid3.service after changes: `systemctl restart squid3.service`
4. Ensure all browsers instances are closed
5. Trigger the proxychain firefox using: `proxychains4 firefox`

##### Sniffing your Proxy Server

On the same server instance, we start sniffing by installing **Tshark**

Install it using: `apt-get install tshark`

Sniffing the **eth0** interface by **TCP** on **3128 port**: `tshark -i eth0 -f "tcp port 3128" `

For verification you can test the **DNSleak test** and the **public IP address**

##### Setting a pipe from the server to our local machine,

We are going to Stream the traffic to the machine over SSH and push it  to Wireshark from monitoring

1. On the **DigitalOcean** instance, just install `apt-get install tshark wireshark` (if is necessary)

2. Setting the users on the wireshark group (you cannot run wireshark as root): `usermod -a -G wireshark david`

3. The newgrp command configures the group membership with which a user will log in. `newgrp wireshark`. This will let running tshark command on a regular user

 On the Host machine. We are going to pipe the output of the embedded **Tshark** pcap file. This is the tricky part of the workshop.

`wireshark -k -i <(ssh david@[IP DO instance] "tshark -F pcap -w - -f 'not tcp port 22'")`

Where:

1. -i (interface) not necessary a network interface, in this case is a live stream information -> output redirection
2. the command specifies a -F pcap (default format for tshark)
3. -w - writing on the standard output

This will prompt the GUI of wireshark (previously installed) and will sniff the incoming traffic

You can also filter the incoming traffic on the **WireShark GUI** like this

`tcp.port == 3128 && ip.src ==[IP DO machine] || tcp.port == 80 || tcp.port == 443`

This Wireshark filter should display your own traffic coming from HTTP ports and from the TCP Proxy port

#### What can we learn from this?

1. The person who controls the proxy server can see all the sites you are visiting, all the places you are going, log your traffic information and more.
2. Most source IP address on **3128 port** will be your home router, someone can look your router and access it and they can do a lot of things like, change DNS servers, monitor all URLs request, your pc can be placed on the DMZ of the router, etc
3. You cannot see encrypted data easily.
4. Anonymity is relative