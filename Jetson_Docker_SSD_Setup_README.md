
# Jetson Orin Nano: Semester Startup Instructions (Docker + SSD Setup)

## 🚀 Goal

Prepare Jetson Orin Nano devices to:

- Run course Docker containers.
- Avoid SD card overload by storing heavy Docker data on the SSD.

---

## 🢄 Step 1: Flash JetPack 6.0.2 and Connect via SSH

1. Download JetPack 6.0.2 from NVIDIA and flash it to the SD card using SDK Manager or Balena Etcher.
2. Insert the SD card into the Jetson device and boot.
3. Complete initial setup (create user, connect to Wi-Fi or Ethernet).
4. From a separate computer, use SSH to connect to the Jetson:

```bash
ssh your_username@<jetson_ip_address>
```

> 💡 **It is strongly recommended to use SSH for the rest of the setup** to make copying/pasting commands easier.

---

## 🛠️ Step 2: Mount the SSD

> ⚠️ **Important:** Before Docker can store data on the SSD, the SSD must not only be mounted, but the target directory `/mnt/nvme/docker` must exist before Docker starts. If this directory is missing, Docker may fail or silently use the SD card instead.

```bash
lsblk                          # Look for /dev/nvme0n1p1
sudo mkdir -p /mnt/nvme
sudo mount /dev/nvme0n1p1 /mnt/nvme
```

Make the mount permanent by editing `/etc/fstab`:

```bash
sudo vi /etc/fstab
```

Paste this line:

```
/dev/nvme0n1p1 /mnt/nvme ext4 defaults,nofail,x-systemd.device-timeout=10 0 2
```

Then save and exit (`:wq`).

---

## 📃 Step 3: Move Docker to SSD

```bash
sudo systemctl stop docker
sudo mkdir -p /mnt/nvme/docker
sudo rsync -aP /var/lib/docker /mnt/nvme/docker
sudo mv /var/lib/docker /var/lib/docker.bak
sudo ln -s /mnt/nvme/docker /var/lib/docker
sudo systemctl start docker
```

### ✅ Verify Docker is on the SSD:

```bash
ls -l /var/lib | grep docker
# You should see: docker -> /mnt/nvme/docker

sudo du -sh /mnt/nvme/docker
# Should return several GB if working properly
```

> 📝 `docker.bak` is a backup of the original Docker folder on the SD card. Keep it unless space is needed or everything is confirmed working.

---

## 📊 Step 4: Setup Course Docker Container

```bash
mkdir -p ~/nvdli-data

echo "sudo docker run --runtime nvidia -it --rm --network host \
--volume ~/nvdli-data:/nvdli-nano/data \
--device /dev/video0 \
nvcr.io/nvidia/dli/dli-nano-ai:v2.0.3-r36.3.0" > docker_dli_run.sh

chmod +x docker_dli_run.sh
./docker_dli_run.sh
```

> ⚠️ If you get an `invalid reference format` error, check your quotes and slashes.

---

## 📈 Summary

- SD card = OS only  
- SSD = Docker + heavy data  
- More stable boot and reduced SD corruption risk  

---

### 🛠️ Troubleshooting

- Reboot the Jetson and run:
  ```bash
  ls -l /var/lib | grep docker
  df -h /
  df -h /mnt/nvme
  ```

- If SSD isn’t mounted, rerun:
  ```bash
  sudo mount /mnt/nvme
  ```

---

## 📌 Credits

**Prepared for:** Course Instructors using Jetson Orin Nano with JetPack 6.0.2  
**Maintainer:** [Your Institution/Team Name Here]  
**Compiled by:** Christopher Benjamin  
**With advisory support from:** ChatGPT (OpenAI)
