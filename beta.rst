SD-WAN Router Beta
==================

The following document provides a brief overview of the SD-WAN router beta.

Beware: This beta is intended to show the future direction of the SD-WAN router.
It is still very much a beta. It has many bugs and many features still missing.
However, while it is a young product, it should be stable and is very fast, even on tiny hardware.
It will allow you to get a sneak peak at the future direction and some of the exciting technology on the way.

Overview
--------

What is the SD-WAN router?
~~~~~~~~~~~~~~~~~~~~~~~~~~

In short, its a router optimized for "SD-WAN" (software defined WANs) 
type deployments. These are typically sites with several internet 
connections, and perhaps "virtual" internet connections through tunnel 
connections to cloud services.
An SD-WAN router specialized in maximizing performance, reliability, and 
security of the connection. SD-WAN routers are often used to handle 
multisite deployments and are coupled with inexpensive on-prem hardware, 
being cloud managed, and security services in the cloud.

How is this different than NGFW?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

SD-WAN routers and NGFW/UTM have a great deal of overlap. NGFW and UTMs 
main focus is on security and have some advanced functionality. SD-WAN 
router's main focus is smart routing for maximized performance and 
reliability, but also have some security technology. NGFW/UTM typically 
use heavy gateway devices to perform expensive security technologies at 
each physical gateway. SD-WAN promises to move some of these 
technologies to data-centers and cloud providers, and use more 
inexpensive and simpler physical gateway devices that are cloud managed.

Don't worry, much of the stuff you love about Untangle (like reports) 
are still the same!

Is the SD-WAN router free? Open source?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Similar to NGFW, the SD-WAN router is open source (GPLv2).
However, it does rely on several commercial cloud intelligence services that provide data and or services.
Also similar to NGFW, the SD-WAN router will have a free version and a paid version, however the free version
will be full functionality, but limited in throughput. Sites with less throughput can use the SD-WAN router (with full functionality) for free.
Sites with more sites will need to purchase a subscription to get increasing levels of throughput.

NOTE: The throughput limit has been disabled for the beta because the beta has much SD-WAN functionality currently disabled.

Beta Testing
------------

To get started on the beta test first you must get one of the currently supported platforms.

Hardware
~~~~~~~~

In the long term, we hope to support more platforms. If you have feedback about desired platforms, we would love to hear it.
In the meantime we have selected two widely available highly performant (yet affordable) routers:

`Linksys WRT-1900ACS <https://www.linksys.com/us/p/P-WRT1900ACS/>`
`Linksys WRT-3200ACM <https://www.linksys.com/us/p/P-WRT3200ACM/>`

Beware: The model number must be exact! - For example, the WRT1900AC is not the same as the WRT1900ACS.

Installation
~~~~~~~~~~~~

First, install your router as normal, with the "Internet" port plugged into your internet/modem or upstream router/switch.
Also, connect your installation device (PC, tablet, phone) with to one of the LAN ports, or connect wireless using the
information provided by Linksys.

The default internal IP of the linksys is 192.168.1.1, so connect to "http://192.168.1.1" in your browser and follow the Linksys instructions.
After installation is complete and the installation device is online you can flash the SD-WAN router firmware.


Flashing
~~~~~~~~

Download the appropriate firmware for your router.

`Linksys WRT-1900ACS SD-WAN router factory firmware <https://download.untangle.com/sdwan-linksys-wrt1900acs-factory.img/>`
`Linksys WRT-3200ACM SD-WAN router factory firmware <https://download.untangle.com/sdwan-linksys-wrt3200acm-factory.img/>`

In the linksys administration UI click on "Connectivity"

.. image:: images/beta/linksys_1.png
    :scale: 40%

Then select "Choose File" and choose the file you downloaded above. Then select "Start."

.. image:: images/beta/linksys_2.png
    :scale: 40%

At this point the router will begin the flashing process. Wait 2 minutes, get a coffee - relax.
Do not reboot the router.

The Linksys administration interface will wait for the linksys to return, but it will not.
Instead you need to close that window and connect to "http://192.168.1.1" in a new tab.

NOTE: If you were using another subnet before (something other than 192.168.1.x) you may need to reboot your device or refresh your DHCP Lease.
If you are connecting wirelessly, you will need to connect to the "Untangle" SSID with a password of "12345678".


Setup Wizard
~~~~~~~~~~~~

Once connected to 192.168.1.1 you will see the setup wizard.

.. image:: images/beta/sdwan_setup.png
    :scale: 40%

The setup wizard will walk you through basic configuration of the SD-WAN router.

NOTE: The setup wizard is a work in progress, and this is not at all what the final version will be like.

The first step allows you to choose a new password.

**IMPORTANT**: For better debugging SSH and HTTP are available by default on the WAN. This allows easier access to troubleshoot if something goes wrong.
This will likely change at some point, but for now **choose a good password**.

The following steps allow you to configure each interface.

.. image:: images/beta/sdwan_setup_2.png
    :scale: 40%

On the left side you can see the interface being configured (lan1 in the above example) and below its configuration.
Depending on its configuration, you may have additional options on the right side.

The defaults are likely the best options. The 4 lan (lan1-lan4) ports and the two wifi cards (wlan0-wlan1) are bridged together into your "internal" zone.
The wan port (labeled "internet" physically) is configured for DHCP and DHCP6, you may configure it statically if DHCP is not available.

On the "wlan0" and "wlan1" steps, you can click on the "Wifi Settings" tab to change the SSID and password for wireless access.

After this the device is fully configured!


A Quick Tour
------------

Lets get to know your SD-WAN router

Dashboard & Reports
~~~~~~~~~~~~~~~~~~~

The first step to optimizing your network traffic for maximum performance is seeing and understanding what traffic is on the network.
Unlike almost all firmware solutions, the SD-WAN router has extensive report that allow close inspection and monitoring of network traffic.

.. image:: images/beta/dashboard.png
    :scale: 40%

For those familiar with NGFW, you'll be right at home. These reports and dashboard are very similar.

Individual reports can be viewed under reports.
You can zoom in on charts and hide certain values from the key by clicking on them.
You can view the data by clicking on the data icon in the upper right.
You can customize the view by clickin on the settings icon (changes are not saved for now though).
You can add conditions at the top to view the given report, but through the specified "filter," like for a specific client, application, port, etc.

The customizable dashboard shows a collection of reports over the specified timeframe.
This can be used to keep a continuous view of activity on the network.
Similar to reports you can add conditions to view a specific client, application, port, etc.
Widgets can be added by clicking on the "Widgets" in the upper right.

(Don't worry - custom widgets like NGFW are coming soon!)

Settings
~~~~~~~~

The settings are where all the router settings live.

"Network" contains all the network configuration related settings, like interfaces and DNS.

"Firewall" contains all the firewall rules configuration.
This section is very different than NGFW and will be more documented in the future.
Essentially, the SD-WAN router by gathering all the layer-7 intelligence (similar to NGFW) but "injecting" it into the kernel so it can be used in a layer-3 firewall. This gives you the power of layer-7, but the performance of layer-3.
Rules are powerful, but can be a bit hard to configure - more info coming on this soon!

"Smart Routing" contains all the SD-WAN logic, from intelligent path selection and balancing based on latency, bandwidth, jitter, etc.
This section ensure you can get the maximum performance if using multiple WANs (be they physical or VPN tunnels).
NOTE: This section has been disabled for beta until we are confident it is working as expected. Similarly virtual WANs (OpenVPN, Wireguard, etc) are also not available.

"System" contais all the system settings.
You can change the hostname/domainname here, or the admin password.
Eventually many settings, like timezone, language, etc will live here.

"Monitor" will show the sessions/hosts/users/devices - very similar to NGFW.
This section in currently unimplemented.

Feedback
~~~~~~~~

The goal of the beta program is to show some of the upcoming tech, but also to gather feedback and make sure we're on the right track.

Is there something you would love to see that is critical functionality?
Is there something that you would like to see improved?
Did you have a problem or encounter a bug?

Please let us know!




