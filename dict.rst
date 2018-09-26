netfilter dict extension
========================


Summary
-------

The dict, short for dictionary, netfilter extension provides a mechanism from within nftables to do table lookups on environment metadata that is not present in the packet and not contained within the rule.
In a firewall you often wish to block or manipulate packets based on things not immediately evident in the packet, but things that can often be calculated in various ways.

dict provides the ability to create dictionaries (lookup tables) stored within kernel space for fast lookups. These tables can be maintained by userspace applications where it is more convenient to calculate various network metadata.
In our case, we have a userspace daemon (packetd) which listens to various packets with NFqueue and builds various dictionaries of metadata based on the traffic it sees and the information it gathers.

Syntax
------

The syntax of an dict nft expression is as follows:

dict <table_name> <key expression> <field_name> <data_type> <value>

table_name
~~~~~~~~~~
Table name is a string that specifies the name of the table that the key expression will key into. Max length is 128.

key_expression
~~~~~~~~~~~~~~
The key expression is an nftables expression that produces a table key depending on the contents of the packet under examination. Below are a few common examples of key expresssion:

ct id
  The conntrack id of the conntrack associated with the packet. This is typically used as the key expression into the session table
ip saddr
  The source ip address of the packet. This is typically used as the key into the host table
ether saddr
  The source MAC address of the packet. This is typically used as the key into the device (MAC) table
dict user ct id username long_string
  The username associated with the session of the packet being examined. This is typically used as the key into the user table

field_name
~~~~~~~~~~
Table specifies the table of dictionaries, the key expression specifies the dictionary (row), and the field name specifies the entry in the dictionary (row) to look up. It is a string with a maximum length of 128.

data_type
~~~~~~~~~
This can be any fixed length data type supported by nftables natively. Common examples are listed below:

=========== =========
Type        Description
=========== =========
bool        boolean (true/false)
ether_addr  MAC address
ipv4_addr   IPV4 address
ipv6_addr   IPV6 address
long_string a string up to 128 bytes. This may also include wildcard characters like: ?, \*, [, ], !, and -.
integer     4 byte integer
=========== =========

value
~~~~~
Value may be just a value or may be a value expression. The specified value must match the DATA_TYPE specified. Below are some examples

=========== =========
Type        Example
=========== =========
bool        true, false
ether_addr  00:20:00:aa:bb:cc
ipv4_addr   192.168.1.1, 192.168.1.0/24, 192.168.1.1-192.168.1.50
ipv6_addr   2001:0db8:ac10:fe01:0000:0000:0000:0000, 2001:0db8:ac10:fe01::
long_string brett, bre*, ?rett, bret[a-z]
int         5, < 3, > 0, <= 7, >= 10, != 100
int64       100447866061385
=========== =========

actions
~~~~~~~

Much like other nft expressions you can use this as a "condition":

*dict sessions ct id application long_string NETFLIX*

And you can also set values in the table:

*dict sessions ct id username set user123*

You can also flush an entire dictionary (row) from a table:

*dict sessions ct id flush*


Example Tables
--------------

Lets look at a few examples of how this might be useful. In our case the userspace daemon, packetd, processes packets and uses various ways to calculate metadata.
It maintains all of this metadata in various dictionaries. The most useful and commonly used table is the "session" table which stores a list of all current sessions and what is known about them:
Lets look at a stripped-down imaginary sessions table as an example. To keep it simple we'll reduce the number of columns quite a bit.

sessions:

============= ========== =========== ================= ================= =========== =========== ============== ============== ===================== =================== =========== ======================= =============== =========== ================= ==================
conntrack_id* session_id ip_protocol client            server            client_port server_port client_country server_country server_cert_cn        dns_prediction      application application_chain       client_hostname username    category          dns_request
============= ========== =========== ================= ================= =========== =========== ============== ============== ===================== =================== =========== ======================= =============== =========== ================= ==================
0x11111166    12341210   UDP         192.168.1.100     192.0.2.200       11400       53          XL             US                                                       DNS         IP:UDP:DNS              windows_pc      user123                       google.com
0x11111177    12341212   UDP         192.168.1.100     192.0.2.220       11500       53          XL             US                                                       DNS         IP:UDP:DNS              windows_pc      user123                       www.netflix.com
0x11112222    12341234   TCP         192.168.1.100     192.0.2.100       11223       443         XL             US             netflix.com           www.netflix.com     NETFLIX     IP:TCP:SSL:NETFLIX      windows_pc      user123     video_streaming
0x11112222    12341234   TCP         192.168.1.100     192.0.2.100       11223       443         XL             US             google.com            google.com          GMAIL       IP:TCP:SSL:GMAIL        windows_pc      user123     technology
0x11112244    12341235   UDP         192.168.1.100     192.0.2.200       11400       9000        XL             CN                                                       BITTORRENT  IP:UDP:BITTORRENT       windows_pc      user123
0x11112255    12341236   UDP         192.168.1.100     192.0.2.220       11500       9000        XL             CN                                                       BITTORRENT  IP:UDP:BITTORRENT       windows_pc      user123
...
============= ========== =========== ================= ================= =========== =========== ============== ============== ===================== =================== =========== ======================= =============== =========== ================= ==================


Looking at this example, the first column (conntrack_id) is the "key" into the table. Using the key you can lookup the appropriate row, in this case the session.
Then using the "field" parameter you can lookup the metadata associated with that session (conntrack_id).

The first few fields aren't particularly interesting. The "client" and "ip protocol" can be calculated easily based on information already in the packet using traditional nft expressions.
The later ones are more interesting. These fields have been calculated by packetd (the userspace daemon) by using signatures, heuristics, lookups, and other techniques. packetd then writes these values to the table and passes the packet back via nfqueue.

At this point you can use these fields in nftables to control traffic:

- a session's hostname or username
  
  - example: prioritize or limit a specific user

- a session's application

  - example: block bittorrent traffic
  - example: route netflix to a specific interface

- a session's geographical information

  - example: block all inbound sessions except from certain countries
  - example: block internal devices from accessing certain countries
     
- a session's site's category

  - example: block pornography sites
  - example: deprioritize video_streaming sites

- etc

The syntax to accomplish these things is described lower in the document.

There are also many other dictionaries maintained by packetd. Lets look at two other fictious dictionaries:

hosts:

================= ================= =================== ========= ================== ============ ===================================== =========== ============= =============== ==============
host*             mac_address       mac_address_vendor  interface hostname           host_profile http_user_agent                       username    quota_size    quota_remaining quota_exceeded
================= ================= =================== ========= ================== ============ ===================================== =========== ============= =============== ==============
192.168.1.100     00:11:22:33:44:55 Intel Corporation   5         windows_pc         windows      Mozilla/5.0 (Windows NT 10.0; Win6... user123     1000000000    1234333         false
192.168.1.101     00:11:22:33:44:66 Samsung Electro...  5         samsung-sm-g935v   android      Dalvik/2.1.0 (Linux; U; Android 8.... user531     1000000000    -8000           true
...
================= ================= =================== ========= ================== ============ ===================================== =========== ============= =============== ==============

users:

================= ======================== ============= =============== ==============
username*         usergroups               quota_size    quota_remaining quota_exceeded
================= ======================== ============= =============== ==============
user123           engineering,exec,onsite  1000000000    1234333         false
user531           sales,onsite             1000000000    1234333         false
...
================= ======================== ============= =============== ==============

devices:

================= ======================== ============= ================ ============== =========== ============= =============== ==============
mac_address       mac_address_vendor       interface     hostname         device_profile username    quota_size    quota_remaining quota_exceeded
================= ======================== ============= ================ ============== =========== ============= =============== ==============
00:11:22:33:44:55 Intel Corporation        5             windows_pc       windows        user123     1000000000    1234333         false
00:11:22:33:44:66 Samsung Electronics Ltd  5             samsung-sm-g935v android        user531     1000000000    -8000           true
...
================= ======================== ============= ================ ============== =========== ============= =============== ==============

The "hosts" table stores a table of all the hosts (unique IPs) seen sending traffic on the network and various attributes that are known about them.
The "users" table stores a list of known users, usually pulled from some captive portal or directory service, and various attributes of the user.
The "devices" table stores a list of all known seen MAC addresses.

Users and hosts and devices are all tracked separately, because they have a complex non 1:1:1 relationship between them.
Often users have multiple devices, and many devices are multi-user machines and sometimes its better to use IP addresses (hosts) and sometimes MACs (devices).

Some example use cases of how these tables can be used in nft rules to control traffic:

- a host's profile
  
  - example: all androids hosts are blocked from certain services
  - example: all ipad devices use the second WAN link
    
- a host's quota

  - example: deprioritize hosts over quota
  - example: limit/block hosts over quota
    
- match on a user's group

  - example:block access to certain services for "sales" users
    
- etc

dict expressions can combined in various ways just like regular nft expression to express more complex ideas:

- block video_streaming category for hosts over their quota
- block social_networking category for users in the sales group
- etc

Example Rules
-------------

This is various examples using the above tables.


Block netflix:

*nft add rule ip filter forward dict sessions ct id application long_string NETFLIX reject*

Reject any packet who's mac-vendor contains the string NEST:

*nft add rule ip filter forward dict host ip saddr mac-vendor long_string \*NEST\* reject*

If a host is attempting to connect to a webserver, but has not been authenticated via captive portal, redirect to the captive portal page:

*nft add rule ip filter forward tcp dport 80 dict host ip saddr captive-portal-authenticated bool false dnat to 127.0.0.1:80*

If a device has exhausted its quota, reject its traffic:

*nft add rule ip filter forward dict devices ether saddr quota-remaining integer <= 0 reject*

If a user has exceeded their quota, reject its traffic. This uses the conntrack id to look up the username associated with a session,
and then uses the username as the key into the user table.

*nft add rule ip filter forward dict users dict session ct id username long_string quota-exceeded bool true reject*

Reject all traffic destined for a particular country:

*nft add rule ip filter forward dict session ct id server_country long_string NL reject*

For all hosts, set the mac_address field with the source mac address of the packet:

*nft add rule ip filter prerouting ct state new dict host ip saddr mac_address ether_addr set ether saddr*

Block netflix for all users in the sales group. This looks up the user from the sessions tables using ct id, then uses that to lookup the user in the users table and then finds the groups.

*nft add rule ip filter forward dict users dict sessions ct id username long_string  user_group long_string \*sales\* reject*


