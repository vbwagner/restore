Restoration script for full linux backup
========================================

Prepare for backups
-------------------

Before you'll be able to restore your system from backup, you have to do
backups.

1. Get an USB thumb drive or external HDD big enough to hold entire
   content of our notebook's SSD.
2. Install gparted-live into beginning of this disk
3. Format rest of this disk (gparted would take no more than half of GB
at the beginning) as ext4 system
4. Install rsnapshot on your machine and set up it to make backup into
   ```
   /media/${your name}/${label of your USB partition}/${hostname}
   ```
   If you have big usb HDD and several notebooks, you can backup all of
   them on one medium.
   You should backup everything including /boot/efi, although you can
   omit mozilla cache and other thinks which are likely to change during
   backup.

5. Do first backup. Do:
   ```
   echo p |fdisk /dev/nvme0n1 > partitions.layout  

   copy restore script into root of your backup partition.

Do backup regularly
-------------------

Don't forget to insert this USB drive into your notebook and run 
rsnapshot with appropraite argument as root on regular basis.

Use alpha/beta/gamma modes of rsnapshots, so you'll store daily backups
for last week, weekly ones for last month and several monthly ones.

Restore old files occasionaly
-----------------------------

Sometimes you'll find out that you have incedently removed or modified
some file. You can than plug your backup drive in and get yesterday's or
week ago copy.

When disaster happens
---------------------

When your SSD drive dies, or have been irrepairable  wiped out,
or hit by trojan or cryptolocker so it is easier to wipe out then do 
anythin else:

1. Repair the hardware
2. Insert your backup drive in USB port and boot from it. Mount your
   second partition under, say /mnt
3. From parted-live GUI create neccessary partitions. You can consult
   partitions.layout file which you have created while preparing backup.
   Don't forget create vfat partion for /boot/efi, if you are using uefi
   boot.
4. Mount newly created root partition under, say /target
   and if you unse separate partions for /home, /var or anything else,
   mount them on /target/home, /target/var etc.
   Don't forget to mount /target/boot/ef
5. Cd to /mnt and run 
    ```
    restore /target
	```
    IF you keep backup for several
    compiters on one disk or want to restore not the latest daily backup,
    specify backup directory
    
	```
    restore mynote/beta.1 /target
	```
6. Reboot and enjoy.

Other uses
----------

You can use this restoration procedure when you are upgrading your main
SSD - no need to search for USB SATA or USB NVME controller to copy data
from old disk. That is why we don't create partitions from script. User
may want to rearrange partition layout or just restore system on bigger
drive.


