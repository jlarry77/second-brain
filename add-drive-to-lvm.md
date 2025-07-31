# Expanding LVM Storage in Linux

This guide walks you through the process of adding a new drive to an existing LVM (Logical Volume Manager) setup on a Linux system. You'll learn how to identify the disk, partition it, create a physical volume, extend your volume group and logical volume, and finally expand the filesystem.

---

## ⚠️ Prerequisites & Warnings

> **⚠️ WARNING:** These steps will overwrite any existing data on the selected drive. Make sure you have backed up all important data before proceeding.

> This guide assumes you're familiar with basic Linux commands and have `sudo` privileges.

---

## Terminology

- `<DRIVE_PATH>`: Disk identifier, e.g. `sdb`
- `<PARTITION_NUMBER>`: Usually `1`, so the partition becomes `/dev/sdb1`
- `<VOLUME_GROUP_NAME>`: Name of your volume group, e.g. `ubuntu-vg`
- `<LOGICAL_VOLUME_NAME>`: Name of your logical volume, e.g. `ubuntu-lv`

---

## Step-by-Step Instructions

### 1. Identify Available Disks

```bash
lsblk
```

> Note the name of the disk you want to add (e.g. `sdb`).

---

### 2. Create a New Partition on the Disk

```bash
sudo fdisk /dev/<DRIVE_PATH>
```

Inside the `fdisk` prompt:

```
n     # New partition
p     # Primary partition
1     # Partition number
<Enter> # Default first sector
<Enter> # Default last sector (use whole disk)
t     # Change partition type
8e    # Linux LVM
p     # Print partition table to verify
w     # Write changes and exit
```

> ⚠️ `w` will write changes to disk — this will destroy existing data!

---

### 3. Create a Physical Volume (PV)

```bash
sudo pvcreate /dev/<DRIVE_PATH><PARTITION_NUMBER>
```

**Example:**

```bash
sudo pvcreate /dev/sdb1
```

Check it was created:

```bash
sudo pvs
```

---

### 4. Find Existing Volume Group Name

```bash
sudo vgdisplay
```

Look for the line:

```text
VG Name     ubuntu-vg
```

---

### 5. Extend the Volume Group

```bash
sudo vgextend <VOLUME_GROUP_NAME> /dev/<DRIVE_PATH><PARTITION_NUMBER>
```

**Example:**

```bash
sudo vgextend ubuntu-vg /dev/sdb1
```

Verify:

```bash
sudo vgdisplay
```

---

### 6. Find Logical Volume and Extend It

Find the LV path:

```bash
sudo lvdisplay
```

Look for:

```text
LV Path      /dev/ubuntu-vg/ubuntu-lv
```

Now extend the logical volume to use all available free space:

```bash
sudo lvextend -l +100%FREE /dev/<VOLUME_GROUP_NAME>/<LOGICAL_VOLUME_NAME>
```

**Example:**

```bash
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
```

Confirm:

```bash
sudo lvdisplay
```

---

### 7. Resize the Filesystem

Check the filesystem type:

```bash
df -T /
```

If the type is `ext4`, resize with:

```bash
sudo resize2fs /dev/<VOLUME_GROUP_NAME>/<LOGICAL_VOLUME_NAME>
```

**Example:**

```bash
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
```

You should see output like:

```
Filesystem at /dev/ubuntu-vg/ubuntu-lv is mounted on /; on-line resizing required
...
The filesystem ... is now <expanded size> blocks long.
```

---

### 8. Verify New Disk Space

```bash
df -h
```

**Sample Output:**

```
Filesystem                         Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv  117G  4.6G  107G   5% /
```

You’re done! Your LVM-backed root filesystem has been successfully extended.

---

## TL;DR Script Summary

```bash
# Example values: sdb, ubuntu-vg, ubuntu-lv
sudo fdisk /dev/sdb          # Create /dev/sdb1 of type 8e
sudo pvcreate /dev/sdb1
sudo vgextend ubuntu-vg /dev/sdb1
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
df -h                        # Confirm space increase
```

---

## Resources

- [man lvm](https://man7.org/linux/man-pages/man8/lvm.8.html)
- [Red Hat LVM Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/logical_volume_manager_administration/)
- [Ubuntu LVM Help](https://help.ubuntu.com/community/LVM)

---

## Suggestions or Improvements?

Feel free to open a pull request or issue if you have suggestions or corrections.

---


