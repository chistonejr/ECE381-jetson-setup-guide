# ⚙️ SSD Partitioning and Formatting Instructions

If you encounter an error like:

```
mount: /mnt/nvme: special device /dev/nvme0n1p1 does not exist.
```
it means the SSD was **not partitioned yet**.

Follow these quick steps to create a partition manually:

**1. Open `fdisk` to create a new partition**
```
sudo fdisk /dev/nvme0n1
```
- Type `n` → Create new partition
- Press `Enter` to accept Partition number (default 1)
- Press `Enter` to accept First sector (default)
- Press `Enter` to accept Last sector (use full disk)
- Type `w` → Write changes and exit


**2. Reboot the device** 
```
sudo reboot
```
This ensures the new partition is recognized by the operating system.

**3. Format the new partition** 
```
sudo mkfs.ext4 /dev/nvme0n1p1
```
**4. Proceed with regular steps**

[Click to return](Jetson_Docker_SSD_Setup_README.md)
