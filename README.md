# ComfyUI Docker

A Docker image for running ComfyUI with CUDA support, pre-configured with essential custom nodes and dependencies.

## Overview

This repository contains a Dockerfile to build a production-ready ComfyUI image with:
- **Base**: CUDA 12.6 + cuDNN 9 + Ubuntu 24.04
- **Target**: NVIDIA H20 / Driver 565+
- **ComfyUI Version**: v0.7.0 (pinned)
- **PyTorch**: 2.2.2 with CUDA 12.1 support

## Features

- Pre-installed ComfyUI core and dependencies
- Custom nodes included:
  - ComfyUI-AnimateDiff-Evolved
  - ComfyUI-VideoHelperSuite
  - ComfyUI-Advanced-ControlNet
  - comfyui_controlnet_aux
- Optimized for offline runtime (all dependencies bundled)
- GPU acceleration support via NVIDIA CUDA

## Prerequisites

- Docker installed and running
- NVIDIA Docker runtime (nvidia-docker2) for GPU support
- NVIDIA GPU with driver version 565+ (for H20 compatibility)
- CUDA 12.6+ compatible GPU

## Building the Image

```bash
# Build the image
docker build -t comfyui:latest -f docker/Dockerfile .

# Or specify a custom tag
docker build -t comfyui:v0.7.0 -f docker/Dockerfile .
```

## Running the Container

### Basic Usage (CPU only)

```bash
docker run -d \
  --name comfyui \
  -p 8188:8188 \
  comfyui:latest
```

### With GPU Support

```bash
docker run -d \
  --name comfyui \
  --gpus all \
  -p 8188:8188 \
  comfyui:latest
```

### With Volume Mounts (for persistent data)

```bash
# Create directories for models and outputs
mkdir -p ./models ./outputs ./input

# Run with volume mounts
docker run -d \
  --name comfyui \
  --gpus all \
  -p 8188:8188 \
  -v $(pwd)/models:/opt/ComfyUI/models \
  -v $(pwd)/outputs:/opt/ComfyUI/output \
  -v $(pwd)/input:/opt/ComfyUI/input \
  comfyui:latest
```

## Accessing ComfyUI

Once the container is running, access ComfyUI at:
- **URL**: http://localhost:8188

## Directory Structure

The container uses the following directory structure:
- `/opt/ComfyUI` - Main ComfyUI installation
- `/opt/ComfyUI/models` - Model files (mount this for persistence)
- `/opt/ComfyUI/output` - Generated outputs (mount this for persistence)
- `/opt/ComfyUI/input` - Input files (mount this for persistence)
- `/opt/ComfyUI/custom_nodes` - Custom nodes directory

## Customization

### Adding Custom Nodes

To add custom nodes, you can either:
1. Modify the Dockerfile to clone additional repositories
2. Mount a custom nodes directory as a volume
3. Use docker exec to install nodes after container creation

### Environment Variables

You can customize ComfyUI behavior with environment variables:

```bash
docker run -d \
  --name comfyui \
  --gpus all \
  -p 8188:8188 \
  -e COMFYUI_PORT=8188 \
  comfyui:latest
```

## Troubleshooting

### GPU Not Detected

Ensure NVIDIA Docker runtime is installed:
```bash
# Check if nvidia-docker2 is installed
docker run --rm --gpus all nvidia/cuda:12.6.3-base-ubuntu24.04 nvidia-smi
```

### Port Already in Use

Change the host port mapping:
```bash
docker run -d --name comfyui --gpus all -p 8189:8188 comfyui:latest
```

### Out of Memory

Adjust Docker memory limits or use a smaller model.

## Maintenance

### Updating the Image

To update ComfyUI or dependencies:
1. Modify the Dockerfile (e.g., change ComfyUI version)
2. Rebuild the image
3. Stop and remove the old container
4. Start a new container with the updated image

### Viewing Logs

```bash
docker logs comfyui
docker logs -f comfyui  # Follow logs
```

### Stopping and Removing

```bash
docker stop comfyui
docker rm comfyui
```

## License

This Docker image is based on ComfyUI, which is licensed under the Apache License 2.0.
Please refer to the original ComfyUI repository for license details.

## References

- [ComfyUI GitHub](https://github.com/comfyanonymous/ComfyUI)
- [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker)
- [PyTorch](https://pytorch.org/)

