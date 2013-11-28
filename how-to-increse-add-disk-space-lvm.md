how-to-increse-add-disk-space-lvm.md

This post contains an instructuion how to increase disk space on a linux server on VMWare VCenter.  
You don't need to shutdown the server. It can be performed live!

1. go to add a new disk on VMWare VSphere client with thin provisioning and using SCSI (0:1) node
2. go to the machine's console

```sh

$ cat /proc/partitions           ;# to see the current partition list
$ sudo apt-get install scsitools ;# install scsitools to scan the new disk
$ dpkg -L scsitools              ;# to see where tool's path
$ sudo /sbin/rescan-scsi-bus     ;# scan new disk
	/sbin/rescan-scsi-bus: line 592: [: 1.03: integer expression expected
	Host adapter 0 (ata_piix) found.
	Host adapter 1 (ata_piix) found.
	Host adapter 2 (mptspi) found.
	Scanning SCSI subsystem for new devices
	Scanning host 0 for  SCSI target IDs  0 1 2 3 4 5 6 7, all LUNs
	Scanning host 1 for  SCSI target IDs  0 1 2 3 4 5 6 7, all LUNs
	Scanning for device 1 0 0 0 ... 
	OLD: Host: scsi1 Channel: 00 Id: 00 Lun: 00
	      Vendor: NECVMWar Model: VMware IDE CDR10 Rev: 1.00
	      Type:   CD-ROM                           ANSI SCSI revision: 05
	Scanning host 2 for  SCSI target IDs  0 1 2 3 4 5 6 7, all LUNs
	Scanning for device 2 0 0 0 ... 
	OLD: Host: scsi2 Channel: 00 Id: 00 Lun: 00
	      Vendor: VMware   Model: Virtual disk     Rev: 1.0 
	      Type:   Direct-Access                    ANSI SCSI revision: 02
	Scanning for device 2 0 1 0 ...            
	NEW: Host: scsi2 Channel: 00 Id: 01 Lun: 00     
	      Vendor: VMware   Model: Virtual disk     Rev: 1.0 
	      Type:   Direct-Access                    ANSI SCSI revision: 02
	1 new device(s) found.                     
	0 device(s) removed.  

$ cat /proc/partitions          ;# sdb is the new disk listed here
	major minor  #blocks  name

	  11        0      65882 sr0
	   8        0   16777216 sda
	   8        1     248832 sda1
	   8        2          1 sda2
	   8        5   16525312 sda5
	 252        0   12308480 dm-0
	 252        1    4190208 dm-1
	   8       16   52428800 sdb


$ fdisk -l
	# Disk /dev/sdb: 21.5 GB, 21474836480 bytes
	# 255 heads, 63 sectors/track, 2610 cylinders
	# Units = cylinders of 16065 * 512 = 8225280 bytes
	# Sector size (logical/physical): 512 bytes / 512 bytes
	# I/O size (minimum/optimal): 512 bytes / 512 bytes
	# Disk identifier: 0x00000000                          <<<<< notice! there's no id.

```

###### perform fdisk to add a new disk
If the newly added disk is not there, just reboot.

```sh
$ fdisk /dev/sdb
	# Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
	# Building a new DOS disklabel with disk identifier 0x20a975fc.
	# Changes will remain in memory only, until you decide to write them.
	# After that, of course, the previous content won't be recoverable.

	# Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

	# WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
	#          switch off the mode (command 'c') and change display units to
	#          sectors (command 'u').

	Command (m for help): m
	# Command action
	#    a   toggle a bootable flag
	#    b   edit bsd disklabel
	#    c   toggle the dos compatibility flag
	#    d   delete a partition
	#    l   list known partition types
	#    m   print this menu
	#    n   add a new partition
	#    o   create a new empty DOS partition table
	#    p   print the partition table
	#    q   quit without saving changes
	#    s   create a new empty Sun disklabel
	#    t   change a partition's system id
	#    u   change display/entry units
	#    v   verify the partition table
	#    w   write table to disk and exit
	#    x   extra functionality (experts only)

	Command (m for help): n
	# Command action
	#    e   extended
	#    p   primary partition (1-4)
	p
	Partition number (1-4): 1
	# First cylinder (1-2610, default 1):
	# Using default value 1
	# Last cylinder, +cylinders or +size{K,M,G} (1-2610, default 2610):
	# Using default value 2610

	Command (m for help): t
	Selected partition 1
	Hex code (type L to list codes): 8e
	# Changed system type of partition 1 to 8e (Linux LVM)

	Command (m for help): p

	# Disk /dev/sdb: 21.5 GB, 21474836480 bytes
	# 255 heads, 63 sectors/track, 2610 cylinders
	# Units = cylinders of 16065 * 512 = 8225280 bytes
	# Sector size (logical/physical): 512 bytes / 512 bytes
	# I/O size (minimum/optimal): 512 bytes / 512 bytes
	# Disk identifier: 0x20a975fc                                <<<<< notice! there's an id.

	#    Device Boot      Start         End      Blocks   Id  System
	# /dev/sdb1               1        2610    20964793+  8e  Linux LVM

	Command (m for help): w
	# The partition table has been altered!

	# Calling ioctl() to re-read partition table.
	# Syncing disks.
```

###### Create a PV
Tip: `man lvm` to see the definition of each commands

```sh
$ pvcreate /dev/sdb1
	  # Writing physical volume data to disk "/dev/sdb1"
	  # Physical volume "/dev/sdb1" successfully created

      ....Verify if the PV is created.

$ pvdisplay
      # ... should see sdb1

$ pvscan
	  # PV /dev/sda2   VG VolGroup        lvm2 [7.51 GiB / 0    free]
	  # PV /dev/sdb1                      lvm2 [19.99 GiB]
	  # Total: 2 [27.50 GiB] / in use: 1 [7.51 GiB] / in no VG: 1 [19.99 GiB]
```

###### Add a PV to a VG

```sh
$ vgdisplay
  # ... should see VG Name [something]

$ vgextend VolGroup /dev/sdb1
      # Volume group "VolGroup" successfully extended

$ vgdisplay
  # --- Volume group ---
  # VG Name               VolGroup
  # System ID
  # Format                lvm2
  # Metadata Areas        2
  # Metadata Sequence No  4
  # VG Access             read/write
  # VG Status             resizable
  # MAX LV                0
  # Cur LV                2
  # Open LV               2
  # Max PV                0
  # Cur PV                2
  # Act PV                2
  # VG Size               27.50 GiB
  # PE Size               4.00 MiB
  # Total PE              7040
  # Alloc PE / Size       1922 / 7.51 GiB
  # Free  PE / Size       5118 / 19.99 GiB
  # VG UUID               DO3DUx-rBlw-Qi8d-bm0A-ddkx-rkmO-qkFQVe

$ lvdisplay
  # --- Logical volume ---
  # LV Path                /dev/VolGroup/lv_root
  # LV Name                lv_root
  # VG Name                VolGroup
  # LV UUID                jU7YtI-91hM-AxqT-yxqq-fbx2-j1HT-Emgwn1
  # LV Write Access        read/write
  # LV Creation host, time localhost.localdomain, 2012-11-21 23:35:10 +0700
  # LV Status              available
  # # open                 1
  # LV Size                6.54 GiB
  # Current LE             1674
  # Segments               1
  # Allocation             inherit
  # Read ahead sectors     auto
  # - currently set to     256
  # Block device           253:0

  # --- Logical volume ---
  # LV Path                /dev/VolGroup/lv_swap
  # LV Name                lv_swap
  # VG Name                VolGroup
  # LV UUID                xkl24z-aJeC-uaoS-7fLt-55XI-HvKS-Ij6y7G
  # LV Write Access        read/write
  # LV Creation host, time localhost.localdomain, 2012-11-21 23:35:21 +0700
  # LV Status              available
  # # open                 1
  # LV Size                992.00 MiB
  # Current LE             248
  # Segments               1
  # Allocation             inherit
  # Read ahead sectors     auto
  # - currently set to     256
  # Block device           253:1
```
Notice LV Path

$ lvextend [lv path] /dev/sdb1        ;# tries to extend the size of that logical volume by 
                                      ;# the amount of  free  space  on physical volume /dev/sdb1.  
                                      ;# This is equivalent to specifying "-l +100%PVS" on the command line.
or

$ lvextend -L +19G /dev/VolGroup/lv_root

  # Extending logical volume lv_root to 25.54 GiB
  # Logical volume lv_root successfully resized

$ resize2fs /dev/VolGroup/lv_root
	# resize2fs 1.41.12 (17-May-2010)
	# Filesystem at /dev/VolGroup/lv_root is mounted on /; on-line resizing required
	# old desc_blocks = 1, new_desc_blocks = 2
	# Performing an on-line resize of /dev/VolGroup/lv_root to 6694912 (4k) blocks.
	# The filesystem on /dev/VolGroup/lv_root is now 6694912 blocks long.

$ df -h
	# Filesystem            Size  Used Avail Use% Mounted on
	# /dev/mapper/VolGroup-lv_root
	#                        26G  5.6G   19G  24% /
	# tmpfs                 939M     0  939M   0% /dev/shm
	# /dev/sda1             485M   46M  414M  10% /boot

```

