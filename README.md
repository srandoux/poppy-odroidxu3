# poppy-odroidxu3
odroid xu3/4 linux ubuntu 15.10 with poppy software preinstalled https://www.poppy-project.org

Ubuntu 15.10 from odroid:
http://odroid.com/dokuwiki/doku.php?id=en:xu3_ubuntu_release_note_20160114

https://poppy-project.gitbooks.io/poppy-docs/content/en/installation-for-advanced-users/index.html

Poppy installed from description here
https://github.com/Inmediats-Poppy/poppy-webapps.git

Poppy humanoid was installed:
https://github.com/poppy-project/poppy_install

from the browser:
http://poppy.local
http://poppy.local/poppy-monitor/
http://poppy.local/snap/snap.html

http://poppy.local/wificonfig/?page=wireless

image was created from a 32GB emmc card:
#make a full image from the card
dd if=/dev/mmcblk1 of=odroid_xu3_ubunt1510_poppy bs=1k

#mount the image on a loop device
losetup -f --show odroid_xu3_ubunt1510_poppy /dev/loop0

#check the image structure
fdisk -l /dev/loop0 

  Disk /dev/loop0: 29,1 GiB, 31268536320 bytes, 61071360 sectors
  Units: sectors of 1 * 512 = 512 bytes
  Sector size (logical/physical): 512 bytes / 512 bytes
  I/O size (minimum/optimal): 512 bytes / 512 bytes
  Disklabel type: dos
  Disk identifier: 0x0008aae4

  Device       Boot  Start      End  Sectors  Size Id Type
  /dev/loop0p1        2048   264191   262144  128M  c W95 FAT32 (LBA)
  /dev/loop0p2      264192 61070336 60806145   29G 83 Linux

#compute the offset of the second partition 
offset for the second partition is 264192 * 512 = 135266304

#mount the loopback device
losetup -f --show -o 135266304 odroid_xu3_ubunt1510_poppy /dev/loop1

#check the filesystem
e2fsck -f -y /dev/loop1
#resize the partition to the maximum size
resize2fs -p /dev/loop1 -M

#change the partion table to a smaller size (5GB to be safe)
parted /dev/loop0 
  GNU Parted 3.2
  Using /dev/loop0
  Welcome to GNU Parted! Type 'help' to view a list of commands.
  (parted) p                                                                
  Model: Loopback device (loopback)
  Disk /dev/loop0: 31,3GB
  Sector size (logical/physical): 512B/512B
  Partition Table: msdos
  Disk Flags: 

  Number  Start   End     Size    Type     File system  Flags
   1      1049kB  135MB   134MB   primary  fat16        lba
   2      135MB   31,3GB  31,1GB  primary  ext2

  resizepart 2                                                     
  End?  [31,3GB]? 5GB                                                       
  Warning: Shrinking a partition can cause data loss, are you sure you want to continue?
  Yes/No? yes   
 p

  Disk /dev/loop0: 29,1 GiB, 31268536320 bytes, 61071360 sectors
  Units: sectors of 1 * 512 = 512 bytes
  Sector size (logical/physical): 512 bytes / 512 bytes
  I/O size (minimum/optimal): 512 bytes / 512 bytes
  Disklabel type: dos
  Disk identifier: 0x0008aae4

  Device       Boot  Start     End Sectors  Size Id Type
  /dev/loop0p1        2048  264191  262144  128M  c W95 FAT32 (LBA)
  /dev/loop0p2      264192 9765625 9501434  4,5G 83 Linux

#cut the image to a new size as show by the parted above
dd if=odroid_xu3_ubunt1510_poppy odroid_xu3_ubunt1510_poppy_reduce count=9501434


 
