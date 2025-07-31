# Adding a drive to Logical Volume Management

1.  Identify Available Disks

`lsblk`

- Note path to drive you want to add from the lsblk output

2.  Create a new partition on the drive:

`sudo fdisk <DRIVE_PATH>`

**Inside the `fdisk` prompt, type the following commands one by one, pressing Enter after each:**

- `n` (for new partition)
- `p` (for primary partition)
- `<PARTION_NUMBER>` (for partition number 1 - this will create `/dev/<DRIVE_PATH>1`)
- (Press `Enter` for default first sector, which will use the beginning of the disk)
- (Press `Enter` for default last sector, which will use the entire disk)
- `t` (for changing partition type)
- `8e` (the hex code for Linux LVM)
- `p` (to print the partition table and verify that `sdb1` is created with type `Linux LVM`)
- `w` (to write changes to disk and exit `fdisk`)  ## THIS STEP WILL COMMIT CHANGES AND ERASE DATA FROM YOUR DISK!

3.  Initialize the new partition as a Physical Volume (PV)

`sudo pvcreate <DRIVE_PATH><PARTITION_NUMBER>` # PARTITION_NUMBER comes from the third step when creating a partition
The Output should be: `  Physical volume "/dev/<DRIVE_PATH><PARTITION_NUMBER>" successfully created.`

4.  Find the name of the existing Volume Group:

`sudo vgdisplay`

The first line of output should be `VG NAME      <VOLUME_GROUP_NAME>`

5.  Extend the Volume Group to the new drive:

`sudo vgextend <VOLUME_GROUP_NAME> /<DRIVE_PATH><PARTITION_NUMBER>`
The Output should be: `  Volume group "<VOLUME_GROUP_NAME" successfully extended`

6.  Find Logical Name, and Extend the Logical Volume Group to the new drive:

To find the Logical Volume Name:
`sudo lvdisplay`
The first line should read `LV PATH      /dev/<VOLUME_GROUP_NAME>/<LOGICAL_VOLUME_NAME>`
The second and third lines of the output will have the Logical Volume Name and Volume Group Names

`sudo lvextend -l +100%FREE /dev/<VOLUME_GROUP_NAME>/<LOGICAL_VOLUME_NAME>`
The output should be:
```
  Size of logical volume <VOLUME_GROUP_NAME>/<LOGICAL_VOLUME_NAME> changed from <ORIGINAL_LV_SIZE> (<ORIGINAL_EXTENTS> extents) to <<NEW_LV_SIZE> GiB (<UPDATED_EXTENTS> extents).
  Logical volume <VOLUME_GROUP_NAME>/<LOGICAL_VOLUME_NAME> successfully resized.
```

7.  Resize the File System, which tells the file system itself to recognize and use the newly expanded space on the Logical Volume:
Confirm root file system type:  `df -T /`
Example output:
```
Filesystem                        Type 1K-blocks    Used Available Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv ext4  18631360 4772604  12886980  28% /
```

If you filesystem type is ext4 (most common):
`sudo resize2fs /dev/<VOLUME_GROUP_NAME>/<LOGICAL_VOLUME_NAME>
Example output:
```
resize2fs 1.47.0 (5-Feb-2023)
Filesystem at /dev/ubuntu-vg/ubuntu-lv is mounted on /; on-line resizing required
old_desc_blocks = 3, new_desc_blocks = 15
The filesystem on /dev/ubuntu-vg/ubuntu-lv is now 30990336 (4k) blocks long.
```

8.  Verify the operation has worked:
`df -h'
Sample Output:
```
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              197M  4.2M  193M   3% /run
/dev/mapper/ubuntu--vg-ubuntu--lv  117G  4.6G  107G   5% /
tmpfs                              984M     0  984M   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
/dev/sda2                          1.8G  100M  1.6G   7% /boot
tmpfs                              197M   12K  197M   1% /run/user/1000
```




