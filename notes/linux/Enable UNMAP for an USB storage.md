From <https://wiki.archlinux.org/index.php/Solid_state_drive#External_SSD_with_TRIM_support>

Quickly see discard capability with `lsblk -D` (DISK-GRAN shall not be zero).

Install package "`sg3-utils`"" for `sg_readcap` and `sg_vpd` command line tools.
Install package "`util-linux`" for "`/sbin/blkdiscard -v /dev/sdX`" command line tools.

`------ 8< ------`

Several USB-to-SATA bridge chips (like VL715, VL716 etc.) and also USB-to-PCIe 
bridge chips (like the JMicron JMS583 <http://www.jmicron.com/PDF/brief/jms583.pdf> 
used in external NVMe enclosures like IB-1817M-C31 
<https://www.raidsonic.de/en/standards/searchresults.php?we_objectID=5666>) support 
TRIM-like commands that can be sent through the USB Attached SCSI driver (named 
"uas" under Linux).

But the kernel may not automatically detect this capability, and therefore might 
not use it. Assuming your block device in question is /dev/sdX, you can find out 
whether that is the case by using the command

    sg_readcap -l /dev/sdX

If in its output you find a line stating "Logical block provisioning: lbpme=0" then 
you know that the kernel assumes the device does not support "Logical Block 
Provisioning Management" because the (LBPME) bit is not set.
(<https://www.seagate.com/files/staticfiles/support/docs/manual/Interface%20manuals/100293068k.pdf#G4.1427908>)

If this is the case, then you should next find out whether the "Vital Product Data" 
(VPD) page on "Logical Block Provisioning" of your device tells of supported 
mechanisms for unmapping data. You can do this using the command:

    sg_vpd -a /dev/sdX

Look for lines in the output that look like this:

    Unmap command supported (LBPU): 1
    Write same (16) with unmap bit supported (LBPWS): 0
    Write same (10) with unmap bit supported (LBPWS10): 0

This example would tell you the device supports the "UNMAP" command.

Have a look at the output of

    cat /sys/block/sdX/device/scsi_disk/*/provisioning_mode

If the kernel did not detect the capability of your device to unmap data, then this 
will likely return "full". Apart from "full", the kernel SCSI storage driver 
currently knows the following values for provisioning_mode:

    unmap
    writesame_16
    writesame_10
    writesame_zero
    disabled

For the example above, you could now write "unmap" to "provisioning_mode" to ask 
the kernel to use that:

    echo "unmap" >/sys/block/sdX/device/scsi_disk/*/provisioning_mode

`------ >8 ------`

Make change persistent with:

File `/etc/udev/rules.d/50-usb-ssd-trim.rules`:

    # ASMedia Technology Inc. – ASM1051E SATA 6Gb/s bridge, ASM1053E SATA 6Gb/s bridge, ASM1153 SATA 3Gb/s bridge, ASM1153E SATA 6Gb/s bridge
    ACTION=="add|change", ATTRS{idVendor}=="174c", ATTRS{idProduct}=="55aa", SUBSYSTEM=="scsi_disk", ATTR{provisioning_mode}="unmap"

    # ASMedia Technology Inc. ASM236X series – NVMe enclosure
    ACTION=="add|change", ATTRS{idVendor}=="174c", ATTRS{idProduct}=="2362", SUBSYSTEM=="scsi_disk", ATTR{provisioning_mode}="unmap"

    # Samsung Electronics Co., Ltd Portable SSD T5
    ACTION=="add|change", ATTRS{idVendor}=="04e8", ATTRS{idProduct}=="61f5", SUBSYSTEM=="scsi_disk", ATTR{provisioning_mode}="unmap"

Apply new rules:

    sudo udevadm control --reload

Unplug/replug device to change device provisioning mode.

