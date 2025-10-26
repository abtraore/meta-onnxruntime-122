# meta-onnxruntime-122

ONNX Runtime 1.22.0 with VSI NPU acceleration for NXP i.MX8M Plus

## Features

- ✅ ONNX Runtime 1.22.0 (upgraded from 1.17.1)
- ✅ VSI NPU hardware acceleration support
- ✅ Python bindings included
- ✅ Optimized for i.MX8M Plus (Cortex-A53)

## Quick Installation

### Prerequisites

- Yocto Scarthgap
- NXP i.MX BSP 6.6.52-2.2.0

### Installation Steps

```bash
# 1. Initialize NXP BSP
mkdir ~/imx-yocto && cd ~/imx-yocto
repo init -u https://github.com/nxp-imx/imx-manifest \
    -b imx-linux-scarthgap \
    -m imx-6.6.52-2.2.0.xml

# 2. Add this layer via local manifest
mkdir -p .repo/local_manifests
cat > .repo/local_manifests/onnxruntime.xml << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<manifest>
  <remote name="github" fetch="https://github.com"/>
  <project remote="github" 
           name="abtraore/meta-onnxruntime-122" 
           path="sources/meta-onnxruntime-122" 
           revision="main"/>
</manifest>
EOF

# 3. Sync all sources
repo sync

# 4. Setup build environment
DISTRO=fsl-imx-xwayland MACHINE=imx8mpsolidrun \
    source setup-environment build

# 5. Add layer and configure
bitbake-layers add-layer ../sources/meta-onnxruntime-122
echo 'PREFERRED_VERSION_onnxruntime = "1.22.0"' >> conf/local.conf

# 6. Build image
bitbake imx-image-full-dev
```

## Usage on Target

```python
import onnxruntime as ort

# Verify installation
print("Version:", ort.__version__)  # Should show 1.22.0
print("Providers:", ort.get_available_providers())
# Should show: ['VsiNpuExecutionProvider', 'CPUExecutionProvider']

# Run inference with NPU acceleration
session = ort.InferenceSession(
    "your_model.onnx",
    providers=['VsiNpuExecutionProvider']
)
```

## Tested On

- SolidRun HummingBoard 8P Edge AI
- NXP i.MX8M Plus EVK
