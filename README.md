# Projects



Steps in Configuring RAID 0 for SSD AWS

1. Unmount all mounted SSD devices (Ex.: xvdb)
   umount -f /dev/xvdb
   uncomment /etc/fstab
2. install lvm2 and mdadm
    DEBIAN_FRONTEND=noninteractive apt-get -y install mdadm lvm2  
3. Add support to raid0
    modprobe raid0
4. manually load the dm-mod module
   modprobe dm-mod    
5. Check mdstat
    cat /proc/mdstat
6. For every SSD device prepare raid partition:
     fdisk /dev/xvdb ( follow on screen procedure )
7. configure RAID 0 array via mdadm
     sudo mdadm --create --verbose /dev/md0 --level=0 --raid-devices=2 /dev/xvdb /dev/xvdc
8. create physical volume from the RAID 0 array
     pvcreate /dev/md0
9. create file systems and mount them  
     mkfs.ext4 -L /dev/md0
10. Create a mount point for your RAID array.
     sudo mkdir -p /mnt
11. mount the RAID device on the mount point that you created
     mount /dev/md0 /mnt
12. setup fstab to mount the volumes when server restart
     vi /etc/fstab, and copy the following
     LABEL=cloudimg-rootfs   /        ext4   defaults        0 0
     /dev/md0        /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2 
           
     
verify your setup 
    mount -a --- if no error mount is functioning well
    cat /proc/mdstat  --- list active RAID
    df -h  -- will list all your volumes
    lsblk   -- list all logical volumes
    fdisk -l --list all mount  
