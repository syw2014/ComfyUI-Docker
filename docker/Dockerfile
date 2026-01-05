# =========================================================
# Base: CUDA 12.6 + cuDNN 9 + Ubuntu 24.04
# Target: NVIDIA H20 / Driver 565+
# =========================================================
FROM nvidia/cuda:12.6.3-cudnn-runtime-ubuntu24.04

ENV DEBIAN_FRONTEND=noninteractive
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

WORKDIR /opt

# =========================================================
# 1. System dependencies
# (Build-time only, runtime is fully offline)
# =========================================================
RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    python3-venv \
    git \
    ffmpeg \
    libgl1 \
    libglib2.0-0 \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# =========================================================
# 2. Upgrade pip
# =========================================================
RUN python3 -m pip install --upgrade pip

# =========================================================
# 3. PyTorch (stable, production-proven)
# CUDA 12.1 wheels are compatible with CUDA 12.7 driver
# =========================================================
RUN pip install \
    torch==2.2.2 \
    torchvision==0.17.2 \
    --index-url https://download.pytorch.org/whl/cu121

# =========================================================
# 4. ComfyUI (PINNED to v0.7.0)
# =========================================================
RUN git clone https://github.com/comfyanonymous/ComfyUI.git /opt/ComfyUI \
 && cd /opt/ComfyUI \
 && git checkout v0.7.0

WORKDIR /opt/ComfyUI

# =========================================================
# 5. ComfyUI core dependencies
# =========================================================
RUN pip install -r requirements.txt

# =========================================================
# 6. Custom nodes (offline-ready, common production set)
# NOTE: not pinned yet, but acceptable for v0.1
# =========================================================
WORKDIR /opt/ComfyUI/custom_nodes

RUN git clone https://github.com/Kosinkadink/ComfyUI-AnimateDiff-Evolved.git \
 && git clone https://github.com/Kosinkadink/ComfyUI-VideoHelperSuite.git \
 && git clone https://github.com/Kosinkadink/ComfyUI-Advanced-ControlNet.git \
 && git clone https://github.com/Fannovel16/comfyui_controlnet_aux.git

# =========================================================
# 7. Custom nodes dependencies (best-effort)
# =========================================================
WORKDIR /opt/ComfyUI
RUN pip install -r custom_nodes/comfyui_controlnet_aux/requirements.txt || true

# =========================================================
# 8. Runtime
# =========================================================
EXPOSE 8188
CMD ["python3", "main.py", "--listen", "0.0.0.0"]
