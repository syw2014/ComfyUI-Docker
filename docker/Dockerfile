# =========================================================
# Base: CUDA 12.6 + cuDNN 9 + Ubuntu 24.04
# Target: NVIDIA H20 / Driver 565+
# =========================================================
# =========================================================
# Base: CUDA 12.6 + cuDNN 9 + Ubuntu 24.04 (Python 3.12)
# =========================================================
FROM nvidia/cuda:12.6.3-cudnn-runtime-ubuntu24.04

ENV DEBIAN_FRONTEND=noninteractive \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /opt

# 1. 安装系统依赖
RUN apt-get update && apt-get install -y --no-install-recommends \
    python3 \
    python3-pip \
    python3-venv \
    git \
    ffmpeg \
    libgl1 \
    libglib2.0-0 \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# 2. 创建并激活虚拟环境 (解决 PEP 668 报错)
RUN python3 -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

# 3. 安装 PyTorch (这是关键修正点)
# 针对 Python 3.12，必须使用 2.4+ 版本，这里选最稳的 2.5.1
RUN pip install --upgrade pip && \
    pip install --no-cache-dir \
    torch==2.5.1 \
    torchvision==0.20.1 \
    --index-url https://download.pytorch.org/whl/cu124

# 4. 获取 ComfyUI 源码 (v0.7.0)
RUN git clone https://github.com/comfyanonymous/ComfyUI.git /opt/ComfyUI \
    && cd /opt/ComfyUI \
    && git checkout v0.7.0

WORKDIR /opt/ComfyUI

# 5. 安装核心依赖
RUN pip install --no-cache-dir -r requirements.txt

# 6. 安装常用插件
RUN cd custom_nodes && \
    git clone https://github.com/Kosinkadink/ComfyUI-AnimateDiff-Evolved.git && \
    git clone https://github.com/Kosinkadink/ComfyUI-VideoHelperSuite.git && \
    git clone https://github.com/Kosinkadink/ComfyUI-Advanced-ControlNet.git && \
    git clone https://github.com/Fannovel16/comfyui_controlnet_aux.git

# 7. 插件依赖安装 (即使部分失败也不影响主程序构建)
RUN pip install --no-cache-dir -r custom_nodes/comfyui_controlnet_aux/requirements.txt || true

# 8. 运行时配置
EXPOSE 8188
CMD ["python", "main.py", "--listen", "0.0.0.0"]