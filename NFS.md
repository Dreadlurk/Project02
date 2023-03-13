
-----Part 1-----------------------------------------------------------------------

1.) sudo mdadm --create --verbose /dev/md0 --level=0 --raid-devices=2 /dev/sda /dev/sdb               
    // create RAID 0 array with mdadm and name the device as md0
    cat /proc/mdstat                                     
    // confirm RAID array was created

   
    
2.) sudo mkfs.ext4 -F /dev/md0                
    //  make the file system as ext4
    sudo mkdir -p /mnt/md0
    // make an directory to point the mount
    sudo mount /dev/md0 /mnt/md0
    // mount the filesystem
    sudo cmod 776 -R /mnt/md0
    // change permisson of user to rwx group rwx and other to rw
    
    
3.) sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
    // append the mdadm.conf file to reassemble at boot
    sudo update-initramfs -u
    // this allows the array to be available at boot
    echo '/dev/md0 /mnt/md0 ext4 defaults,nofail,discard 0 0' | sudo tee -a /etc/fstab
    // add mount options to /etc/fstab for auto mounting at boot
    
4.)   sudo apt update
      sudo apt install nfs-kernel-server
      
5.)  /var/nfs/general    44.198.57.79(rw,sync,no_subtree_check)
     // read write- to access and change files, sync for writing changes before replying, thus reflecting the same changes. no_subtree_check - disables "sub tree checking" meaning that the host checks if the file is still avaiable in the exported tree
     /home              44.198.57.79(rw,sync,no_root_squash,no_subtree_check)
     // no_root_squash is for security purposes such as preventing the client using the root of the host.
     
     
6.)  sudo exportfs -r
     sudo systemctl restart nfs-kernel-server
![exportfs files being shared project 0211111](https://user-images.githubusercontent.com/122290600/224599267-af50414c-9796-4015-a63c-7e8cae7ea64c.PNG)

![project 02 export](https://user-images.githubusercontent.com/122290600/224599281-6eee53c9-c195-4905-af5f-c94812f9e32d.PNG)



------Part 2-----------------------------------------------------------------------

Firewall rules to allow nfs from home,WSU and client.
![firewall rules project 02](https://user-images.githubusercontent.com/122290600/224599159-30c3914e-38d3-4e07-9c02-c456f88e01db.PNG)


------Part 3-----------------------------------------------------------------------

Created an instance to run as client and installed nfs with // sudo apt install nfs-common

made directory for NFS share with // sudo mkdir -p /nfs/general

![project 02 nfsstat](https://user-images.githubusercontent.com/122290600/224599055-ad30c3d2-5370-45ae-a669-c3ecc709f977.PNG)
File creation on client side with file shown on host.

![project 02 proof file creation](https://user-images.githubusercontent.com/122290600/224598854-194cf34d-0589-47ec-bb61-9e1e4aec74b8.PNG)

to unmount the nfs share you would use // sudo umount /nfs/general
then check with // df -h



