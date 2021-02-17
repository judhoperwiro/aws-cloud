## Extend Disk AWS EC2 path /

** Example: Extend the file system of NVMe EBS volumes
For this example, suppose that you have an instance built on the Nitro System, such as an M5 instance. You resized the boot volume from 8 GB to 16 GB and an additional volume from 8 GB to 30 GB. Use the following procedure to extend the file system of the resized volumes.

To extend the file system of NVMe EBS volumes

- Connect to your instance.

To verify the file system for each volume, use the df -hT command.
```
[ec2-user ~]$ df -hT
```
The following is example output for an instance that has a boot volume with an XFS file system and an additional volume with an XFS file system. The naming convention /dev/nvme[0-26]n1 indicates that the volumes are exposed as NVMe block devices.
```
[ec2-user ~]$ df -hT
Filesystem      Type  Size  Used Avail Use% Mounted on
/dev/nvme0n1p1  xfs   8.0G  1.6G  6.5G  20% /
/dev/nvme1n1    xfs   8.0G   33M  8.0G   1% /data
```
...

To check whether the volume has a partition that must be extended, use the lsblk command to display information about the NVMe block devices attached to your instance.
```
[ec2-user ~]$ lsblk
NAME          MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
nvme1n1       259:0    0  30G  0 disk /data
nvme0n1       259:1    0  16G  0 disk
└─nvme0n1p1   259:2    0   8G  0 part /
└─nvme0n1p128 259:3    0   1M  0 part
```
This example output shows the following:

The root volume, /dev/nvme0n1, has a partition, /dev/nvme0n1p1. While the size of the root volume reflects the new size, 16 GB, the size of the partition reflects the original size, 8 GB, and must be extended before you can extend the file system.

The volume /dev/nvme1n1 has no partitions. The size of the volume reflects the new size, 30 GB.

For volumes that have a partition, such as the root volume shown in the previous step, use the growpart command to extend the partition. Notice that there is a space between the device name and the partition number.

```
[ec2-user ~]$ sudo growpart /dev/nvme0n1 1
(Optional) To verify that the partition reflects the increased volume size, use the lsblk command again.

[ec2-user ~]$ lsblk
NAME          MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
nvme1n1       259:0    0  30G  0 disk /data
nvme0n1       259:1    0  16G  0 disk
└─nvme0n1p1   259:2    0  16G  0 part /
└─nvme0n1p128 259:3    0   1M  0 part
To verify the size of the file system for each volume, use the df -h command. In this example output, both file systems reflect the original volume size, 8 GB.

[ec2-user ~]$ df -h
Filesystem       Size  Used Avail Use% Mounted on
/dev/nvme0n1p1   8.0G  1.6G  6.5G  20% /
/dev/nvme1n1     8.0G   33M  8.0G   1% /data
```

...

To extend the file system on each volume, use the correct command for your file system, as follows:

[XFS file system] To extend the file system on each volume, use the xfs_growfs command. In this example, / and /data are the volume mount points shown in the output for df -h.

```
[ec2-user ~]$ sudo xfs_growfs -d /
[ec2-user ~]$ sudo xfs_growfs -d /data
If the XFS tools are not already installed, you can install them as follows.

[ec2-user ~]$ sudo yum install xfsprogs
[ext4 file system] To extend the file system on each volume, use the resize2fs command.

[ec2-user ~]$ sudo resize2fs /dev/nvme0n1p1
[ec2-user ~]$ sudo resize2fs /dev/nvme1n1
[Other file system] To extend the file system on each volume, refer to the documentation for your file system for instructions.
```
(Optional) To verify that each file system reflects the increased volume size, use the df -h command again.
```
[ec2-user ~]$ df -h
Filesystem       Size  Used Avail Use% Mounted on
/dev/nvme0n1p1    16G  1.6G   15G  10% /
/dev/nvme1n1      30G   33M   30G   1% /data
```
...
