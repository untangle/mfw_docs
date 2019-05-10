SD-WAN Router Beta
==================

Whilst we  plan to support more platforms in the future, we have selected three widely available highly performant (yet affordable) routers:

- `Linksys WRT-1900ACS <https://www.linksys.com/us/p/P-WRT1900ACS/>`_
- `Linksys WRT-3200ACM <https://www.linksys.com/us/p/P-WRT3200ACM/>`_
- `Linksys WRT-32X <https://www.linksys.com/us/p/P-WRT32X/>`_

Beware: The model number must be exact! - For example, the WRT1900AC is not the same as the WRT1900ACS.


Installation onto a Linksys Router
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

First, install your router as normal, with the "Internet" port plugged into your internet/modem or upstream router/switch.
Also, connect your installation device (PC, tablet, phone) to one of the LAN ports, or connect wirelessly using the
information provided by Linksys.

The default internal IP of the linksys is 192.168.1.1, so connect to "http://192.168.1.1" in your browser and follow the Linksys instructions.
After installation is complete and the installation device is online you can flash the SD-WAN router firmware.


Flashing onto a Linksys Router
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Download the appropriate firmware for your router.

- `Linksys WRT-1900ACS SD-WAN router factory firmware <http://download.untangle.com/sdwan/beta/sdwan-wrt1900acs-factory_v0.1.0beta1-85-g6fe42a850b_20190509T1211.img>`_
- `Linksys WRT-3200ACM SD-WAN router factory firmware <http://download.untangle.com/sdwan/beta/sdwan-wrt3200acm-factory_v0.1.0beta1-85-g6fe42a850b_20190509T1211.img>`_
- `Linksys WRT-32X SD-WAN router factory firmware <http://download.untangle.com/sdwan/beta/sdwan-wrt32x-factory_v0.1.0beta1-85-g6fe42a850b_20190509T1211.img>`_

Or the virtual image:

- `VirtualBox VDI <http://download.untangle.com/sdwan/beta/sdwan-x86-64-combined_v0.1.0beta1-85-g6fe42a850b_20190509T1211.vdi>`_
- `VMWare VMDK <http://download.untangle.com/sdwan/beta/sdwan-x86-64-combined_v0.1.0beta1-85-g6fe42a850b_20190509T1211.vmdk>`_


In the linksys administration UI click on "Connectivity"

.. image:: images/beta/linksys_1.png
    :scale: 30%

Then select "Choose File" and choose the file you downloaded above. Then select "Start."

.. image:: images/beta/linksys_2.png
    :scale: 30%

At this point the router will begin the flashing process. Wait 2 minutes, get a coffee - relax.
Do not reboot the router.

The Linksys administration interface will wait for the linksys to return, but it will not.
Instead you need to close that window and connect to "http://192.168.1.1" in a new tab.

*NOTE:* If you were using another subnet before (something other than 192.168.1.x) you may need to reboot your device or refresh your DHCP Lease.
If you are connecting wirelessly, you will need to connect to the "Untangle" SSID with a password of "12345678".

Other Tips for Untangle SD-WAN Router running on the Linksys models
----------

Resetting Linksys Routers to Factory Defaults
~~~~~~~~~~~~~~~~~~~~~~~~~

To reset to factory defaults (SD-WAN router defaults) just hold down the reset on the button for 10 seconds while booted up.
It will reboot when released and initialize new settings. At this point follow the Setup Wizard instructions above.

Reset to Linksys Firmware
~~~~~~~~~~~~~~~~~~~~~~~~~

Download the Linksys firmware.

- `Linksys WRT1900ACS stock firmware <http://www.linksys.com/us/support-article?articleNum=165487>`_
- `Linksys WRT3200ACM stock firmware <https://www.linksys.com/us/support-article?articleNum=207552>`_
- `Linksys WRT32X stock firmware <https://www.linksys.com/us/support-article?articleNum=226203>`_

Rename it something like firmware.bin to make the following instructions easier.

Option 1 (Intermediate)

#. Download an SSH program if necessary (ssh for linux, putty/winscp for windows, ssh on mac)
#. scp the firmware to /tmp on your SD-WAN router.
#. ssh to your router (as root using the password configured for "admin")
#. run: ``dd if=/tmp/firmware.bin of=/dev/mtdblock4 bs=1M``
#. run: ``dd if=/tmp/firmware.bin of=/dev/mtdblock6 bs=1M``
#. run: ``sync``
#. reboot the device

Option 2 (Advanced)

If you have a USB tty connected, you can do so with uBoot and TFTP via some simple commands.
This requires cracking open the case and connecting your USB serial adapter to access uboot.
Then connect a LAN port to the TFTP server (or the network with the TFTP server)
Assuming the TFTP server is at 192.168.1.20, do the following::
  setenv ipaddr 192.168.1.100
  setenv serverip 192.168.1.20
  setenv firmwareName firmware.bin
  run update_both_images
  boot

Upgrading to a newer version
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Upgrading to a newer version can be done with the following images.
From the Administration UI, upload the image to settings > system > upgrade and then click on Upload.

- `Linksys WRT-1900ACS SD-WAN router sysupgrade firmware <http://download.untangle.com/sdwan/beta/sdwan-wrt1900acs-sysupgrade_v0.1.0beta1-85-g6fe42a850b_20190509T1211.img>`_
- `Linksys WRT-3200ACM SD-WAN router sysupgrade firmware <http://download.untangle.com/sdwan/beta/sdwan-wrt3200acm-sysupgrade_v0.1.0beta1-85-g6fe42a850b_20190509T1211.img>`_
- `Linksys WRT-32X SD-WAN router sysupgrade firmware <http://download.untangle.com/sdwan/beta/sdwan-wrt32x-sysupgrade_v0.1.0beta1-85-g6fe42a850b_20190509T1211.img>`_

To do so on the command line do the following:

#. Download the sysupgrade image, rename it to sysupgrade.img to make the following instructions easier
#. scp sysupgrade.img to your router in /tmp/
#. ssh to your router (as root using the password configured for "admin")
#. run: ``sysupgrade /tmp/sysupgrade.img``
#. Wait. The router will reflash and reboot.

*NOTE:* This process keeps existing settings/configuration.
