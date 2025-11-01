# Lab 3 – Visual Transformers using NANOOWL / OWL-ViT

This lab demonstrates how to run visual transformer inference on Jetson devices using **NANOOWL** (OWL-ViT implementation) inside NVIDIA’s `jetson-containers` environment.

---

## 🧠 Prerequisites
- **JetPack 6.2 / CUDA 12.6** environment  
- **Camera connected** and accessible via `/dev/video*`
- Internet access (for cloning and installing dependencies)

---

## 🚀 Step 1: Clone and set up `jetson-containers`

```bash
git clone https://github.com/dusty-nv/jetson-containers
bash jetson-containers/install.sh

````
## 📷 Step 2: Verify your camera is initialized

```bash
ls /dev/video*
````

You should see one or more devices listed, e.g. /dev/video0, /dev/video1, etc.


## 🧱 Step 3: Start the NANOOWL Docker container

Launch the proper Jetson container environment.

### Auto tag detection:
```bash
jetson-containers run --workdir /opt/nanoowl $(autotag nanoowl)
````

You’ll know you’re inside the container when your prompt looks like this:

`root@ubuntu:/opt/nanoowl#`


## 🧩 Step 4: Install the required module (aiohttp)

### 🔧 This step uses the updated Jetson PyPI mirror.
 #### Note: ⚠️The original .dev index is currently offline.
```bash
pip install --no-cache-dir \
  --index-url https://pypi.org/simple \
  --extra-index-url https://pypi.jetson-ai-lab.io/jp6/cu126 \
  aiohttp
````

💡 Note: If you restart or exit the container later, re-run this command before launching the demo since containers reset temporary installs.

## 🌳 Step 5: Launch the demo
Run this command
```bash
cd examples/tree_demo
python3 tree_demo.py --camera 0 --resolution 640x480 \
  ../../data/owl_image_encoder_patch32.engine
````

⚠️ If you encounter an error about a missing engine file, rebuild it:
```bash
cd /opt/nanoowl
python3 -m nanoowl.build_image_encoder_engine data/owl_image_encoder_patch32.engine
cd examples/tree_demo
python3 tree_demo.py --camera 0 --resolution 640x480 \
  ../../data/owl_image_encoder_patch32.engine
````
## 🌐 Step 6: Open the demo link

After launching the demo, a link will appear in the terminal, such as:

`http://<ip-address>:7860`


Hold **Ctrl + Click** to open it, or copy and paste the URL into a browser on the same network.


## ✍️ Step 7: Create your prompts

Follow your lab instruction slides to create the prompt inputs for the NANOOWL / OWL-ViT model demo.

## 💡 Info Notes
| Option | Description | Example |
| :--- | :--- | :--- |
| `--camera` | Specifies the camera index (corresponds to `/dev/video*`) when multiple cameras are connected | `--camera 1` |
| `--resolution` | Specifies the camera resolution in the format `{width}x{height}` | `--resolution 640x480` |
| Engine rebuild command | Use this if the engine path or file isn’t found | `python3 -m nanoowl.build_image_encoder_engine data/owl_image_encoder_patch32.engine` |

## ⚙️ Troubleshooting
| Error  | Solution |
| :--- | :--- |
| ModuleNotFoundError: No module named `aiohttp` | Re-run Step 4 inside your current container session. |
|bash: nanoowl: command not found| Launch the container with jetson-containers run ... again (you’re likely on the host shell, not inside Docker).|
|pip install timeout|Make sure your index URLs use .io instead of .dev|
|No camera feed in browser|Try another camera index: 'python3 tree_demo.py --camera 1 --resolution 640x480 ../../data/owl_image_encoder_patch32.engine'|

## 🤝 Credits

This lab guide was prepared by Christopher Benjamin Jr.
in collaboration with ChatGPT (GPT-5) to document and refine the setup steps for
Lab 3 – Visual Transformers using NANOOWL / OWL-ViT.
