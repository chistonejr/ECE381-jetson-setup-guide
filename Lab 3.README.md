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
```
## 📷 Step 2: Verify your camera is initialized
```bash
ls /dev/video*

You should see one or more devices listed, e.g. /dev/video0, /dev/video1, etc.

## 🧩 Step 3: Install the required module (aiohttp)

🔧 This step uses the updated Jetson PyPI mirror.
The original .dev index is currently offline.

pip install --no-cache-dir \
  --index-url https://pypi.org/simple \
  --extra-index-url https://pypi.jetson-ai-lab.io/jp6/cu126 \
  aiohttp

🌳 Step 4: Launch the demo

cd examples/tree_demo
python3 tree_demo.py --camera 0 --resolution 640x480 \
  ../../data/owl_image_encoder_patch32.engine
If you encounter an error when running the above, try rebuilding the engine:
python3 -m nanoowl.build_image_encoder_engine \
  data/owl_image_encoder_patch32.engine

🌐 Step 5: Open the demo link

After launching the demo, a link will appear in the terminal (for example):

http://<ip-address>:7860
Hold Ctrl + Click to open it, or copy and paste it into a browser on the same network.

✍️ Step 6: Create your prompts
Follow your lab instruction slides to create the prompt inputs for the NANOOWL / OWL-ViT model demo.

| Option                 | Description                                                                                   | Example                                                                               |
| :--------------------- | :-------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------ |
| `--camera`             | Specifies the camera index (corresponds to `/dev/video*`) when multiple cameras are connected | `--camera 1`                                                                          |
| `--resolution`         | Specifies the camera resolution in the format `{width}x{height}`                              | `--resolution 640x480`                                                                |
| Engine rebuild command | Use this if the engine path or file isn’t found                                               | `python3 -m nanoowl.build_image_encoder_engine data/owl_image_encoder_patch32.engine` |

🤝 Credits

This lab guide was prepared by Christopher Benjamin
in collaboration with ChatGPT (GPT-5) to document and refine the setup steps for
Lab 3 – Visual Transformers using NANOOWL / OWL-ViT.
