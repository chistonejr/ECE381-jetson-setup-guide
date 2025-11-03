# Lab 4 – Stable Diffusion on Jetson (WebUI)

This lab walks you through launching **Stable Diffusion WebUI** on NVIDIA Jetson using `jetson-containers`.  
It reflects a known-good flow tested on JetPack 6.2 / CUDA 12.6, including a container tag that worked reliably in our setup.

---
## 🧠 Prerequisites

- **JetPack 6.2 / CUDA 12.6** on Jetson  
- **Docker** & **jetson-containers** installed  
- Internet access (first run will fetch dependencies/models as needed)
- (Optional) A mounted models directory at `/data/models` in the container

> **Note:** The `autotag` for `stable-diffusion-webui` may resolve to a tag with issues on some setups.  
> Below we pin a **known-good tag**.

## 📂 Step 0 - Go to Documents
```bash
cd Documents
````

## 🚀 Step 1 – Clone and set up `jetson-containers`
⚠️ Please note that this setup should have been done in the previous [Lab 3](https://github.com/chistonejr/ECE381-jetson-setup-guide/blob/main/Lab%203.md))
```bash
git clone https://github.com/dusty-nv/jetson-containers
bash jetson-containers/install.sh
````
---
## 🧱 Step 2 – Start the Stable Diffusion container

Use a tested tag (worked reliably in our environment):
```bash
jetson-containers run --workdir /opt/stable-diffusion-webui \
  daivdl487/stable-diffusion-webui:r36.4.3

````

You’ll know you’re inside the container when your prompt looks like:
`root@ubuntu:/opt/stable-diffusion-webui#`

> **Why this image?**
> `autotag stable-diffusion-webui` pulled `r36.4.0` for us, which had issues.
> `daivdl487/stable-diffusion-webui:r36.4.3` launched cleanly.

## 🌐 Step 3 – Launch the WebUI
From inside the container:
```bash
cd /opt/stable-diffusion-webui
python3 launch.py \
  --data=/data/models/stable-diffusion \
  --enable-insecure-extension-access \
  --xformers \
  --listen \
  --port=7860
  ````
You should see logs ending with something like:

`Running on local URL:  http://0.0.0.0:7860
Model loaded in XX.Xs ...`

A “blinking” terminal is **normal**—the server is running and waiting for a browser connection.

## 🔎 Step 4 – Open the WebUI in a browser
- If you’re on the Jetson desktop: open http://localhost:7860

- If from another device on the same network (Headless Mode):

1. Get the Jetson’s IP:
```bash
hostname -I
````

  2. Open `http://<jetson-ip>:7860` in your browser.

## 🛠 Troubleshooting

**Check the server inside the container**
```bash
curl -s http://127.0.0.1:7860 | head
ss -ltnp | grep 7860
````

If you see HTML from `curl` and a listener on `0.0.0.0:7860`, the server is up.

**Can’t reach from another machine?**

- Ensure you started the container with `--network host` (see Step 2).
- Try adding explicit binding:
```bash
python3 launch.py ... --server-name 0.0.0.0 --port=7860
````

**Non-fatal extension messages**

- Lines like:

  - `CivitAI Browser+: Basemodel fetch error ...`

  - `config_states ... "created_at" does not exist`

  - Gradio `DeprecationWarning`

 - These are usually harmless and don’t block the UI.

**Nothing seems to happen / empty page**

- Wait 20–60 seconds on first load (model init).

- Add memory-friendly flags (see below).

- Confirm you have a model at `/data/models/stable-diffusion/models/Stable-diffusion/...` or add one in the UI after it loads.

## ⚡ Optional Performance Flags

If you experience slowness or memory pressure, add one or more of these to the `launch.py` command:
```bash
--medvram
--opt-sdp-no-mem
--no-half-vae
--disable-extension stable-diffusion-webui-tensorrt
````

Example combined run:
```bash
python3 launch.py \
  --data=/data/models/stable-diffusion \
  --enable-insecure-extension-access \
  --xformers \
  --listen --server-name 0.0.0.0 --port=7860 \
  --medvram --opt-sdp-no-mem --no-half-vae \
  --disable-extension stable-diffusion-webui-tensorrt
````

## 🤝 Credits

Prepared by Christopher Benjamin
with assistance from ChatGPT (GPT-5), documenting a practical, working flow for
Lab 4 – Stable Diffusion on Jetson.
