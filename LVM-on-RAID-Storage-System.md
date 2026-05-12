# RAID + LVM Storage System Setup (Linux)

## Objective

* Configure RAID for fault tolerance and performance
* Create LVM on RAID device for flexible storage management
* Enable scalable logical volumes and easy storage expansion

---

# 1. Install Required Packages

### RHEL/CentOS

```bash id="zsl4da"
sudo yum install mdadm lvm2 -y
```

### Ubuntu/Debian

```bash id="yht6v4"
sudo apt install mdadm lvm2 -y
```

---

# 2. Verify Available Disks

Check attached disks:

```bash id="3pl0rw"
lsblk
```

Example disks used:

* /dev/sdb
* /dev/sdc
* /dev/sdd

---

# 3. Create RAID Partition on Disks

Use `fdisk` for each disk:

```bash id="dl4ckm"
sudo fdisk /dev/sdb
sudo fdisk /dev/sdc
sudo fdisk /dev/sdd
```

Inside fdisk:

```bash id="h6ztz8"
n   # new partition
p   # primary
1   # partition number
t   # change type
fd  # Linux RAID autodetect
w   # write changes
```

---

# 4. Create RAID Array

### Example: RAID 5

```bash id="5mnx1w"
sudo mdadm --create --verbose /dev/md0 --level=5 --raid-devices=3 /dev/sdb1 /dev/sdc1 /dev/sdd1
```

Verify RAID:

```bash id="i0kfx0"
cat /proc/mdstat
```

Detailed RAID info:

```bash id="rxvvfg"
sudo mdadm --detail /dev/md0
```

---

# 5. Save RAID Configuration

```bash id="4sz4mb"
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm.conf
```

Ubuntu:

```bash id="w3z6zv"
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
```

---

# 6. Create Physical Volume (PV)

```bash id="6m8o0h"
sudo pvcreate /dev/md0
```

Verify:

```bash id="2bz6rv"
sudo pvs
```

---

# 7. Create Volume Group (VG)

```bash id="0t95b6"
sudo vgcreate vg_data /dev/md0
```

Verify:

```bash id="cbzq8k"
sudo vgs
```

---

# 8. Create Logical Volume (LV)

Create 5GB LV:

```bash id="ak5b7m"
sudo lvcreate -L 5G -n lv_storage vg_data
```

Verify:

```bash id="od3p2w"
sudo lvs
```

---

# 9. Create File System

```bash id="zyz6l8"
sudo mkfs.ext4 /dev/vg_data/lv_storage
```

---

# 10. Create Mount Point

```bash id="5vkckq"
sudo mkdir /storage
```

---

# 11. Mount Logical Volume

```bash id="r2b6cu"
sudo mount /dev/vg_data/lv_storage /storage
```

Verify:

```bash id="26q89t"
df -h
```

---

# 12. Permanent Mount Configuration

Get UUID:

```bash id="i7d44d"
sudo blkid
```

Edit fstab:

```bash id="4cgsr8"
sudo vi /etc/fstab
```

Add:

```bash id="dd7psu"
/dev/vg_data/lv_storage /storage ext4 defaults 0 0
```

Reload mounts:

```bash id="y95a9j"
sudo mount -a
```

---

# LVM Scaling (Storage Expansion)

## 13. Extend Logical Volume

Increase LV by 2GB:

```bash id="v7n3cu"
sudo lvextend -L +2G /dev/vg_data/lv_storage
```

Resize filesystem:

```bash id="kpq1nz"
sudo resize2fs /dev/vg_data/lv_storage
```

Verify:

```bash id="suxn62"
df -h
```

---

# RAID Monitoring

Check RAID health:

```bash id="7lz0to"
cat /proc/mdstat
```

Monitor details:

```bash id="r0dmyt"
sudo mdadm --detail /dev/md0
```

---

# Simulate Disk Failure (Testing)

Fail one disk:

```bash id="txxjzd"
sudo mdadm /dev/md0 --fail /dev/sdb1
```

Remove failed disk:

```bash id="66rzcr"
sudo mdadm /dev/md0 --remove /dev/sdb1
```

Add replacement disk:

```bash id="i9j3yz"
sudo mdadm /dev/md0 --add /dev/sdb1
```

