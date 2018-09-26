Netfilter
=========

Flow
----

The netfilter flow diagram goes here

Tables
------

The netfilter table docs go here

Chains
------

The netfilter chain docs go here

Marks
-----

Marks and Connmarks are used heavily to store metadata about a packet or session.
The following tables show how the various bits within the mark or connmark are used.

Mark (packet mark):

========== =============================== ===========
Bitmask    Name                            Description
---------- ------------------------------- -----------
0x000000ff Source Interface Zone           The incoming (source) interface ID of this packet is indicated in these bits
0x0000ff00 Destination Interface Zone      The outgoing (destination) interface ID of this packet is indicated in these bits
0x00ff0000 QoS                             TBD (Reserved)
0x01000000 Source Interface is WAN         The incoming (source) WAN status is indicated in this bit
0x02000000 Destination Interface is WAN    The outgoing (destination) WAN status is indicated in this bit
========== =============================== ===========

Connmark (connection/session mark):

========== =============================== ===========
Bitmask    Name                            Description
---------- ------------------------------- -----------
0x000000ff Client Interface Zone           The client interface ID of this packet is indicated in these bits
0x0000ff00 Server Interface Zone           The server interface ID of this packet is indicated in these bits
0x00ff0000 QoS                             TBD (Reserved)
0x01000000 Client Interface is WAN         The client interface WAN status is indicated in this bit
0x02000000 Server Interface is WAN         The server interface WAN status is indicated in this bit
========== =============================== ===========

