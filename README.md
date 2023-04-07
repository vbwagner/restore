Restoration script for full linux backup
========================================

Prepare for backups
-------------------

Before you'll be able to restore your system from backup, you have to do
backups.

1. Get an USB thumb drive or external HDD big enough to hold entire
   content of our notebook's SSD.
2. Install [gparted-live](https://gparted.org/livecd.php) into beginning of this disk
3. Format rest of this disk (gparted would take no more than half of GB
at the beginning) as ext4 system
4. Install [rsnapshot](https://rsnapshot.org/) on your machine and set up it to make backup into
   ```
   /media/${your name}/${label of your USB partition}/${hostname}
   ```
   Or whereever your desktop enviroment mountes removable disks.

   If you have big usb HDD and several notebooks, you can backup all of
   them on one medium.
   You should backup everything including /boot/efi, although you can
   omit mozilla cache and other thinks which are likely to change during
   backup.

5. Do first backup. Do:
   ```
   echo p |fdisk /dev/nvme0n1 > partitions.layout  
   ```

   copy restore script into root of your backup partition.

Do backup regularly
-------------------

Don't forget to insert this USB drive into your notebook and run 
`rsnapshot` with appropriate argument as root on regular basis.

Use alpha/beta/gamma modes of `rsnapshot`, so you'll store daily backups
for last week, weekly ones for last month and several monthly ones.

Since backup to external drive requires human intervention anyway (pick
drive from drawer and plug it into computer, and then return back), I
don't expect myself to do backups every day. Why should I backup a
notebook I haven't switched on since last backup?

This set of scripts includes some scripts which simplify semiregular
backups on removable drive. 

1. **backup** - perl script which checks if there are enough backups on
level alpha done and it's time to do next level beta backup.
It reads `/etc/rsnapshot.conf` for `snapshot_root` and `retain`
parameters. After successful backup it unmounts backup drive.

2. **aftermount** - this script inteneded to be run by automounting
system in your desktop session (tested with spacewm). It runs after
removable disk is mounted and checks if this disk contains
`snapshot_root`. If so, it displays dialog window asking you if you want
to run backup. If yes, it starts **backup** using sudo.

3. **backup.sudo** - place this file into `/etc/sudoers.d` so
**aftermount** would be able to run **backup** as root without password.


Restore old files occasionaly
-----------------------------

Sometimes you'll find out that you have incedently removed or modified
some file. You can than plug your backup drive in and get yesterday's or
week ago copy.

That is why **aftermount** script displays dialog requiring you to
confirm start of backup. You may want to plug backup drive in just to
dig out some files.

When disaster happens
---------------------

When your SSD drive dies, or have been irrepairable  wiped out,
or hit by trojan or cryptolocker so it is easier to wipe out then do 
anything else:

1. Repair the hardware
2. Insert your backup drive in USB port and boot from it. Mount your
   second partition under, say `/mnt`
3. From parted-live GUI create necessary partitions. You can consult
   partitions.layout file which you have created while preparing backup.
   Don't forget create vfat partition for `/boot/efi`, if you are using UEFI
   boot.
4. Mount newly created root partition under, say `/target`
   and if you use separate partitions for `/home`, `/var` or anything else,
   mount them on `/target/home`, `/target/var` etc.
   Don't forget to mount `/target/boot/efi`
5. Cd to `/mnt` and run 
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
