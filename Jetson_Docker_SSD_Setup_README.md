
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
```````````````````````````````````````````````````````````````````````````````````````````````````
 Username: ece381-<Jetson#>
 Password: machinelearning<Jetson#>
```````````````````````````````````````````````````````````````````````````````````````````````````
4. Plug in USB A - USB C (USB C to Jetson and USB A to Computer)
> ⚠️ "USB A - USB C" wire **MUST** be connected before SSH or else an error will show.
5. Use SSH to connect to the Jetson:

```````````````````````````````````````````````````````````````````````````````````````````````````
ssh ece381-<Jetson #>@192.168.55.1
```````````````````````````````````````````````````````````````````````````````````````````````````

> 💡 **It is strongly recommended to use SSH for the rest of the setup** to make copying/pasting commands easier.

---

## 🛠️ Step 2: Mount the SSD

> ⚠️ **Important:** Before Docker can store data on the SSD, the SSD must not only be mounted, but the target directory `/mnt/nvme/docker` must exist before Docker starts. If this directory is missing, Docker may fail or silently use the SD card instead.

```bash
lsblk                          # Look for /dev/nvme0n1p1

You should see this:

![Whatyoushouldsee](Whatyoushouldsee.png)


sudo mkdir -p /mnt/nvme        # Create a mount point
sudo mount /dev/nvme0n1p1 /mnt/nvme      # Mount the SSD

```

Make the mount permanent by editing `/etc/fstab`:

```bash
sudo vi /etc/fstab
```

Press (`i`) to enter insert mode, paste the line below, then press (`Esc`), type (`:wq`), and hit (`Enter`) to save and exit.

```
/dev/nvme0n1p1 /mnt/nvme ext4 defaults,nofail,x-systemd.device-timeout=10 0 2
```
This ensures the SSD automatically mounts on boot and prevents accidental reversion to the SD card.

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
# Should return a number in KB size indicating that SSD is empty and ready for use.
```

> 📝 `docker.bak` is a backup of the original Docker folder on the SD card that you may also notice. Keep it unless space is needed or everything is confirmed working.

---

## 📊 Step 4: Setup Course Docker Container
💡 **Important:** Make sure Steps 2 and 3 are fully complete before starting this step. Previously, setting up Docker containers before ensuring the SSD was properly mounted and the Docker directory created resulted in data loss after reboot. Following Steps 2 and 3 first ensures all course installations persist to the SSD as intended.

**1.** Create the course data directory:
```bash
mkdir -p ~/nvdli-data
```
**2.** Create the container launcher script. **It is recommended to copy and paste the code below rather than typing it out manually to avoid formatting errors.**
```bash
echo "sudo docker run --runtime nvidia -it --rm --network host \
--volume ~/nvdli-data:/nvdli-nano/data \
--device /dev/video0 \
nvcr.io/nvidia/dli/dli-nano-ai:v2.0.3-r36.3.0" > docker_dli_run.sh
```
>⚠️ If typing manually, ensure no extra line breaks or indentation errors. Use `nano docker_dli_run.sh` to verify or correct formatting.

**3.** Make the script executable:
```bash
chmod +x docker_dli_run.sh
```
**4.** Run the container:
```bash
./docker_dli_run.sh
```

> 👀 When you see "Unable to find image `'nvcr.io/nvidia/dli/dli-nano-ai:v2.0.3-r36.3.0'` locally" after running the container, please wait a few minutes for the initial installation to start. This is NOT an error!


> ⚠️ If you get an `invalid reference format` error, check your quotes and slashes, especially in step 4.2.

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
**Compiled by:** Christopher Benjamin Jr.       
**With advisory support from:** ChatGPT (OpenAI)
